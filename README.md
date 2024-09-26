# deploying-Prestashop-on-AWS-with-remote-database-configuration
This repository contains a guide for deploying PrestaShop on an Amazon Web Services (AWS) instance using the Free Tier.Notably, the setup ensures that the database is hosted separately from the application server.
 then nav
**STEP 1 : EC2 SETUP AND LAUNCH**
i.Login into AWS managment console or create an AWS account if you don't have one then sign in and navigate to AWS management console.
ii.Now let's create an ec2 instance,click on ec2  on the left pane then click on launch instance.You should have this webpage now name your instance.

![image](https://github.com/user-attachments/assets/ce09aede-05dd-4ab1-88dd-805f1ebaf816)

iii. Next select Ubuntu server 24.04 LTS as our AMI i.e Amazon Machine Image 

iv.Then we select t3.micro as our instance type as it's eligible for free tier
 ![image](https://github.com/user-attachments/assets/6d1e09bc-7511-469a-ac25-2d71a70b30b1)
 
v. Moving on,we create a key paiir which we use to connect our instance via ssh
  ![image](https://github.com/user-attachments/assets/654edec5-5115-4036-b837-95dc1dc1bd0e)
  select approprtiately as shown in image below and create.This would prompt a download immediately.
  ![image](https://github.com/user-attachments/assets/aea308bc-1739-4494-b26b-8edefd6b6f19)
NOTE: secure your key pair as we'll need in future step

 vi.  Now to the network settings,let's configure the inbound security groups 
 Allow HTTP (port 80) for web traffic.
 ![image](https://github.com/user-attachments/assets/e2ffae82-6c0a-4f2d-a57d-f0404b04d4d7)

  Allow SSH (port 22) for remote access.
 ![image](https://github.com/user-attachments/assets/8919e146-49a2-4c3c-a678-2c3c6c6e44a5)

  Allow HTTPS (port 443) for secure traffic.
![image](https://github.com/user-attachments/assets/7806fd96-d6b3-492f-8758-0141d640bab8)

v. Finally here, we can now launch our instance.
    ![image](https://github.com/user-attachments/assets/33349b28-3c25-4e1a-b30c-e62bf6b1e95d)


**STEP 2 : EC2 CONNECTION**

i. Firstly,let's connect to our instance via SSH to get that done hover over your instance and click connect
  ![image](https://github.com/user-attachments/assets/9cbda1ed-0849-46b7-9740-81ac31e0ac69)
As listed in the instruxtions below,copy the ssh address and dive  into your terminal 
  ![image](https://github.com/user-attachments/assets/0b2fe3bc-1d45-40d9-8ffa-90aacb6199ad)

ii. Navigate to the directory where your key file is stored

            cd 'filepath'

![image](https://github.com/user-attachments/assets/0cd3207d-636c-4f74-9103-dcdb65ad2665)

iii. Now  let's put in our ssh address....
   Being that I am using WSL I was faced with permission isssues in accessing my key file 
      ![image](https://github.com/user-attachments/assets/ab4905f1-4b96-4c2d-84f1-bc32903e472b)
  To resolve that let's move our key file to our WSL directory
             ![image](https://github.com/user-attachments/assets/e3a9d61d-bb12-4978-950d-145861b9f9b6)
iv. Copy the SSH address and run it while in the directory where the key file is stored.
   Boom! We've got our instance coonected 
   ![image](https://github.com/user-attachments/assets/6c177db0-face-4548-86e9-02c625a6bc3a)

 **STEP 3  LAMP STACK SETUP**
   Lamp stack comprises of Linux,Apache,Mysql or MariaDB and PHP or Python 
    i.Firstly,let's update our server 
                                                                                                                                                         
                sudo apt update
                
![image](https://github.com/user-attachments/assets/333a86b9-21c1-42c2-bab2-068648b52971)


                 sudo apt upgrade -y          
                 
  ![image](https://github.com/user-attachments/assets/94b37209-213b-4a0b-b04f-9f7ef624488a)

ii. Apache installation

                        sudo apt install apache2 -y
![image](https://github.com/user-attachments/assets/677eccab-1246-4289-9f10-b7221d2af1c2)

iii.  MYSQL client installation (Database to be hosted separately)

          sudo apt install mysql-client -y
          
![image](https://github.com/user-attachments/assets/20c53b42-23e1-47ae-8cda-8213d49e66a9)

iv.  PHP  installation-including some modules to ensure diverse functionality

           sudo apt install php libapache2-mod-php php-mysql php-xml php-gd php-curl php-intl php-zip php-json php-mbstring -y

  ![image](https://github.com/user-attachments/assets/ae01e846-1a83-4d9b-a45a-7f3ed4214a3b)


  **STEP 4: RDS SETUP**

  i.Navigate to RDS on AWS
  
  ![image](https://github.com/user-attachments/assets/18ba6d77-116c-4740-8113-1c72720c1af8)

  ii. Click on create new database
  
  iii. Select standard creation method     
  
  iv.  Select engine version
  
  v.  Select  template 
  
   ![image](https://github.com/user-attachments/assets/f4fa6194-e9e0-488e-87ac-ee292082a285)

  vi.  Now let's provide a master username and password for our database.I'll be using the auto-generate option.
         ![image](https://github.com/user-attachments/assets/5ba7b855-6fb9-4dfc-b1f1-bf5c2292c372)
   
  vii. Now in  the instance configuration section,don't select previous generation classes,select db.t2.micro from the dropdown list. This is a Free-Tier eligible instance size.
      ![image](https://github.com/user-attachments/assets/93ca9b6e-6fb3-4f11-9633-962a6ae4d77c)
      
 viii. In the storage selection,choose general purpose SSD,allocated storage remain at 20gb and the advanced settings can stay default.
 
   ![image](https://github.com/user-attachments/assets/97e2187e-a3c2-4f71-8984-56cfacdccd32)

ix. Now in the connectivity section,select connect to an ec2 resource and choose our ec2 instance in the drop down.Our RDS and ec2 instance associate easily and share configuration.
      ![image](https://github.com/user-attachments/assets/fb242342-bf14-4512-8334-deec8254a3b6)

 x.  Next select default as DB subnet group,"No" for public access and "choose existing" for our firewall  VPC group.The rest should remain default in this section.

 ![image](https://github.com/user-attachments/assets/e482413f-87b8-4856-b67b-9151e6ef5b3b)

 xi.   In the database authentication section,select password authentication

  ![image](https://github.com/user-attachments/assets/57e0f39b-b8dd-4322-b3f0-c3f1dd4e34bf)

  xii.  optionally,you can turn enable enhanced monitoring and set as appropriate,in my case..

  xiii. Finally,in the advanced configuration section we name our database,leave everything else as default except the deletion protection which we'll enable and and backup retention I'll set mine to 6 days.

  ![image](https://github.com/user-attachments/assets/a980f0cd-5700-409b-bfcd-4aa596bf064c)
     
 Also in the log exports let's select 'error log' and 'slow query log' for troubleshooting and performnce purposes

 ![image](https://github.com/user-attachments/assets/40d023f5-b1cd-4e70-b70c-ae12a8373fdd)

 Create database 
 ![image](https://github.com/user-attachments/assets/5c3ce0c1-4c0f-474a-8045-bfa8d4217bc7)
Our database and ec2 instance linked up successfully
 ![image](https://github.com/user-attachments/assets/e68ea569-ee7d-4ee0-8dfa-791f6dec9556)



 **STEP 5: PRESTASHOP INSTALLATION**

I. Let's use the  wget command to download Prestashop file

            wget https://download.prestashop.com/download/releases/prestashop_1.7.8.8.zip

  ![image](https://github.com/user-attachments/assets/18e2a19b-cc47-476b-a028-28f4de2894ce)


 ii. Now let's unzip the file and move to the web root directory.

           sudo apt install unzip
  ![image](https://github.com/user-attachments/assets/4d7a54b7-4772-429c-af58-daa44dfccbdc)
      
           unzip prestashop_1.7.8.8.zip
          sudo mv prestashop /var/www/html/prestashop


**STEP 6: DATABASE CONNECTION CONFIGURATION**

i. Get the AWS RDS endpoint,copy it  

   ![image](https://github.com/user-attachments/assets/5ae5aeb5-d454-4279-9f87-caaaa8a6b1a5)

ii. Create database for Prestashop

   ![image](https://github.com/user-attachments/assets/e19c0768-9395-41c9-98be-eb436609c491)

   exit the mysql client

                        exit

**STEP 7: PRESTASHOP INSTALLATION ON BROWSER**

i. Firstly we open our EC2 public address on our browser
    
    'ec2PublicAddress'/prestashop/install

![image](https://github.com/user-attachments/assets/fa5a4d9f-37d2-4500-b34c-206fadef176b)

ii.  Follow the installation procedure as shown in browser.As shown in the image below we are lacking some requirements 

![image](https://github.com/user-attachments/assets/26248d79-45ed-4764-8884-960bd233f2ad)

let's install the GD library in our Virtual Machine

    sudo apt install php7.4-gd
![image](https://github.com/user-attachments/assets/970f4d1c-5615-41a9-8d3d-b68e3dc0a888)

Now let's install and enable the intl connection

      sudo apt install php7.4-intl

![image](https://github.com/user-attachments/assets/e5a57781-120b-4a86-a531-3326e8d8add0)

Let's enable apache mod_rewrite

        sudo a2enmod rewrite

![image](https://github.com/user-attachments/assets/fd0aabc6-b132-43cc-ae58-0db14850220f)

as mentioned,don't forget to restart your server so the changes can be updated

![image](https://github.com/user-attachments/assets/a644529f-a278-40fd-8096-65027853fcd2)

To clear the above issue let's update the intl componeent
 a. install the composer,php dependency manager

              sudo apt update
              sudo apt install composer

   ![image](https://github.com/user-attachments/assets/cd3f3ccb-81b0-4eab-89b2-721f03971bd7)

  Next let's fill out our store information

  ![image](https://github.com/user-attachments/assets/3a22788b-59fc-4376-9c5f-d77c608d9fd9)

  Afterwards we'll configure our database
  
  ![image](https://github.com/user-attachments/assets/9b66292d-a439-4c85-b114-22d65702c063)


  NOTE: You don't need to add port to your address provided you're using the default mysql port(3306).
        Database identifier is different from database name.
        login is your username.

Cheers! Our PrestaShop installation is complete

  ![image](https://github.com/user-attachments/assets/1a0b798d-6314-4663-9122-153b4e0d35db)


          



        



  
           




     









 








  
  

  

    



       

   









 

