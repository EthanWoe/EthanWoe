## Minecraft Server Hosted on AWS.
* ***Software Needed for Minecraft on AWS***
  * Most recent version of Java
  * A active Minecraft java license/account
  * Offical minecraft server .jar file

* ***What is this Software?***
  * Java: Java is required to host a Minecraft server as the game's engine and core is written from java meaning without it
    the server would not be able to start.
  * An active Minecraft Java license: This is just a valid copy/license of the game to purchase an account it can be found [here](https://www.minecraft.net/en-us/store/minecraft-java-bedrock-edition-pc?tabs=%7B%22details%22%3A0%7D)
  * .jar File: The Offical .jar file is an executable file needed to run a Minecraft server it holds all the necessary code and resources for the server software.
     
* ***How does this affect me?***
    * This writeup will show users how  to setup a minecraft server from a ubuntu virutal machine using AWS.
     * This includes the startup for the ubuntu server in AWS, configuring the AWS network settings such as security groups,  downloading and updating the ubuntu server if its the first time launched, installing the offical.jar file, and finally connecting to the server via the Minecraft Application.

## VPC SETUP FOR AWS.
* ***CIDR BLOCK***
    * The current CIDR block i have is a 172.31.0.0/16 CIDR as that comes default with the server. The CIDR block i would prefer is a /24 block though.
    * The current /16 block will be more than enough for me too mess around with me and my friends and it works at default so it is usable. The more ideal CIDR block would be a /24 as it limits the current IPS to around 14 which is still more than enough for each of my friends to play.

* ***Subnet Block***
    * Again the default subnet block from AWS is a /20 for each subnet. But the Ideal subnet block would be a /28
    * With this subnet block it gives access to 16 ips which 2-3 of those IPS are used up by AWs giving us around 13 or so ips. These ip's will be more than enough to host a small server with friends or a workgroup. 
* ***Route Table***
    * I have one route of 0.0.0.0/0 towards the VPC
    * This allows updates to the server and for other IP's to connect to the server.
* ***Network ACL***
    * Inbound Rules:
      * TCP 25565 is allowed.
      * SSH TCP 22 is allowed.
      * All Outbound traffic is allowed.
   * This makes sure that Minecraft can run and that the server admins can connect via ssh and then otherwise restricting the traffic from other ports.
* ***Security Group***
    * Inbound Rules
      * TCP 25565 from public ips
      * SSH 22 from my IP only
    * This makes sure that other people can connect to the server and no one can acess the console without access.

## AWS Instance Setup

* ***Instance Type***
  * The instance that i chose was a t2.large which comes with t.2 large comes with 8 gigs of ram and 2 vCPUs
  * This server size is good for 1-3 different people at once. I think that if you wanted to play with more you would want to do a t2.xlarge and for the most performace a t2.2xlarge. The only reason i say this is that minecraft eats up a good chunk of memory when loading in different chunks. When you have multiple people running seperate directions each loading in serperate chunks it will take a toll on the servers performance leading to drops in tick rate and lag.
   
* ***AMI***
  * The Ami i used for the server is Ubuntu.
  * The reason that i used ubuntu is that there were mutliple different resources installing Minecraft servers via ubnunu. This means if you run into problems you most likely could ask a online thread how to fix a problem or look up the problem and find a solution that way aswell.

* ***Volume Size***
  * The volume size that i choose was 32 GIGS.
  * I chose 32 Gigs of storage as I was not sure how many different people wanted to play my server. If i was having more than three or four then it would be neccesary to have the extra space of 32 gigs compared to something like 8 or 16. 

## Cost Estimates

* ***Summary of all Costs***
  * For a month of 24/7 server usage with a t2.large it comes out ~65 USD.
* ***Projected Cost***
  * My server has not been very active so my [projected cost](https://github.com/user-attachments/assets/14a38bff-85c3-4ab6-a9be-076fee38e8fb) has not updated yet.
* ***Cost of a t2.large***
  * The cost of a t2.large is around 0.0832 USD per hour.
* ***Cost of having a Elastic IP***
  * The cost of having a elastic IP is  0.005 USD per hour.
* ***Cost of Ubuntu***
  * Running Ubuntu on your AWS instance does not incure any aditional charge. 

## Installation Instructions

***Setting up AWS***
 1.  After launching your lab press the green button and navigate to the EC2 home page.
 2.  Next their should be a button that says *Launch Instance* if it does not please press on the *Instances* button on the left.
 3.  Name your server then select Ubuntu.
 4.  Next select the architecture you wish to use. I used 64bit (x86).
 5.  Then select your server type to fit your needs. I would recommend a t2.large but go no lower then a t2.medium.
 6.  Make a key pair and name it minecraft this can be used to ssh into your instance in the future.
 7.  For the Network settings select edit in the top right and scroll down the the inbound rules.
 8.  Under SSH change it to my ip or enter in your ip. Then add a new security group.
 9.  In the new security group it needs to be Custom TCP and the port needs to be 25565 which will allows us to connect with the minecraft port.
 10.  Choose the storage type you want i would suggest around 32. Then press launch.
 11.  After launching head over to elastic IPS in the EC2 page under Network and security.
 12.  Press allocate elastic IP then press allocate.
 13.  Finally click on your elastic IP then associate it with your minecraft server. This will allow the server to have a static IP adress.

***Ubuntu Setup and Minecraft host Installation***

1. Connect to your Ubuntu server either using SSH or through EC2 then click on your server and in the top right press connect.
2. After connecting update and upgrade your instance using `sudo apt-get update && apt-get upgrade -y`
3. After this is completed install java for minecraft by typing in `sudo apt install openjdk-21-jdk`
4. Next head to [minecrafts website](https://www.minecraft.net/en-us/download/server) to get the most recent jar file.
5. Head to the part that says "Download minecraft_server.1.21.5.jar and run it with the following command" and right click on the link and copy it.
6. After copying the link head back into your instance and type `wget https://piston-data.mojang.com/v1/objects/e6ec2f64e6080b9b5d9b471b291c33cc7f509733/server.jar`
7. After typing this if you do `ls` there should be a file named "server.jar"
8. Then type `echo "eula=true" > eula.txt` as their is no gui for us to accept or deny the EULA that is associated this command must be put in to set up the server.
9. Finally type `java -Xmx1024M -Xms1024M -jar server.jar nogui` The 1024 is interchangable with the amount of ram you want allocated with the server i would reccomed you use most and leave one or two gigs spare.
10. [This](https://github.com/user-attachments/assets/24339162-1ec3-4036-8b47-e7e4c13beba6) is what it should look like if all the steps were performed properly. 

***Verifying and Connecting to your Minecraft Server.***
1. If you want to verify your server without having to open your game open up "Windows PowerShell"
2. Head over to your Instance and copy the Elastic IP or the public IPv4.
3. Type in this command `Test-Netconnection -Computername 54.91.122.235 -Port 25565`
4. If your server is running you should get a message stating that ["TcpTestSuccessful = True"](https://github.com/user-attachments/assets/17746a10-f7e3-40dc-b13f-b1556928f8e3) if it does not say True then something is wrong with your server.
5. After getting the verification that your server is wokring launch the most recent version of minecraft at the time it is 1.21.5
6. Once in Main Menu press Multiplayer than direct connect or add a server.
7. Type in the elastic IP/Public ipv4  where its asking for a IP and press connect.
8. Now you should have your own server hosted on AWS 😄


The guide that i used for this instalation was this [video](https://www.youtube.com/watch?v=RoZumss8Wug) It is quite helpful if you need a visual aid but he does make some mistakes in it such as installing the wrong version of java.

## Security

***Admin and Whitelist***
 *Through the console of the server i was able to make myself a administrator by typing `/op Flipper` 
 * Then in minecraft or through the console i typed `/whitelist on` Making it so that no one else can join the server without explicit permission from an admin.

 * User attempting to join the server without whitelist permissions [in game](https://github.com/user-attachments/assets/9e14997b-f244-494e-a4c5-34c91dfdb6fc) vs in [console](https://github.com/user-attachments/assets/31605a54-3c50-4e4c-8655-5cd1cbea6651)

 * To whitelist a user type `/whitelist add ign` it should look something like [this](https://github.com/user-attachments/assets/cf452e65-1282-467c-82c0-b22b9d9d99db) in the command line
 * Then that user will then be able to join as shown in the console.

 * Their are other commands that minecraft has to prevent bad actors aswell such as blacklist which will then blackist that players ip adress or ban which will just ban that players account.

 ## ITS ALIVE!!!!

 ***Server with mutliple people on it!***
  ![image](https://github.com/user-attachments/assets/726ec938-920a-4012-88be-137289d2d24a)

## Backup Policy
  * Minecraft has some built in protection for the server istelf with a command `/save-on` which saves the current world to my directory. The nice thing about this is that player data is also saved with this command meaning if you had to rollback people would only lose that certain time frame of items and not everything in total.
  * To make this more powerful I could connect something else too it that takes the save file data every hour or so and puts it on a different machine to make sure there is two different copies in two different places all the time.
  * Recovery time should be almost instant as you can just choose a previous save version. The time of complete failure may take longer depedning on what fails. If its the file gets corrupted it should'nt take much time but if its AWS that would take as much time as AWS.
##Troubleshooting

 ***Amount of Data***
 The amount of data that would need to be backed up would probably be around 7-10 gigs per backup. This would mean having at least 10 that overwrite eachother every other hour would need around 120 gigs of storage for everything. 

 ## Troubleshooting

* Troubleshoot other people connecting to your server to make sure whitelist and commands work proplerly.
* Testing how much Ram you need to run the server without hiccups.
