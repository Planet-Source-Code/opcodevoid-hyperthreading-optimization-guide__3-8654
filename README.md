<div align="center">

## HyperThreading Optimization Guide


</div>

### Description

Learn to Optimize for the HyperThreading Technolgy. This tutorial shows you pitfalls that make HT slower, and Tips that make it really Fast. It also includes General SMP programming So your application can take advantage of MultiProcessor systems
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[OpcodeVoid](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/opcodevoid.md)
**Level**          |Beginner
**User Rating**    |4.4 (40 globes from 9 users)
**Compatibility**  |C, C\+\+ \(general\), Microsoft Visual C\+\+, Borland C\+\+, UNIX C\+\+
**Category**       |[Documents](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/documents__3-27.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/opcodevoid-hyperthreading-optimization-guide__3-8654/archive/master.zip)





### Source Code

```
To get the formatted article go here
<p>
http://www.eliteproxy.com/plugins/viewpost.php?s=yes&pid=99
<p>
<p>
<P>
<P>
<P><P><P><P>
							Hyper threading optimization Guide
By opcodevoid:
Disclaimer: This tutorial is © and can be redistributed as long as a link to be website is given. You can not modify this tutorial in anyway
Note: This guide is for Regular SMP processors to; your application needs to be multiple
CPU aware even if you don’t plan on using it, because your customers might, Plus its good to learn how to write c++ code for Intel new Hyper threading/Hyperthreading technology
For more information join us on are irc servers at irc.crackingislife.com channel #opcodevoid
 Or our online active web chat http://www.crackingislife.com/cgi-bin/cgiirc/irc.cgi
Contents
1.0	What is hyper threading
1.1	Introduction to SMP programming
1.2	How to take advantage of SMP
1.3	How to take advantage of Hyper threading
1.4 Benchmarks
1.0	What is hyper threading
        It was my birthday and as a present I got to choose a new computer; I had a choice between a
DVD writer +
40 Gigabyte HD, +
256 mb of ram +
Monitor +
mouse keyboard +
Video card +
Case
or
brand new Intel 2.8ghz Hyper threading pc +
case +
video card
  Of course I choose the hyper threading cpu even though I didn’t even know what it was
It just sounded so freaking awesome and I had to get it, of course to my surprise Ht technology wasn’t no where near as good as two separate CPU’s like a normal SMP machine. I have actually got 51-52% gain using HT technology in algorithm design
For HT, but no where near a 100% boost a second CPU can give.
 For starter HT is not two processors but can fool the system into thinking it is which is good since Intel Processors spend most of there time sleeping , Intel found a way to execute different parts of there processors at once! in other words two threads running at exactly the same time.
 Of course if two threads compete for the same parts of the processor there will actually be a slowdown. Yes that’s right, Hyper threading can slow thing down but usually doesn’t. I found out that if I execute two threads on the same virtual CPU on a HT machine it’s a 100% speed degrade
 The best thing to do is send one thread for your sound system and one thread for your 3D processing try not to execute multiple threads on the same function I haven’t had amazing result doing this.
 I actually tried using mmx,ss2e, fpu, regular register math, but found no noticeable speed increase or decrease.
 I only found a true Extreme 30-40% slowdown when I executed two threads on the same processor or virtual processor
 So in conclusion hyper threading allows two threads to use two different parts
Of the CPU at once which usually leads to a 30-40% gain which is amazing, of course you can’t just get this gain by creating two threads, you must assign them to each processor which is really simple under windows and which we will be learning.
 A real quick note, you must make sure your threads don’t both use the same CPU or virtual cpu or you will have a huge performance degrade, 100% to that extent, 1.5ms to 3000ms to be exact which is just scary, that’s why we will learn the ins and out of SMP programming.
1.1 Introduction to SMP programming
 SMP simple means multiple processors; threads to application are always presented as executing at the exact same time in parallel. Of course we know this not to be true especially on a single processor machine; we know that the kernel just allocates each thread a time slice.
 What I’m trying to say is that threads will actually slow the system down unless there
Executing on different processors. The best thing to do is queue the threads and dispatch them to every single processor on the system in HT case its two, But my algorithms are pretty generic and will work for up two 32 processors (the maximum).
 Windows Provides two calls we need to use
GetProcessAffinityMask:
This returns a 32 bit value where each bit represents a processor.
For example if it return 3, that means two processors, because 3 in binary is
0000:0011 each set bit equals one processor so that’s two.
SetThreadAffinityMask:
This allows you to choose which processor a thread executes on, the first
thing you do is call GetProcessAffinityMask get the cpu count and mask, and Assign
a thread of your choice to your processor of choice.
GetProcessAffinityMask has the following parameters
 HANDLE hProcess,
 PDWORD_PTR lpProcessAffinityMask,
 PDWORD_PTR lpSystemAffinityMask
 hProcess: The Handle of the process you want to get the affinity from
 lpProcessAffinityMask: The 32bit value where each bit represents a processor which
in turn mean which cpu the processor can use
lpSystemAffinityMask: The 32bit value where each bit represents a processor which
in turn means which CPU the system can use
A example of the using it would be
#define EXTRACT_BITS_RL(the_val, bits_start, bits_len) ((the_val >> (bits_start - 1)) & ((1 << bits_len) - 1))
size_t get_cpu_count()
{
	DWORD pmask,smask;
	GetProcessAffinityMask(OpenProcess(PROCESS_ALL_ACCESS,false,GetCurrentProcessId()),&pmask,&smask);
	int bitarray[32];
	size_t count=0;
	for(size_t i =1;i<33;i++)
	{
		bitarray[i] =EXTRACT_BITS_RL(pmask,i,1);
		if(bitarray[i]==0)
			return count;
		count++;
	}
	return 32; //maxium processors
}
 My algorithm for getting the number of cpus using GetProcessAffinityMask works like this.
1.	Get the mask
2.	2. Loop though all 32 bits testing for a zero bit
3.	If a 0 bit is found return the count(number of cpus)
4.	If not 0 increase count and return to step 2
EXTRACT_BITS_RL is a amazing macro(not made by me) that will extra a bit at the position a specify.
 You might be saying you aren’t returning the bit mask , you’re just using it to get the process count. You’re correct you see we already know that if we want to execute on processor 3 we need a mask of 4 which is 100 in binary, we just need to know if processor thread actually exist.
 Of course I not going to leave you in the dark trying to figure out which bits to
set to specify your processor, I have a great function you can use for that
size_t get_cpu_mask(size_t cpu_number)
{
  if(cpu_number==1) return 1;
  if(cpu_number==2) return 2;
  return pow(2,cpu_number-1);
}
  If i send get_cpu_mask(3) , it will return 4 which is 100 which specifies processor 3
 Now that we know are Affinity Mask all we have to do is use SetThreadAffinityMask
To force a thread to use a processor of my choice, which is rather simple
SetThreadAffinityMask(thead_handle,get_cpu_mask(8));
This would force thead_handle to use CPU 8
That’s it, that’s all there is to programming for a multi Cpu machine.
1.4 How to take Advantage of Hyper Threading
  The first thing we must realize is hyperthreading is not Two CPU’s and will not
scale as well as two cpus. The second thing to realize is that it can slow thing down if we execute two threads that use the same parts of the processor(not by much). So I have the following guide lines
1. Use Different parts of the processor like register math such as add eax,1 and fpu math
together.
2. Use Intel compilers which comes with HT optimization, If i get my hands on the compiler I will see how it actually works but until than
1.4	Bench Marks
 Here is a program I made that Runs a serious of test and records the total Mill seconds
used. I tested it with Hyper threading and without Hyper threading
HT benchmark test
1547:
(Y) Different Algorithm
(Y) Different Processors
(Y) Two Threads
(Y) HT Enabled(Yes)
Conclusion: I tested it with two different algorithms, two different threads, and HT enabled. I saw no significant different when the algorithms where different.
2796 :
(Y) Different Algorithm
(N) Different Processor
(N) Two Threads
(Y) HT Enabled(yes)
Conclusion: 1 Thread hitting two routine is way slow, HT technology has a speed boost.
3125 :
(Y) Different Algorithm
(N) Different Processor
(Y) Two Threads
(Y) HT Enable(yes)
Conclusion: Ooch, Here is HT weakness Two threads setup to execute on the same virtual CPU, 1.5 seconds to 3 seconds a 100% decrease in speed
2800:
(Y) Different Algorithm
(N) Different Processors
(Y) Two Threads Yes
(N) HT Enabled
Conclusion: Wow 2800, on a Single CPU(HT is Disable now),
2904:
(Y) Different Algorithm
(N) Different Processors
(N) Two Threads
(N) NT Enable
Conclusion: Using 1 thread slowed things down on a hyper threading
processor(HT disabled).
Its interesting how a simple SetThreadAffinityMask can go from 1500ms to 3000ms(100% increase) I suggest you Queue up threads and execute them on different processors for the greatest speed as we saw the biggest slow down when we didn’t properly set the Affinity for the threads.
 The conclusion here is Set those Affinity Mask for different processors if you need 4 threads Queue them and execute them each on different processors.
Here is the algorithm I used for the bench mark. This one creates two threads, and uses two different algorithms.
#include "windows.h"
#include "iostream.h"
#include "math.h"
#define EXTRACT_BITS_RL(the_val, bits_start, bits_len) ((the_val >> (bits_start - 1)) & ((1 << bits_len) - 1))
#define MODIFY_BIT_RL(the_val, bit_num, bit_val) (bit_val == 0 ? (the_val & (~(1 << (bit_num - 1)))) : (the_val | (1 << (bit_num - 1))))
size_t get_cpu_count()
{
	DWORD pmask,smask;
	GetProcessAffinityMask(OpenProcess(PROCESS_ALL_ACCESS,false,GetCurrentProcessId()),&pmask,&smask);
	int bitarray[32];
	size_t count=0;
	for(size_t i =1;i<33;i++)
	{
		bitarray[i] =EXTRACT_BITS_RL(pmask,i,1);
		if(bitarray[i]==0)
			return count;
		count++;
	}
	return 32; //maxium processors
}
size_t get_cpu_mask(size_t cpu_number)
{
  if(cpu_number==1) return 1;
  if(cpu_number==2) return 2;
  return pow(2,cpu_number-1);
}
void thread1(LPARAM nothing)
{
	float x;
	for(unsigned int i=0;i<10000;i++)
	{
		x+=12;
		x = x - 1 + 2;
		if(nothing!=1)
			thread1(1);
	}
}
void thread2(LPARAM nothing)
{
	unsigned int p;
	for(unsigned int i=0;i<10000;i++)
	{
		p = p + 12;
		p &=p;
		p = p + 12;
		p = p * 12;
		if(nothing!=1)
			thread2(1);
	}
}
int main(int argc, char* argv[])
{
	cout << "Dual cpu Optimization test " << endl;
	DWORD cpu_count = get_cpu_count();
	//create a array to store mask
	DWORD *cpu_mask = (DWORD *)GlobalAlloc(GMEM_FIXED,sizeof(DWORD) * cpu_count);
	for(size_t i = 0;i<cpu_count;i++)
	{
		cpu_mask[i] = get_cpu_mask(i+1);
		cout << "Cpu " << i+1 << " Mask " << cpu_mask[i] << endl;
	}
	DWORD elasp =0;
	DWORD start = GetTickCount();
	DWORD t1,t2;
	HANDLE obj[2];
	obj[0]=CreateThread(NULL,NULL,(LPTHREAD_START_ROUTINE)thread1,NULL,CREATE_SUSPENDED,&t1);
	obj[1]=CreateThread(NULL,NULL,(LPTHREAD_START_ROUTINE)thread2,NULL,CREATE_SUSPENDED,&t2);
	ResumeThread(obj[0]); //resume the threads
	ResumeThread(obj[1]); //resume thread
	WaitForMultipleObjects(1,obj,true,INFINITE);
	elasp = GetTickCount() - start;
	cout << "Time elasp was " << elasp << endl;
	return 0;
}
 It compiles under Visual c++ 6.0 , I also uploaded the file so you can Download it.
```

