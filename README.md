<p align="center">
  <img src="https://github.com/user-attachments/assets/7fcbbd21-02b6-466e-a783-e5b218124318" alt="Microsoft Intune project banner" width="716">
</p>

# Deploying 7-Zip as a Win32 Application with Microsoft Intune

## Project Overview

This lab demonstrates how to package, configure, assign, and verify the deployment of **7-Zip** to a Windows 11 virtual machine using **Microsoft Intune**. I used the Microsoft Win32 Content Prep Tool to package the installer, created a Win32 app in Intune, configured installation settings and detection rules, and targeted an enrolled test device through a Microsoft Entra security group.

**Result:** I verified that 7-Zip was installed on the test VM and that its application files were present under `C:\Program Files\7-Zip`.

## Skills Demonstrated

- Win32 application packaging (`.intunewin`)
- Microsoft Intune app configuration and assignment
- Installation requirements and detection rules
- Microsoft Entra ID security groups
- Windows device enrollment and app deployment verification
- VMware-based Windows lab testing

## Lab Environment

- Microsoft Intune admin center
- Microsoft Entra ID tenant
- Windows 11 Pro virtual machine hosted in VMware
- Microsoft Win32 Content Prep Tool (`IntuneWinAppUtil.exe`)
- 7-Zip Windows installer

## Prerequisites

- A Microsoft Entra tenant with Intune licensing available for the test user/device
- Appropriate Intune administrative permissions
- A Windows 11 VM with internet connectivity
- An Intune-enrolled Windows device (or the ability to enroll one)
- The 7-Zip installer and Microsoft Win32 Content Prep Tool

> **Note:** Microsoft Entra joining and Intune enrollment are separate states. Joining a device to Entra ID does not, by itself, guarantee that the device is managed by Intune. Automatic MDM enrollment must be configured when using that enrollment path. Verify the device appears in Intune and reports an appropriate management state before expecting an app assignment to install.

## Deployment Steps

### 1. Prepare the installer and packaging folders

I created three folders to organize the packaging process:

- **Source:** Contains the downloaded 7-Zip installer (`.exe`).
- **Tools:** Contains the Microsoft Win32 Content Prep Tool (`IntuneWinAppUtil.exe`).
- **Output:** Stores the generated `.intunewin` package.

The official tool is available in Microsoft's [Win32 Content Prep Tool repository](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool).

