## Compute > Image > Console Guide

## Create Images

Images can be created from default disk of an instance. Instances of the t2, m2, c2, r2, and x1 type, except u2 type, support for creating images during instance execution, but do not ensure data integrity. Only closed u2-type instances can create images.  

To create images for Windows instance, it is recommended to prepare for creating images by using Sysprep and then close the next instance.  For more details on sysprep, see [Guide for Windows Sysprep](#windows-sysprep). 

To create an image for Windows instance which is under execution,  prerequisite tasks are required if the Windows instance is made out of images dated before May 28 of 2019. Check the Windows version of the image which created an instance from **Image Name** of **Instance Details**. For more details, see [Guide to Create Windows Instance Images under Execution](#windows).

## Modify Images

Modify an image name.

Check **Protected** and update images, in order to prevent images from deleted by mistake. To delete an image where Protected is enabled, uncheck **Protected** in Modify Image and update an image.

## Share with Other Projects

Select a project to share images with and share.


## Guide to Windows Sysprep

To create a Windows image, an image must return to default, by deleting information included to the hardware and user, and be made available for creating an instance. The process can be executed with Sysprep, which is a system preparation utility provided by Microsoft to distribute Windows OS.

First, access a Windows instance and execute an Order Prompt at the manager's authority.
![[Figure 1 Execute Order Prompt 명령 프롬프트 실행]](http://static.toastoven.net/prod_infrastructure/compute/sysprep/001_170524_800px.PNG)

When the order prompt window pops, execute the following order:

	cd C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf
	C:\Windows\System32\Sysprep\sysprep.exe /generalize /oobe /shutdown /unattend:Unattend.xml

![[Figure 2. Execute Sysprep]](http://static.toastoven.net/prod_infrastructure/compute/sysprep/002_170524_800px.PNG)

When Sysprep is completed, Windows instance is automatically closed. When Windows instance is confirmed closed, user's Windows image can be created by using [Create Images](./console-guide/#_1).

### Option Details of Sysprep


`\generalize`

Remove the original system information registered in Windows. In this phase, Security ID (SID) is reset, while all system restoration points, as well as event logs are removed. After that, it is rebooted and the `Windows Configuration Phase` will be tried.   
> [Note]
In this phase, deleting SID and user information may affect operations of existing programs.  


`\oobe`
Reboot Windows and enter with a start mode, then apply pre-defined user settings, such as setting by nation, network location, language, and time zone.

`\shutdown`
Shut down Windows.

`\unattend`
Reinstall Windows, and restore the user settings as recorded in the previous phase. Here, Windows user information is registered, and drivers and products are updated, while additional software can be installed. Further user configuration is also available, adding to default settings provided by response files.     

> [Note]
Response files for Windows images that TOAST provides are located at C:\Program Files\Cloud Solutions\Cloudbase-Init\conf\Unattend.xml. As settings are all ready as required, no modification is necessary, except for special purposes.  


## Guide to Create Windows Instance Images under Execution 

When creating an image with Windows instance which is under execution, if the version of the original instance image is dated before May 28 of 2019, following tasks are required. 
Skip the following, however, if your Windows image version is dated after May 28 of 2019. 

1. Access Windows instance and execute **Task Scheduler** on the **App**. 
![[그림 3 Task Scheudler 실행]](http://static.toastoven.net/prod_infrastructure/compute/windows/001_190604.png)

2. Click **Create Task** from the **Actions** tab on the right of **Task Scheduler**. 
![[그림 4 Create Task 시작]](http://static.toastoven.net/prod_infrastructure/compute/windows/002_190604.png)

3. On the **Create Task** window, enter name for **Name** on the **General** tab; select **Run with highest privileges** of the **Security options**, and click **Change User or Group**. 
![[그림 5 User 변경 및 우선순위 설정]](http://static.toastoven.net/prod_infrastructure/compute/windows/003_190604.png)

4. In the box below **Enter the object name to select**, enter **SYSTEM** and click **OK**. 
![[그림 6 SYSTEM 유저 설정]](http://static.toastoven.net/prod_infrastructure/compute/windows/004_190604.png)

5. From the **Triggers** tab on the **Create Task** window, click **New** to create a new trigger. 
![[그림 7 Trigger 설정 1]](http://static.toastoven.net/prod_infrastructure/compute/windows/005_190604.png)

6. Select **At startup** for **Begin the task**, click **OK**, and trigger is completely created. 
![[그림 8 Trigger 설정 2]](http://static.toastoven.net/prod_infrastructure/compute/windows/006_190604.png)

7. On **Create Task**, go to **Actions** and click **New** to create a new action. 
![[그림 9 Action 설정 1]](http://static.toastoven.net/prod_infrastructure/compute/windows/007_190604.png)

8. Go to **New Action** and enter the value as below. 

```
	Program/script: C:\Windows\System32\ipconfig.exe
	Add arguments(optional): /renew
```

![[그림 10 Action 설정 2]](http://static.toastoven.net/prod_infrastructure/compute/windows/008_190604.png)

9. Click **OK** to close **New Action** and **Create Task** and complete with scheduler registration. 
