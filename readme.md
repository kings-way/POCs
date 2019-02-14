### Exploit for CVE-2019-5736 

## How to

* build image

		cd CVE-2019-5376
		gcc run.c -o run -static
		docker build -t testpoc .


* run

		docker run -it --privileged --name testpoc_instance testpoc
		
		# open another terminal, and run docker exec
		docker exec -it testpoc_instance bash

Notes:   
* original approach from [DragonSector](https://blog.dragonsector.pl/2019/02/cve-2019-5736-escape-from-docker-and.html)  
use a malicious.so(which used by runc) with  malicious entry point (like #!/proc/self/exe) to hijack the execution of runc, and malicious.so starts another process/thread to open '/proc/self/exe' to hold the file descriptor.  Finally, we are able to write to the fd after the runc exits.
> shortcoming: need a malicious entry point program. It's no problem for ```docker run```, but ```docker exec``` has to run that specific program.

* approach used here (also used by the some exploits from others)  
start the malicious container first, and run a process (in a loop) to monitor new processes in /proc. On finding a new one, open  '/proc/{pid}/exe' to hold the file descriptor. Then it's the same like above.
> shortcoming: works on every ```docker exec``` command towards the container, but will not work when using ```docker run``` to start the new container
---- 