![Packaging folder structure](https://github.com/user-attachments/assets/47de9edb-dca6-48e5-8d0c-e043536e2549)
![Tools folder](https://github.com/user-attachments/assets/e23c5891-8d03-461e-aeec-e159879e781f)
![Output folder](https://github.com/user-attachments/assets/3693ace7-a459-4963-a1f6-0ba0c0ae2698)
![Source folder](https://github.com/user-attachments/assets/f3b79b34-c1f7-45c3-b5c3-cad9d7e55058)

### 2. Package 7-Zip as a Win32 application

I ran `IntuneWinAppUtil.exe` and provided the source folder, installer filename, and output folder. The tool packages the installer and supporting source content into a **`.intunewin`** file that can be uploaded to Intune. This is not a GitHub package format; it is the format used by Intune for Win32 app deployment.

![Running the Win32 Content Prep Tool](https://github.com/user-attachments/assets/40e9204e-be4e-49bf-ac81-c11e40ebe5d8)

### 3. Add the Win32 app in Intune

In the **Microsoft Intune admin center**, I navigated to **Apps > Windows**, created a new application of type **Windows app (Win32)**, and uploaded the `.intunewin` file from the Output folder.

![Creating a Win32 app in Intune](https://github.com/user-attachments/assets/18bebd3a-f365-4f3a-bf89-7f91bab6785a)

### 4. Configure installation settings and requirements

I entered the application's metadata and configured the **install** and **uninstall** commands for the installer I packaged. The exact commands depend on the specific 7-Zip installer version and whether it is an EXE or MSI; they should be tested for silent installation and removal before broad deployment.

I also configured operating-system requirements so Intune would evaluate whether a targeted device was eligible for the app. These requirements are distinct from the app's detection rules.

![Application settings and requirements](https://github.com/user-attachments/assets/aec7ec68-f2b8-483b-97f5-1cd26ef8d593)

### 5. Configure the detection rule

I configured a detection rule to check for evidence that 7-Zip was already installed, using its installation location under `C:\Program Files\7-Zip`.

Detection rules tell Intune **whether the app is installed**. They do not specify the installation destination; that is controlled by the installer and its command-line options. If the detection rule succeeds, Intune reports the application as detected rather than repeatedly trying to install it. A file or folder existence check can be used in a lab, although a version-aware file or MSI product-code rule may be more suitable for production deployments.

### 6. Enroll the Windows 11 test VM

I prepared a Windows 11 VM in VMware and connected it to my Microsoft Entra tenant through Windows **Settings > Accounts > Access work or school** using the appropriate join/enrollment workflow.

I then verified the device's Intune management status. The **Sync** option can request a new management check-in, but a successful Entra sign-in or join alone is not proof of Intune enrollment.

![Windows work or school account settings](https://github.com/user-attachments/assets/d1bb44c7-dcb0-4d6d-96b7-8263309b7498)

### 7. Create a security group and assign the app

I created a Microsoft Entra **security group** and added the test VM as a device member. In the 7-Zip application's **Assignments** section, I targeted that group with a **Required** assignment.

- **Required:** Intune attempts to install the app automatically on eligible targeted devices.
- **Available for enrolled devices:** Makes the app available for user-initiated installation through Company Portal when assigned appropriately (typically to a user group).

An **Available** assignment does not automatically install the application, and uninstall behavior depends on additional configuration and permissions.

After assigning the group, I initiated another device sync to request a check-in.

![App assignment to a security group](https://github.com/user-attachments/assets/b2c1e89b-c19c-427a-96f7-5a7d5f31dd49)

### 8. Monitor installation status

In Intune, I opened the 7-Zip app and reviewed its **Device install status** to monitor the deployment. App delivery and status reporting can take time because devices must check in, evaluate policy, and process the installer and detection rule.

![Intune device installation status](https://github.com/user-attachments/assets/8372a3c1-6222-414f-9ab8-79f7cb5465d1)

### 9. Verify installation on the VM

Finally, I checked the Windows 11 VM and confirmed that the 7-Zip application files were present under `C:\Program Files\7-Zip`. I also checked that the application could be found from Windows. This provided device-side confirmation of the deployment.

![7-Zip installed on the Windows VM](https://github.com/user-attachments/assets/500ee5a4-284c-420e-b65e-66796ca25840)

## Troubleshooting and Lessons Learned

During the lab, I encountered an installer/package error while configuring the deployment. This reinforced the importance of matching the installation command to the actual installer type (**EXE versus MSI**) and testing silent installation behavior. It also highlighted why detection rules, device enrollment status, and app assignment targeting should be checked separately when troubleshooting deployment failures.

Key takeaways:

1. **Packaging:** Intune Win32 apps use `.intunewin` packages created with the Microsoft Win32 Content Prep Tool.
2. **Installer commands:** Silent installation and uninstall switches vary by installer; they are not interchangeable across EXE and MSI packages.
3. **Detection:** A detection rule should reliably identify the installed app, ideally including version checks when relevant.
4. **Enrollment:** Microsoft Entra join and Intune MDM enrollment are related but distinct.
5. **Assignments:** A Required deployment installs automatically on eligible targets; an Available assignment enables user-initiated installation in Company Portal.
6. **Verification:** Both Intune reporting and on-device validation are useful for confirming a successful deployment.

## Win32 Application Installation and Uninstallation Commands

When deploying Win32 applications through Microsoft Intune, the installation and uninstallation commands depend on the installer type (.MSI or .EXE).

| Installer Type | Install Command | Uninstall Command |
|---|---|---|
| **MSI** | `msiexec /i "application.msi" /qn /norestart` | `msiexec /x "{PRODUCT-CODE-GUID}" /qn /norestart` |
| **EXE (NSIS)** | `"application.exe" /S` | `"uninstall.exe" /S` |
| **EXE (Inno Setup)** | `"application.exe" /VERYSILENT /NORESTART` | `"unins000.exe" /VERYSILENT /NORESTART` |
| **EXE (InstallShield)** | Depends on installer configuration | Depends on installer configuration |

### Command Parameters Explained

| Parameter | Description |
|---|---|
| `msiexec` | Windows Installer command-line utility |
| `/i` | Installs an MSI package |
| `/x` | Uninstalls an MSI package |
| `/qn` | Runs without displaying a user interface |
| `/norestart` | Prevents an automatic restart |
| `/S` | Silent installation/uninstallation for NSIS installers |
| `/VERYSILENT` | Silent mode for Inno Setup installers |
| `{PRODUCT-CODE-GUID}` | Unique MSI product identifier used for uninstallation |

**Important Notes:**

- MSI installation commands generally use `msiexec`, whereas EXE commands depend on the installer technology.
- Not every EXE supports `/S`, `/silent`, or `/quiet`. Always consult the application's documentation.
- The executable name and uninstall path must match the actual application package and installation location.
- Quotation marks are recommended around executable paths containing spaces.
- Test installation, detection, and uninstallation commands before deploying applications to production devices.





## Outcome

**7-Zip was successfully deployed to the Windows 11 lab VM.** This project gave me hands-on practice with the core workflow for deploying Win32 applications using Microsoft Intune, including packaging, application configuration, security-group targeting, detection, and deployment verification.

## References

- [Microsoft Win32 Content Prep Tool](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool)
- [Microsoft Learn — Win32 app management in Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-app-management)

