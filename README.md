1. Install docker toolbox from https://www.docker.com/products/docker-toolbox
2. Run the docker quickstart terminal
3. Change default vm settings (Only needed once)
  1. Remove the default vm

     ```
     docker-machine rm default
     ```
  2. Re-create the default vm
     * Choose the number of cpus: **--virtualbox-cpu-count**

         Since this is based on the hardware, there is no "correct" amount. For
         this example we'll use two.


     * Choose the amount of RAM: **--virtualbox-memory**

         This is also based on the host hardware. However, choose at least
         4GB. In this example, we'll choose 8GB.


     * Choose the amount of disk space: **--virtualbox-disk-size**

         It is recommended that this be at least 50GB since building generates
         a lot* of output. In this example we'll choose 50GB.
     ```
     docker-machine create -d virtualbox --virtualbox-cpu-count=2 \
         --virtualbox-memory=8192 --virtualbox-disk-size=50000 default
     ```
4. Create the volume

   In the terminal that appears type the following command to create a volume.
   ```
   docker volume create --name myvolume
   docker run -it --rm -v myvolume:/workdir busybox chown -R 1000:1000 /workdir
   ```
5. Create and run a samba container

   This container is what will allow you to see the files in the volume.
   ```
   docker create -p 127.0.0.1:137-139:137-139 -p 127.0.0.1:445:445 \
       --name samba -v myvolume:/workdir crops/samba
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
   docker run --rm -it -v myvolume:/workdir crops/poky \
       --workdir=/workdir
   ```
