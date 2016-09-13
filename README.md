1. Install docker for mac from https://docs.docker.com/docker-for-mac/
2. Run the docker app
3. Start a terminal
4. Create the volume

   In the terminal that appears type the following command to create a volume.
   ```
   docker volume create --name myvolume
   docker run -it --rm -v myvolume:/workdir busybox chown -R 1000:1000 /workdir
   ```
5. Create and run a samba container

   This container is what will allow you to see the files in the volume.
   ```
   docker create -t -p 137-139:137-139 -p 445:445 --name samba -v myvolume:/workdir crops/samba
   ```
6. Start the samba container

   ```
   docker start samba
   ```
7. Get the ip address used to talk to samba and open /workdir

   ```
   docker-machine ip
   ```
   The result of this command is the address you will use to see the workdir.
   In this example let's say the command returned 192.168.99.100. Now, to see
   the *workdir* open the file browser in windows (win+e) and type
   ```
   \\192.168.99.100\workdir
   ```
8. Start a container to do work.

   You can now use any of the other containers such as the poky
   container. The important part to remember is that the volume created above,
   is the one to use when specifying the workdir. For example, to run the
   poky container:
   ```
   docker run --rm -it -v myvolume:/workdir crops/poky --workdir=/workdir
   ```
