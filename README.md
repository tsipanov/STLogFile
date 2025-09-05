# STLogFile

This is the library that I used to write when I was working on the Windows CE / Pocket PC code. It seems like people find it useful, so I just publish it from the authors repo. 


## USAGE

Include this file into any header that will be included in all othr CPP files.
For Visual C projects with precompiled headers the ideal place would be stdafx.h
You can either insert this file into the project or not, in the latter case 
the log classes won't litter your project workspace. This won't affect
usage of logs.

The primary usage of this code is to have a simple way to output debugging
information into the log file, which could be easiliy turned off or on during 
the compile time.
Example :

```cpp
STLOG_WRITE(_T("saving %d transactions"), nTransactions); 
```

Another STLogFile feature, marker: 

```cpp
void MyFunction()
{
	STLOG_MARKER(_T("MyFunction"));
	if (somethingbad())
		return FALSE;
	if (somethinggood())
		return TRUE;

	return FALSE; 
}
```

If you want to output some binary data (buffer contents) you should use a STLOG_WRITE_DATA macro:

```cpp
  {
  ...
  char buffer[125];
  // .. fill the buffer with data 

  STLOG_WRITE_DATA(buffer, 125);
  }
```

This code will write a >>[MyFunction] line on entering the function and <<[MyFunction] 
on exit wherever the program leaves your function.

To find out the problem of the wrong function execution simply add STLOG_LASTERROR:

```cpp
  HANDLE hFile = CreateFile(....);
  if (INVALID_HANDLE == hFile)
  {
	STLOG_WRITE("Cannot open file");
	STLOG_LASTERROR;
  }
```
This will print the last textual description of error returned by GetLastError() funciton.

And .. profiling. Before describing log file profiling features it would be appropriate 
to say that all measured timing includes the time for file operation, which slightly 
increases the execution time, therefore this method could only help to *estimate* and 
compare execution times to find bottlenecks of your code.

```cpp
  ....
  STLOG_PROFILE_FUNCTION(Calculate());
  ..
```

This code will estimate time needed for function execution. Simple. When the 
logging is turned off this will be transformed to straight call to Calculate().

```cpp
  {
	STLOG_PROFILE_BLOCK;
	.. code here ...

  }
```

  This sample of code will print to log file the time when the execution entered the
  profiled block, when the execution leaves it and the time interval between the two points.
  When your program finishes, this macro will print profiling statistics, like how many 
  times this code has been executed, how much time did it take in total, in average, 
  maximium and minimum timings.


  Sometime you want to have intermediate timings and STLogFile has a solution for this case:
```cpp
  {
	STLOG_PROFILE_BLOCK;
	Phase0();
	STLOG_PROFILE_CHECKPOINT
	Phase1();
	STLOG_PROFILE_CHECKPOINT
	Phase2();
  }
```
  This will print timings between the check points and the time from beginning of the block 
  every time execution reaches any check point.

There are some options, which can be used for fine tuning like where to create log file,
how to create it and others. 
