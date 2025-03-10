Here's a detailed blog-style guide on how to **move the Windows pagefile to another drive** when Windows keeps forcing it onto C: due to a hidden registry setting.  

---

# **How to Move Windows Pagefile to Another Drive (Even When It Keeps Using C:)**  

If you’ve ever tried to move your **pagefile** from the **C:** drive to another disk, you may have run into a frustrating problem: **Windows keeps ignoring your settings** and creates a temporary pagefile on C instead. Even if you set a **custom pagefile** on another drive (D:, E:, or Z:), Windows just **won't use it**.  

The issue? **A hidden registry setting (PagefileOnOsVolume) forces Windows to always use C:**, even if you disable the pagefile there.  

This guide will show you **exactly how to fix this**, so you can move your pagefile properly without Windows overriding your settings.  

---

## **Why Move the Pagefile to Another Drive?**  
Moving the pagefile off your **C:** (system) drive can:  
✅ Free up space on your SSD (especially if it's small).  
✅ Reduce wear on your primary drive (if it's an SSD).  
✅ Improve performance if you move it to a faster or dedicated disk.  
✅ Prevent Windows from making unnecessary temporary pagefiles.  

However, Windows **often refuses to move the pagefile** properly unless you **change a hidden registry setting**—especially if you've used **BitLocker encryption** in the past.  

---

## **Step 1: Check If Windows Is Forcing the Pagefile on C**  
Before making changes, let’s confirm if Windows is forcing a pagefile on C:  

1. **Open Command Prompt as Administrator**  
   - Press `Win + R`, type `cmd`, and press `Ctrl + Shift + Enter`.  

2. **Run this command to check pagefile locations:**  
   ```
   wmic pagefile list /format:list
   ```
   - If you see **C:\pagefile.sys** but not the one on your other drive (D: or E:), Windows is ignoring your settings.  

3. **Check Virtual Memory Settings**:  
   - Press `Win + R`, type `sysdm.cpl`, and hit **Enter**.  
   - Go to **Advanced → Performance → Settings → Advanced → Virtual Memory**.  
   - Check **Total paging file size for all drives**—if it doesn’t match what you set, Windows is ignoring your settings.  

If Windows keeps creating a **temporary pagefile on C:**, you need to change a **hidden registry setting**.  

---

## **Step 2: Change the Registry Setting to Allow Pagefile on Another Drive**  
Windows **forces the pagefile on C** if a registry key (`PagefileOnOsVolume`) is enabled. Even if you **disable BitLocker and decrypt the drive**, this key remains active.  

### **How to Disable Pagefile Enforcement on C:**  
1. **Open the Windows Registry Editor**  
   - Press `Win + R`, type `regedit`, and press **Enter**.  

2. **Navigate to the Registry Key:**  
   ```
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management
   ```

3. **Find `PagefileOnOsVolume`**  
   - If this key exists, **double-click it** and change the value to `0`.  
   - If it **doesn’t exist**, **right-click on an empty space** → **New → DWORD (32-bit) Value** → Name it `PagefileOnOsVolume` → Set it to `0`.  

4. **Close the Registry Editor and Restart Your PC**  

---

## **Step 3: Move the Pagefile to Another Drive**  
Now that Windows **won’t force the pagefile onto C**, let’s move it properly.  

### **1. Open Virtual Memory Settings**
- Press **Win + R**, type `sysdm.cpl`, and press **Enter**.  
- Go to **Advanced → Performance → Settings → Advanced → Virtual Memory**.  
- Click **Change** under "Virtual memory."  

### **2. Disable Pagefile on C**
- **Uncheck** "Automatically manage paging file size for all drives."  
- Select **C:** and choose **No paging file** → Click **Set**.  

### **3. Enable Pagefile on Another Drive (D: or E:)**
- Select **D:** (or whichever drive you want).  
- Choose **Custom size**.  
- Set **Initial size = 16GB (16384MB)** and **Maximum size = 32GB (32768MB)**.  
- Click **Set**.  

### **4. Apply Changes and Restart Your PC**
- Click **OK** → **Apply** → Restart your computer.  

---

## **Step 4: Verify the Pagefile is Using the Correct Drive**  
After restarting, check that Windows is now using the correct drive.  

### **1. Check in Command Prompt**  
Open **Command Prompt (Admin)** and type:  
```
wmic pagefile list /format:list
```
✅ If you **only see D:\pagefile.sys (or your selected drive)** and **not C:\pagefile.sys**, it worked!  

### **2. Check in Task Manager**  
- Open **Task Manager (`Ctrl + Shift + Esc`) → Performance → Memory**.  
- Check **Committed memory**—it should now include your **D: pagefile** size.  

### **3. Check in File Explorer**  
- Open **File Explorer**, enable **Hidden Items & Protected OS Files**, and check if `pagefile.sys` is now on **D:** instead of C.  

---

## **Troubleshooting (If Windows Still Uses C:)**  
If Windows **still creates a temporary pagefile on C:** after reboot:  

✅ **Make sure the registry setting (`PagefileOnOsVolume`) is still set to `0`.**  
✅ **Manually delete `C:\pagefile.sys`** (you may need to do this in **Safe Mode**).  
✅ **Check BitLocker settings:** If BitLocker is enabled, try disabling it before redoing the steps.  
✅ **Ensure your D: or E: drive has enough free space** (Windows may refuse to move the pagefile if space is too low).  

---

## **Final Thoughts**  
This registry fix allows **full control over where Windows places the pagefile**, solving a frustrating issue where Windows **refuses to use another drive**. If you’ve been struggling to **move your pagefile**, this method ensures Windows **won’t override your settings** anymore.  

### **Key Takeaways:**  
✔ **The "PagefileOnOsVolume" registry key forces Windows to use C:** (even if you disable it).  
✔ **Setting this key to `0` finally lets you move the pagefile to another drive**.  
✔ **Disabling the pagefile on C and setting it on another drive (D or E) now works as expected**.  

Now you can **free up space on your C: drive** and move the pagefile where you actually want it! 🚀  

**Let me know if this worked for you!**