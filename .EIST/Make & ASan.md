# Sample
```makefile
# Compiler and flags
CC = gcc
CFLAGS = -Wall -g -fsanitize=address
LDFLAGS = -fsanitize=address

# Target
main: main.o
	$(CC) $(CFLAGS) $(LDFLAGS) -o main main.o

main.o: main.c
	$(CC) $(CFLAGS) -c main.c

clean:
	rm -f *.o main
``` 

>[!info] **Optional** 
>add ` -fsanitize=undefined ` for undefined behavior detection
>add ` -fsanitize=thread` for detecting data races etc.



