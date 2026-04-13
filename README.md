# lab-environment-ubuntu-hardening
A hands on Linux lab environment focused on system administration, network configuration, and OS hardening for security testing.
---
The objective of the project is to build a secure linux environment from the ground up via using a virtual machine. Instead of just a standard out of the box install, I focused on system hardening and attack surface reduction to simulate a ready server environment.

### What i used for this project
for the virtual machine we used **[VirtualBox 7.2](https://download.virtualbox.org/virtualbox/7.2.6/VirtualBox-7.2.6a-172322-Win.exe)**, and the linux distro im using is **[Ubuntu 24.04](https://ubuntu.com/download/server#manual-install-tab)**

### Host System Specifications
For context, these are the hardware specs of the host machine running the virtual machine:

* **CPU:** Ryzen 5 5500
* **RAM:** 16gb DDR4
* **Storage:** 1tb m.2
* **OS:** Windows 11 (Host)

## Project walkthrough:
Made a quick youtube demo of the process step by step: https://www.youtube.com/watch?v=lXkfci6QbuI

## Step 1 installing the VM
Network Reset Warning: You will receive a notification regarding a temporary network disconnection. This is normal; VirtualBox is installing the Virtual Network Interface, which allows for network bridging and NAT translation.

<img width="495" height="387" alt="image" src="https://github.com/user-attachments/assets/9b7dbce6-8582-4dab-b35c-b954b8c78155" />

Hit yes. then if this appears

<img width="491" height="388" alt="image" src="https://github.com/user-attachments/assets/2e4f1186-7580-4758-82d5-d85ff660c63c" />

This appears because there is missing dependencies that do automation. but this project focus is on OS hardening so we click yes to keep the environment lightweight. can be later solved if we want python automation.

## Step 2 setting up the VM

<img width="960" height="747" alt="image" src="https://github.com/user-attachments/assets/98403230-f333-40ce-b69e-56720c0620c9" />


Once the installion is complete this will appear, we will click on New because we are creating a new VM. a new pop up will appear where you will fill the VM Name and ISO image. ill name my VM ubuntu and for the ISO image we will put our ubuntu verison that we installed and it should look like this:


<img width="820" height="410" alt="image" src="https://github.com/user-attachments/assets/507e3b50-c820-4fa0-a5c6-d158c11fbfff" />

Click Next. and youll see that you can change your Username and password, as well as OS installation Options. for password make sure you have a proper 8-12 character with at least one number and captial to increase resistance against brute force attacks, once you enter ur password click next.

In this section, we will need to give the VM the needed virtual hardware to work. now this part is depending on the host hardware, since my PC is quite capable ill put the sweet spot

<img width="820" height="409" alt="image" src="https://github.com/user-attachments/assets/4b255371-289e-4cb0-8c5b-b5d4abe7345d" />

4GB of ram gives alot of headroom to run security tools and basic logging without slowing down. 2 CPUs also helps with encryption or any cpu based process. so depending on the certain cpu ima keep this at 2. and for Disk space im keeping it default which is 25gb because the OS takes around 5 to 8 gb, and we will keep the rest space for logs tools and updates so we dont need much for this VM.

Once your done click next. and click Finish, and then a new window will appear of the VM sucessfully working and installing the OS like shown here:

<img width="1919" height="1033" alt="image" src="https://github.com/user-attachments/assets/c1c34e3d-43e3-4445-93a8-6217393aba4d" />

Once its done, and you see a yellow test click Enter, and ```Ubuntu login``` will appear.

<img width="1280" height="800" alt="VirtualBox_Ubuntu_12_04_2026_20_05_11" src="https://github.com/user-attachments/assets/54730f2a-7c45-4963-bd9a-a2d1c0c1fb4a" />

That means the VM have sucessfully installed and wants the username and password login so we will enter it:

<img width="1280" height="800" alt="VirtualBox_Ubuntu_12_04_2026_20_12_33" src="https://github.com/user-attachments/assets/7075d457-e31e-4c41-9428-aa97c69121ea" />

Now we have completed step 2 going to step 3 which is

## Step 3 system audit and patching

We need to perform a quick ```sudo apt update && sudo apt upgrade -y``` to ensure that you are starting with a patched kernel and everything is secured

<img width="1280" height="800" alt="VirtualBox_Ubuntu_12_04_2026_20_26_26" src="https://github.com/user-attachments/assets/1e3256f4-97a1-4bca-ba90-961b2e364f4d" />

Once that done do the next command which is ```ip a``` to see your local IP address since we gonna harden SSH. you will want to know where the server lives in the virtual network

<img width="876" height="257" alt="image" src="https://github.com/user-attachments/assets/c2ba550b-c8ec-4cc4-9a34-9dffaa7dd808" />

As you can see. in ```2: enp0s3```, we can see our IP address which is ```10.0.2.15```. now that we know that we have updated our server and know the IP address we will go to

## Step 4 attack surface reduction

What we gonna do in this step is basically reducing any attack opportunities by enabling uncomplicated firewall aka UFW.

Linux tend to let anything talk to it so lets change that to make it only talk to the host. first lets start by typing ```sudo ufw status```

<img width="280" height="50" alt="image" src="https://github.com/user-attachments/assets/0c6f6b47-d25c-4d4a-a708-e9cb5d993d2e" />

By default it will say ```status: inactive```.

And the plan is that we will tell the firewall to deny everything by default then we will allow only SSH so that we wont lock ourselves out, then we will enable it. 

So start by writing ```sudo ufw default deny incoming``` then ```sudo ufw default allow outgoing```. after that do ```sudo ufw allow ssh``` this is critical so that you wont lock yourself out. Then enable the firewall by typing ```sudo ufw enable```.

<img width="497" height="423" alt="image" src="https://github.com/user-attachments/assets/512c91c8-1fd4-486d-8424-774354702541" />

Now that we have successfully got the firewall active, we gonna change our SSH port. to do so we gonna edit the SSH configuration file. By changing the default SSH ```port 22``` that is targeted by attackers to something random, for example ```2222```.

And we gonna disable root automatic login so that the superuser needs to enter manually. But i ran into an issue, we suppose to go into ```nano``` and edit the permission but it was a blank page. so i did ```ls /etc/ssh/```

<img width="963" height="55" alt="image" src="https://github.com/user-attachments/assets/2217b927-e8e8-4c51-8e17-5e60e55b7736" />

And as you can see we cant find ```sshd_config``` because ```ssh_config``` is the VM wants to connect to other computers. But the missing one which is ```sshd_config``` is when you want to secure the VM itself. but i solved it by downloading openssh-server. so write ```sudo apt install openssh-server -y``` once its done you'll see the ```sshd_config ```

<img width="959" height="67" alt="image" src="https://github.com/user-attachments/assets/ef40de58-c6b0-4663-9c3a-f1c0b9e29565" />

Now that we solved the issue, lets run the code ```sudo nano /etc/ssh/sshd_config```

<img width="963" height="800" alt="image" src="https://github.com/user-attachments/assets/6fb447db-9b77-41ae-958a-d136e91a4cfe" />

Great now we are in nano, what we gonna change here is port from ```22``` to ```2222```

<<img width="186" height="82" alt="image" src="https://github.com/user-attachments/assets/f58cdc43-1abe-4d21-9c89-b02eb179efe2" />

And for root login look for ```PermitRootLogin prohibit-password```. What we need to do here is to delete the ```#``` and change ```prohibit-password``` to ```no``` like this:

<img width="182" height="96" alt="image" src="https://github.com/user-attachments/assets/bb9c7768-4fdb-4cb8-b34e-242650c91844" />

That way nobody is allowed to login as root directly. you must login as a regular user first. And now we can save by doing ```ctrl + O``` then ```enter```, then exit using ```ctrl + X```.

Now run ```sudo ufw allow 2222/tcp``` and sudo ```ufw delete allow ssh```

<img width="365" height="119" alt="image" src="https://github.com/user-attachments/assets/088b8140-13c0-4e70-8203-8d52fc418cf1" />

That way your telling the firewall about the new port. and removing the old port which was 22. and now restart SSH so that everything applys by typing ```sudo systemctl restart ssh```. and then to check type ```sudo ufw status verbose```

<img width="499" height="196" alt="image" src="https://github.com/user-attachments/assets/60fc1cf8-a757-4d16-9eb7-b2bd45b0febb" />

As you can see the port is ```2222```. with that we offically harden the server. making it a secure linux environment.

## Verdict
We did alot today. from setting up a virtual machine to hardening a linux server, thank you for your time and until next time.
