# ConfickerWiki

## Background
The Conficker Worm was first discovered on November 21st 2008. It targeted Windows XP/2000 computer systems. The purpose of the virus was to add the infected host to a network that could download executable code. This means that the worm does very little damage in and of itself, but allows the user in control of the worm to gain acces to a large network of computers, effectively creating a botnet. These are very dangerous, as they can carry out a number of attakcs, such as DDoS.

The creator of the worm has still not been found. It is believed that the creator is from Ukraine, based simply on the fact that the virus has steps to prevent it from infecting Ukranian computers. 
![alt text](https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/Conficker.svg/1200px-Conficker.svg.png)


In the end, the worm was somewhat unsuccesful, because of how well it worked. At it height, it is believed to have infected in the vicinity of 11 million devices. This made it a hot topic in the security community, which quickly started working together on counter-measures. Not many notorious attacks were carried out by the worm, however it did infect the German and British military computer networks, as well as leading to the grounding of French fighter jets. While in these systems, the attacks did nothing but be present, and led all these organisations to have to shut down use of those systems until the worm could be removed. It is estimated that it cost the security community near $9bn to attempt to get rid of the virus. To this day, the attack is still present, although it has not been able to work outside of Windows XP/2000 systems, making it less dangerous as newer OS' are developed.

There are 5 variations of the Conficker worm (A, B, C, D, E). All achieve the same thing, but in different ways, as will be explained later.


## Attack Method
Attack file is a packed binary file, propagated as a DLL. This file is then run through the svchost.exe executable, which is run on starting windows.

To see the contents the program, the dll code is run after some decryption of the contents, after which the library is stored in memory, snapshotted and the instructions reconstructed into an executable.

![alt text](https://images.slideplayer.com/24/7446365/slides/slide_19.jpg)

### Network Infection Method
To infect computers, the virus exploits windows file/printer sharing connections. It sends a specially crafted Remote Procedure Call(RPC), to port 139 or 445 on the session. This takes advantage of a vulnerability, which has been designated MS08-67. This vulnerability is caused by the ability of a specific type of RPC to be able to overflow the buffers, loading program data onto memory locations that allow to the system to execute code without authorization. This will then install the code on the target computer. If the target computer has a firewall, the code will attempt to use a Universal Plug and Play message to attempt to open a port for backdoor communication. If successful, the host computer would open the same port as the target computer, and the worm binary would be able to be transferred.
The above procedure assumes that the sharing of files procedure is not protected by a password. In the case that it is, the code attempts a brute force attack on the password, specifically the dictionary attack. In some cases, the 'guessing' of passwords is limited to a certain amount of attempts. If these are exceeded, the network will lock down until the source of the attack is found. This means that even if the worm was unsuccessful in infecting the host, it still did damage in the form of incapacitating the target computers sharing functionality.


### Drive Infection method
Conficker B allowed for the worm to spread through removable drives, such as usb sticks. Many drives contain autorun files, so that when they are inserted into a computer, the drive for example immediately opens for viewing files. Conficker embeds itself into the drive under a random name, and adds an autorun file, which if run, installs the dll onto the target computer. This is a very simple method of spreading the virus, as not all security software automatically scans removable drives.


### Installing the binary on the computer for execution
Once the binary is on the target computer, the first step  is to check whether the system is configured for a Ukranian keyboard. If it is, the binary removes itself. Otherwise it checks the OS type (typically installs itself on Windows XP/2000 computers) and proceeds to install its dll. This is done under a randomly designated name within the Win32 system file. A recently installed dll could be suspicious to expert users or firewalls, therefore when the dll installs itself, it uses the timestamp of the kernel32.dll, which is a trusted file. Another security measure is that the dll sets itself under the authorization of the system only, thereby not allowing deletion by a computer user. Once installed, the code is executed by the svchost.exe. This service call is used to execute dll's, and multiple instances of it are run on starting up windows. For the svchost executable to find the dll, its registry key must be added to the list in 'SOFTWARE\Microsoft Windows NT\CurrentVersion\SvcHost' so that the svchost finds the randomly generated name for the dll in the registry. 

Once the program has installed itself, it attempts to setup an http server. It does this by first obtaining the ip of the system by visiting one of the following websites:
* getmyip.co.uk
* getmyip.org
* checkip.dyndns.org

Next it will check if the system is in the Ukraine, by checking the ip address against the GeoIP database. If it is in the Ukraine, then the worm suicides, and the system is no longer infected. Otherwise it attempts to set up a HTTP Server, on the following address: *http://[ip_adddress_of_system]:[random_port]*. From this the server will scan for other devices to infect.

### Payload Propagation
Once the svchost executes the program, it generates 250 random domain names	. The random number generator is seeded with the system date. In this way, all infected hosts with the same system date,generate the same ip addresses. These domains become rendezvous points for communication between all the infected hosts, and the Conficker client which wishes to upload a file. In Conficker C, the domain name could also be transported to all computers on a local network by opening a pipe.  

### Payload Protection
To prevent the payload from hijackers, it is SHA-1 hashed and RC4 encrypted. The hash is signed with a private key, and once the payload is propagated and unpacked, the code will only be executed if the signature is verified by a public key, which is contained in the worm. 

## The variants
As mentioned, Conficker has 5 variants (A,B,,C,D,E). A was the first, and relied purely on the MS08-067 vulnerability to spread. B added the ability for spreading the virus through removable drives. C increased the amount of domain names generated. C allowed peer-to-peer communication through the pipe communication previously mentioned. D was not a new release of virussses, but instead simply updated the systems infected with variant C, and changed the algorithm used to generate domain names. E was another update for Conficker C, and downloaded Waledac spambot and SpyProtect scareware.

## Other effects

* Disabling of Windows Auto-Update, Defender
* Certain security websites become disabled (Windows update or antivirus software)
* Users of the system locked out




## Sources
https://www.welivesecurity.com/2016/11/21/odd-8-year-legacy-conficker-worm/
http://www.csl.sri.com/users/vinod/papers/Conficker/
