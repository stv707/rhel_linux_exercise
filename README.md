# rhel_linux_exercise

# Soft Links and Hard Links in Linux (RHEL)

## Exercise: Understanding Soft and Hard Links in Linux

### **Objective**
This exercise will help students gain a deep understanding of **soft links (symbolic links)** and **hard links**, including their real-life applications in the Linux filesystem.

### **Prerequisites**
- A RHEL system
- A spare disk available at `/dev/vdb`
- Root or sudo access

### **Part 1: Setup and Understanding the Basics**
#### **Step 1: Create a New Filesystem on /dev/vdb**
Before we begin working with links, let’s format the spare disk and mount it:

```bash
sudo mkfs.xfs /dev/vdb
sudo mkdir /mnt/testdisk
sudo mount /dev/vdb /mnt/testdisk
```

#### **Step 2: Create a Sample File**
Navigate to the mounted disk and create a test file:

```bash
cd /mnt/testdisk
echo "This is a test file" > testfile.txt
ls -li testfile.txt
```

This will show the inode number of `testfile.txt`, which we will use later to compare hard and soft links.

---

### **Part 2: Hard Links**
#### **Step 3: Create a Hard Link**
A **hard link** is another name for a file that shares the same inode. Let’s create one:

```bash
ln testfile.txt hardlink.txt
ls -li
```

Observe that `testfile.txt` and `hardlink.txt` have the same inode number. This means they are actually the same file.

#### **Step 4: Modify the File and Check Changes**
Modify `hardlink.txt` and see if `testfile.txt` is affected:

```bash
echo "Adding more data to the file" >> hardlink.txt
cat testfile.txt
```

Since both files share the same inode, modifications to one are reflected in the other.

#### **Step 5: Delete the Original File**
Now, delete `testfile.txt` and check if `hardlink.txt` is still accessible:

```bash
rm testfile.txt
ls -li
cat hardlink.txt
```

Since a hard link is an exact reference to the file’s inode, the file content remains accessible via `hardlink.txt`.

---

### **Part 3: Soft Links (Symbolic Links)**
#### **Step 6: Create a Soft Link**
A **soft link (symbolic link)** is just a pointer to another file.

```bash
echo "This is a new test file" > testfile2.txt
ln -s testfile2.txt softlink.txt
ls -li
```

Notice that `softlink.txt` has a different inode and shows an arrow pointing to `testfile2.txt`.

#### **Step 7: Modify the Soft Link Target**
Try modifying the original file:

```bash
echo "More data for symlink test" >> testfile2.txt
cat softlink.txt
```

The soft link points to the original file, so changes in the original reflect in the soft link.

#### **Step 8: Delete the Original File and Check the Link**
Now, delete `testfile2.txt` and check `softlink.txt`:

```bash
rm testfile2.txt
ls -li
cat softlink.txt
```

Unlike a hard link, the soft link is broken because it only pointed to `testfile2.txt`, which no longer exists.

---

### **Part 4: Real-Life Use Cases of Links**
#### **Use Case 1: Version Control with Hard Links**
Hard links are useful for version control. Suppose you maintain configuration files:

```bash
ln /etc/hosts /mnt/testdisk/hosts_backup
cat /mnt/testdisk/hosts_backup
```

Even if the original `/etc/hosts` is modified, the backup remains accessible because it shares the same inode.

#### **Use Case 2: Soft Links for Shared Libraries**
Soft links are commonly used in shared libraries. Check how libraries use them in `/lib64`:

```bash
ls -l /lib64/libc.so.6
```

You’ll see that `libc.so.6` is a symbolic link pointing to a specific version of the library.

---

### **Summary**
| Feature         | Hard Link | Soft Link |
|---------------|----------|----------|
| Inode Number | Same as original file | Different |
| Works Across Filesystems | No | Yes |
| File Integrity After Deletion | Remains | Broken |
| Can Link to Directories | No | Yes |

This exercise should give you a solid understanding of how **hard and soft links** work in Linux, along with practical applications.

### **Cleanup**
To clean up your system after this exercise:

```bash
rm -f /mnt/testdisk/hardlink.txt /mnt/testdisk/softlink.txt
umount /mnt/testdisk
sudo rm -rf /mnt/testdisk
```

---

### **Next Steps**
- Experiment with hard links on different filesystems.
- Test using soft links in scripts and applications.
- Explore links in `/etc`, `/usr/bin`, and `/lib64` to see real-life implementations.