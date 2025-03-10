Certainly! Here's a complete and detailed guide for moving **both Docker and WSL (Ubuntu)** data from the `C:` drive to the `D:` drive, including all steps, commands, and explanations.

* * * * *

Moving Docker and WSL (Ubuntu) Data to Another Drive
====================================================

This guide provides comprehensive, step-by-step instructions to move Docker and WSL (Ubuntu) data from `C:` to `D:` using symbolic links to maintain compatibility.

* * * * *

Prerequisites
-------------

1.  **Open PowerShell as Administrator**: Right-click on the Start menu, select **Windows PowerShell (Admin)**.
2.  **Back up important data**: Ensure you have any important data backed up before proceeding.

* * * * *

Part 1: Moving Docker Data to `D:`
----------------------------------

### Step 1: Move Docker Folder to the New Drive

1.  **Navigate to Docker's Current Data Location**:
    -   Open **File Explorer** and go to `C:\Users\Mrnone\AppData\Local\Docker`.
2.  **Move the Entire `Docker` Folder**:
    -   Cut and paste the `Docker` folder to `D:\Docker`. The result should be:

        kotlin

        Copy code

        `D:\Docker\wsl\data`

### Step 2: Recreate Docker Folder Structure on `C:`

1.  **Open Command Prompt as Administrator**:
    -   Press `Win + R`, type `cmd`, then press `Ctrl + Shift + Enter` to open Command Prompt with Administrator privileges.
2.  **Run the Command to Recreate the Folder Structure**:
    -   In Command Prompt, enter:

        cmd

        Copy code

        `mkdir "C:\Users\Mrnone\AppData\Local\Docker\wsl"`

### Step 3: Create a Symbolic Link for Docker Data

1.  **Create the Symbolic Link**:
    -   In the same Command Prompt window, run:

        cmd

        Copy code

        `mklink /D "C:\Users\Mrnone\AppData\Local\Docker\wsl\data" "D:\Docker\wsl\data"`

    -   This creates a symbolic link pointing from the original location on `C:` to the new location on `D:`.

### Step 4: Verify the Docker Symbolic Link

1.  **Check the Link**:
    -   In **File Explorer**, go to `C:\Users\Mrnone\AppData\Local\Docker\wsl` and verify that `data` shows as a shortcut to `D:\Docker\wsl\data`.

### Step 5: Restart Docker

1.  **Open Docker Desktop** to ensure it starts without errors.
    -   If it runs successfully, Docker is now using `D:\Docker\wsl\data` for storage.

* * * * *

Part 2: Moving WSL (Ubuntu) Data to `D:`
----------------------------------------

### Step 1: Export the WSL (Ubuntu) Distribution

1.  **Run the Export Command**:
    -   In **PowerShell (Admin)**, export your WSL distribution (e.g., Ubuntu) to a `.tar` file:

        powershell

        Copy code

        `wsl --export Ubuntu D:\WSLBackup\Ubuntu.tar`

    -   This command creates a file named `Ubuntu.tar` at `D:\WSLBackup` with all data from your Ubuntu WSL distribution.
2.  **Confirm the `.tar` File**:
    -   Check that `Ubuntu.tar` is successfully created in `D:\WSLBackup`.

### Step 2: Unregister the WSL Distribution

1.  **Remove the Current Ubuntu Installation**:
    -   In the same PowerShell window, run:

        powershell

        Copy code

        `wsl --unregister Ubuntu`

    -   This removes Ubuntu from WSL (but not the `.tar` file you created).

### Step 3: Import Ubuntu to `D:`

1.  **Import Ubuntu Using the `.tar` File**:
    -   Import Ubuntu back into WSL, specifying the new location on `D:`:

        powershell

        Copy code

        `wsl --import Ubuntu D:\WSL\Ubuntu D:\WSLBackup\Ubuntu.tar --version 2`

    -   This imports Ubuntu to `D:\WSL\Ubuntu`, creating a new `ext4.vhdx` file in `D:\WSL\Ubuntu`.

### Step 4: Verify the New WSL Data Location

1.  **Check for `ext4.vhdx`**:
    -   Open **File Explorer**, navigate to `D:\WSL\Ubuntu`, and confirm the presence of `ext4.vhdx`, which now contains your Ubuntu WSL data.

* * * * *

Part 3: Create a Symbolic Link for WSL (Ubuntu) Data
----------------------------------------------------

### Step 1: Recreate the Folder Structure on `C:`

1.  **Open Command Prompt as Administrator**:
    -   If not already open, press `Win + R`, type `cmd`, and press `Ctrl + Shift + Enter`.
2.  **Create the Necessary Folder Structure**:
    -   Run the following command to recreate the Ubuntu folder structure on `C:`:

        cmd

        Copy code

        `mkdir "C:\Users\Mrnone\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState"`

### Step 2: Create a Symbolic Link for Ubuntu Data

1.  **Create the Symbolic Link**:
    -   In the Command Prompt, create a link pointing from `C:` to `D:`:

        cmd

        Copy code

        `mklink /D "C:\Users\Mrnone\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState\ext4.vhdx" "D:\WSL\Ubuntu\ext4.vhdx"`

    -   This ensures WSL finds your Ubuntu data on the `D:` drive.

### Step 3: Verify the Ubuntu Symbolic Link

1.  **Check the Link**:
    -   In **File Explorer**, go to `C:\Users\Mrnone\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState` and verify that `ext4.vhdx` appears as a shortcut to `D:\WSL\Ubuntu\ext4.vhdx`.

### Step 4: Start WSL and Test

1.  **Run Ubuntu**:
    -   In PowerShell, start your Ubuntu distribution:

        powershell

        Copy code

        `wsl -d Ubuntu`

    -   If Ubuntu starts without issues, WSL is now successfully using `D:\WSL\Ubuntu\ext4.vhdx`.

* * * * *

Summary
-------

After following these steps, both Docker and WSL (Ubuntu) data are moved to the `D:` drive, with symbolic links on the `C:` drive to maintain compatibility.