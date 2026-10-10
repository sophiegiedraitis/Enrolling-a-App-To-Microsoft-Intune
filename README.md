<img width="716" height="490" alt="microsoft_intune" src="https://github.com/user-attachments/assets/7fcbbd21-02b6-466e-a783-e5b218124318" />

<p align="center">
<img 
</p>

<h1>Enrolling a App Into Microsoft Intune<h1>

 
 <h3>
In this lab, we are enrolling Zip-7 into the Microsoft Intune App Portal. We will configure specific detection rules that determine whether the entra enrolled machine already has the app installed upon it. Once we enroll our VM into the Entra portal, we will configure the VM with a security group to then monitor the app to download the 7-Zip.

 </h3>

<h2>
 Skills Demonstrated
</h2>
 
- Microsoft Intune
- VMWare


<h2>Environments and Technologies Used</h2>

- Microsoft Inune
- VMWware 


<h2>Operating Systems Used </h2>

- Windows 11 Pro (Virtual Machine)</b> (version 22H2)                                                               

<h2>List of Prerequisites</h2>

- Microsoft Azure tenant                                                                 


<h2>Installation Steps</h2>

 <p>
1. First we will need to package up the Zip-7 file into a format that can be uploaded as a app to Microsoft Inune. Under File Explorer we are going to create three folders: Source, Output, and Tools. The Source folder contains the .EXE of the 7-Zip, the Tools folder contains the  Microsoft Content Prep Tools used to package up the 7-Zip download into a format that can be uploaded to Github. The Content Prep Tool can be downloaded from Github here https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool
Microsoft-Win32-Content-Prep-Tool IntunWinAppUtil.exe
</p>   
<img width="1002" height="345" alt="image (2)" src="https://github.com/user-attachments/assets/47de9edb-dca6-48e5-8d0c-e043536e2549" />
<img width="1262" height="322" alt="tools" src="https://github.com/user-attachments/assets/e23c5891-8d03-461e-aeec-e159879e781f" />
<img width="1100" height="336" alt="output" src="https://github.com/user-attachments/assets/3693ace7-a459-4963-a1f6-0ba0c0ae2698" />
<img width="1041" height="491" alt="source" src="https://github.com/user-attachments/assets/f3b79b34-c1f7-45c3-b5c3-cad9d7e55058" />

 <p>
2. Next, we will go into command line to extract the Microsoft Intune Windows App and package up the 7-Zip application to get it into the Intune Windows format that we can upload to Intune
</p>   

<img width="1416" height="567" alt="image (4)" src="https://github.com/user-attachments/assets/40e9204e-be4e-49bf-ac81-c11e40ebe5d8" />

 <p>
3. Now we can go ahead and upload the 7-Zip package to Intune. In the Intune portal when we go to Apps we are going to choose Windows app (Win32) as our app type and upload the output file we created.
</p>   
<img width="1811" height="592" alt="image (5)" src="https://github.com/user-attachments/assets/18bebd3a-f365-4f3a-bf89-7f91bab6785a" />

<p>
  4. Then we can fill in the property information regarding how the app is going to be regarding the programs install and uninstall command. We are also setting up the requirements that a device will be requirekd to have for the program to be installed onto it. In this case, the only requirement is the the computer needs to be at least on Windows 11.
</p>
<img width="1532" height="897" alt="image (7)" src="https://github.com/user-attachments/assets/aec7ec68-f2b8-483b-97f5-1cd26ef8d593" />

<p>
In addition to the requirements we are also setting up detection rules that makes Intune determine whether a device already has 7-Zip installed onto it. Our detection rules state that if a device doesnt't already have 7-Zip installed under C:\Program Files then it will be installed there.

<p>
Now its to enroll a device into our Intune portal and placed it inside of a security group configured for the app to be installed onto it. We are spinning up a Windows 11 VM in VMWARE and then join it to your Entra tennent under the section Work or School accounts of Windows settings. Once its joined, clicking sync willl sync the device enrollment so you should be able to view the device under the Devices section of Intune.
</p>

<img width="1042" height="832" alt="image (8)" src="https://github.com/user-attachments/assets/d1bb44c7-dcb0-4d6d-96b7-8263309b7498" />

<p>
Under the groups section in Microsoft Intune, we are going to go ahead and create the security group that we are going to put the VM into before adding it to the 7-Zip app section.
</p>

<p>
Under the Apps section of Intune under device properties we can now add the 7-Zip security group to the Required section. The Required section automaticall installs the app onto the device as long as the detectiion rules indicate that 7-Zip hasn't already been installed onto the device. The Available section for the installation makes the app available for install if we were to configure our Intune tennent to use Company Portal, giving users the ability to install and uninstall apps on their own within the Company Portal application.
</p>

<p>
Once the device security group is placed within the Required app field then we can go back into our VM and click on the sync button again to speed up the time it takes for the app to be installed onto the device.
</p>
</p>
<img width="1607" height="812" alt="Screenshot 2026-10-10 084425" src="https://github.com/user-attachments/assets/b2c1e89b-c19c-427a-96f7-5a7d5f31dd49" />

<p>
It can take a bit of time for the app to be downloaded onto the devicde. To monitor the installation you can go to the Device install status section of the App section in Intune.
</p>

<img width="1861" height="606" alt="Screenshot 2026-10-10 085039" src="https://github.com/user-attachments/assets/8372a3c1-6222-414f-9ab8-79f7cb5465d1" />

<p>
We can now confirm that 7Zip has been successfully installed onto our VM since we can see the 7-Zip application folder under Program Files and of course search for the find the application
</p>

<img width="1022" height="847" alt="Screenshot 2026-10-10 085548" src="https://github.com/user-attachments/assets/500ee5a4-284c-420e-b65e-66796ca25840" />
