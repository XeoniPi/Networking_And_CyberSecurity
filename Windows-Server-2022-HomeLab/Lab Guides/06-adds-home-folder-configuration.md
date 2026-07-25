# Lab Guide: Active Directory Home Folder Configuration (Server & Client)

This guide covers the deployment of Home Folders in Windows Server 2022 Active Directory Domain Services (AD DS). It details why companies use them, how to set up the shared infrastructure, assign paths to users, and understand key permission rules.

---

## 1. What is a Home Folder and Why Use It?
A **Home Folder** is a dedicated private network storage directory assigned to a specific Active Directory user. Instead of saving files locally on a single machine's local drive (like `C:\Users\username\Documents`), files are saved directly onto a centralized file server network path.

### Core Reasons for Deployment:
* **Roam Anywhere Data:** Users can log into any corporate client computer on the domain and instantly access their personal work files.
* **Centralized Enterprise Backups:** IT staff can backup all user data by copying one single shared drive folder on the host server instead of running backups on hundreds of individual physical client machines.
* **Data Security:** If a physical client machine breaks or is stolen, no confidential company files are lost because the files live securely on the Server storage array.

---

## 2. Server Side Configuration: Creating the Base Share

To host personal folders, you must first create a root directory on the Server with specific network and security rules.

### Step A: Create and Share the Directory
1. Open File Explorer on your Windows Server.
2. Create a new folder on your storage drive (e.g., `C:\xeonipi`).
3. Right-click the **`xeonipi`** folder -> Select **Properties**.
4. Go to the **Sharing** tab -> Click **Advanced Sharing...**.
5. Check the box for **Share this folder**.
6. Click **Permissions** -> Ensure **Everyone** has **Full Control** (NTFS permissions will restrict actual entry later). Click **OK** -> **Apply**.

### Step B: Configure NTFS Security Settings
To prevent users from snooping into other employees' directories, the root path requires custom security inheritance rules.

1. In the same folder properties, switch to the **Security** tab -> Click **Advanced**.
2. Click **Disable inheritance** -> Choose **"Convert inherited permissions into explicit permissions on this object"**.
3. Edit the permissions so that:
   * **Administrators / SYSTEM:** Have **Full Control** *(This allows network backups)*.
   * **Authenticated Users / Domain Users:** Have **Read / List Folder Contents** and **This Folder Only** permissions. *(This allows them to connect to the root directory without viewing other users' private sub-folders).*
4. Click **Apply** and **OK**.

---

## 3. Active Directory Side Configuration: Assigning Paths to Users

Active Directory can automatically build and provision customized sub-folders for each user using a dedicated environment variable string.

1. Open **Server Manager** -> **Tools** -> **Active Directory Users and Computers**.
2. Click your **Users** container.
3. Select your lab users (e.g., `santo` and `nishad`). *Tip: You can select multiple users at once to apply settings in bulk!*
4. Right-click the selected user(s) -> Select **Properties**.
5. Go to the **Profile** tab.
6. Locate the **Home folder** section at the bottom:
   * Select the **Connect** radio button.
   * Choose a drive letter from the dropdown menu (Standard enterprise choice is usually **`H:`**).
   * In the **To:** input box, type the network variable path:
     ```
     \\192.168.56.10\xeonipi\%username%
     ```
7. Click **Apply**, then click **OK**.

> 💡 **The `%username%` Magic:** Active Directory automatically replaces the `%username%` string with the real login name of the individual user account (e.g., creating `\\192.168.56.10\xeonipi\santo` and `\\192.168.56.10\xeonipi\nishad`). It also applies restrictive NTFS permissions so **only** that specific user can open their own folder.

---

## 4. Client Side Verification: Access Testing

1. Boot up your **Client PC** and log in using a domain user account (e.g., Username: `santo` | Password: `ABC.123`).
2. Open **File Explorer** and click **This PC**.
3. Under the **Network locations** section, you will see a newly mapped drive labeled:
   * `santo (\\192.168.56.10\xeonipi) (Z:)`
4. Double-click the **H:** drive. Create a test document file inside it.
5. Log out of the client computer and log in as the other domain user (`nishad`). Check **This PC**; you will notice `nishad` sees their own blank `H:` drive and cannot access or view `santo`'s test document.

---

## 5. Architectural Comparison: Home Folder vs. Mapped Network Drive

| Feature | Home Folder | Mapped Network Drive |
| :--- | :--- | :--- |
| **Primary Purpose** | Personal private storage space for individual work files. | Shared collaborative space for entire teams/departments. |
| **Target Path** | Points to a unique user subdirectory (`...\xeonipi\santo`). | Points to a broad common root folder (`...\xeonipi\Marketing`). |
| **Permissions** | Strictly private; only the owner and domain admins have entry. | Group permissions; everyone in the department can read/write. |
| **Configuration** | Setup individually inside the Active Directory **User Profile** tab. | Deployed globally to many computers using **Group Policy (GPO)**. |

---

## 6. Home Folder Benefits: Advantages vs. Disadvantages

### Advantages (Pros)
* **Zero Local Footprint:** Hard drives on user laptops do not fill up with large work files.
* **Easy Hardware Lifecycle:** If an employee's computer breaks, you can give them a brand-new machine immediately. They log in and lose zero data.
* **Automated Creation:** Using `%username%` saves IT administrators from manually creating and setting permissions on directories for thousands of users.

### Disadvantages (Cons)
* **Single Point of Failure:** If the central file server goes down, crashes, or loses power, no user on the domain can open or work on their files.
* **Network Speed Dependency:** If the host adapter network interface is slow, opening or saving large files over the virtual switch takes more time compared to local drives.
* **Storage Cost:** Servers require robust, expensive, and redundant hard drive arrays (RAID) to manage large volumes of user data safely.
