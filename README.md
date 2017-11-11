# call-trace
This is a tool for generating call information live from a running process. It uses instrumentation
functions supported by gcc `-finstrument-functions`. By using lib bfd it translates addresses to
human readable information. So you will need to to link with `-lbfd`

## Usage
Add `-lbfd` to your library flags, and `-finstrument-functions` to your C-flags.

## Example
If you have a program like
```c
#include <stdio.h>
#include <stdlib.h>

void bar()
{
	fprintf(stderr, "In bar!\n");
}

void foo()
{
	bar();
}

int main(void)
{
	fprintf(stderr, "Starting program\n");

	foo();

	return EXIT_SUCCESS;
}
```

and then using a Makefile like
```make
CFLAGS += -g
CFLAGS += -finstrument-functions
LDLIBS += -lbfd

main: main.o call-trace.o
```
Compile and run
```
$ make && ./main
-trace- calling [0x402b91 main at main.c:15] from [0x7f2b6656388a ?? ??:0]
Starting program
-trace- calling [0x402b5e foo at main.c:10] from [0x402bd3 main at main.c:20]
-trace- calling [0x402b17 bar at main.c:5] from [0x402b7d foo at main.c:11]
In bar!
```

