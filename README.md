# Kali Linux Full-Screen Fix in VirtualBox

This repository documents how I diagnosed and fixed a Kali Linux virtual machine running in Oracle VirtualBox that was stuck at a low resolution (800×600) even after installing Guest Additions.

The project focuses on **Linux system troubleshooting, virtualization drivers, and display configuration**.

## 🔍 Root Cause Analysis — Video Memory Limitation

During troubleshooting, VirtualBox was configured with only **16 MB of video memory**, which severely limited the maximum resolution available to the guest operating system. 

Even after installing Guest Additions and loading the correct kernel modules (`vboxvideo` and `vboxguest`), Kali continued to boot into a low

800×600 display mode. Increasing the VM’s video memory to **128 MB** unlocked high-resolution display modes and allowed the X server to properly switch resolutions once prompted via

`xrandr`. This change was a critical prerequisite for enabling full-screen mode and automatic display resizing.

---

## 🖥️ Environment

- Host OS: Windows 11  

- Hypervisor: Oracle VirtualBox  

- Guest OS: Kali Linux (XFCE Desktop)  

- Graphics Controller: VMSVGA  

- Video Memory: 16 MB 

---

## ❌ Problem

After installing Kali Linux:

- Full-screen mode did not work  

- Auto-resize guest display had no effect  

- Resolution remained stuck at 800×600  

- VM window stayed small on a high-resolution monitor. Example:
<img width="800" height="600" alt="Screenshot_2026-01-24_22-48-11" src="https://github.com/user-attachments/assets/058d9f92-fa0b-4150-a42e-51f7e428e959" />

---

## ✅ Step 1 — Increase Video Memory

VirtualBox Manager → Kali → Settings → Display

- Set **Video Memory** to 128 MB 

- Graphics Controller: VMSVGA  

---

## ✅ Step 2 — Insert Guest Additions CD

While the VM was running:

Devices → Insert Guest Additions CD Image

This mounts a virtual ISO inside Kali that contains VirtualBox-specific drivers.

![Guest Additions CD](images/guest-additions-cd.png)

<img width="800" height="600" alt="Screenshot_2026-01-24_23-06-24" src="https://github.com/user-attachments/assets/be21a63c-2623-48fa-95db-446367889aa1" /> 
(Guest Additions successfuly inserted the CD See VBox_GAs_...Icon)

---

## ✅ Step 3 — Install Guest Additions from the mounted ISO
After inserting the Guest Additions CD image, Kali automatically mounted the virtual ISO under '/Downloads/'.
I navigated to the mounted directory and executed the installer script directly:


# Locate the mounted Guest Additions ISO
ls /Downloads/

# Run the installer script
sudo /Downloads/VBoxLinuxAdditions.run

# Reboot to load the new kernel modules and drivers
sudo reboot

## ✅ Step 4 - Change Resultion from 800x600 using xrandr
After installing Guest Additions and rebooting, Kali still launched at a low default resolution of **800x600**, and full-screen mode did not immedietly work.

To diagnose this, I queried the display server for available modes using xrandr
<img width="1920" height="1080" alt="Screenshot_2026-01-24_23-14-23" src="https://github.com/user-attachments/assets/91c88343-9ba4-43d0-acec-08e591e1baa1" />

I manually switched to a higher resolution:
'''bash
xrandr --output Virtual-1 --mode 1920x1080
<img width="1920" height="1080" alt="Screenshot_2026-01-24_23-14-42" src="https://github.com/user-attachments/assets/d258412d-b578-45d1-a29a-62645bc988c2" />

Once the display resized successfully, enabling
View -> Auto-resize Guest Display allowed the VM to dynamically adjust to full screen mode going foward. 

<img width="1920" height="1080" alt="Screenshot_2026-01-24_23-45-11" src="https://github.com/user-attachments/assets/69241359-86c0-4b42-8eee-d10ef9a015dd" />

