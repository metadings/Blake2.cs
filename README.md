﻿
**﻿﻿Blake2B.cs source code package - C# implementation**

```
Written in 2012 by Samuel Neves <sneves@dei.uc.pt>
Written in 2012 by Christian Winnerlein <codesinchaos@gmail.com>
Written in 2016 by Uli Riehm <metadings@live.de>

To the extent possible under law, the author(s) have dedicated all copyright
and related and neighboring rights to this software to the public domain
worldwide. This software is distributed without any warranty.

You should have received a copy of the CC0 Public Domain Dedication along with
this software. If not, see <http://creativecommons.org/publicdomain/zero/1.0/>.
```

Ask questions on [stackoverflow](http://stackoverflow.com/questions/tagged/c%23+blake2) using tags `C#``Blake2` !

**Usage 1**

Have "one" Blake2B hash value.

```csharp
using Blake2;
using System;
using System.Text;


	string text = "HHHHAAAALLLLOOOOWWWWEEEELLLLTTTT";
	byte[] bytes = Encoding.UTF8.GetBytes(text);
	byte[] value;

	using (var hash = new Blake2B()) value = hash.ComputeHash(bytes);

	foreach (byte v in value) Console.Write("{0:x2}", v);
	Console.WriteLine();

```

**Usage 2**

Have "many" Blake2B hash values.

```csharp
using Blake2;
using System;
using System.Text;


	byte[] textBytes = Encoding.UTF8.GetBytes("HHHHAAAALLLLOOOOWWWWEEEELLLLTTTT");

	byte[] hashSource = new byte[sizeof(UInt64) + textBytes.Length];
	Buffer.BlockCopy(textBytes, 0, hashSource, sizeof(UInt64), textBytes.Length);

	var hashValue = new byte[64];

	UInt64 i = 0; // threadI;

	using (var hash = new Blake2B())
	{
		do
		{
			Blake2B.UInt64ToBytes(i, hashSource, 0);

			hash.Compute(hashValue, hashSource);

			// if (Quersumme(i + 1) == 1) Console.WriteLine ...

		} while (0 < ++i && i < UInt32.MaxValue);

		/* DANGER: Your program will run FOR HOURS !

		   Intel Pentium Dual CPU E2160 @ 1.80GHz x2
		   => 1.012.321.448 Blake2B in 1131s or 18,85m
		   => 895.068 Blake2B/s or 53.704.056 Blake2B/m

		1. You should use `emacs` as your editor, using file `~/.emacs.d/init.el`
		   and (global-auto-revert-mode 1), to have a real-time view of your program.

		2. You run this program using a pipe, on GNU/Linux and on Windows...

		   $ mono ./YourProgram.exe > './YourPrograms output.txt'

		2. You should also try this using new System.Threading.Thread's:

		} while (threadI < (i += threadC)); /**/
	}

	foreach (byte v in hashValue) Console.Write("{0:x2}", v);
	Console.WriteLine();

```

**Example 1**

```
~/Blake2B.cs/bin/Debug $ echo -n HHHHAAAALLLLOOOOWWWWEEEELLLLTTTT > ./Hallo.txt

~/Blake2B.cs/bin/Debug $ hexdump ./Hallo.txt 
0000000 4848 4848 4141 4141 4c4c 4c4c 4f4f 4f4f
0000010 5757 5757 4545 4545 4c4c 4c4c 5454 5454
0000020

~/Blake2B.cs/bin/Debug $ mono ./Blake2B.exe --in=./Hallo.txt
bbc9e82dbf9a8897a5ec2f6836c381dbe27ac0b8ecd9912afa67459ef9474d70a52bf24ad5dcf29dbb8004d19a387b6516cc47ffae99d59d52efc013456c6b48
```

