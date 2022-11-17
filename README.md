# learn-Linux-admin_managing-permissions

<!--
| Command | Description |
| --- | --- |
|     |     |
|     |     |
|     |     |
|     |     |
-->
## Permissions File
```
r : Read a file Ex. # cat <file name>
    Read a directory contents Ex. ls /dir
w : Write -->Create, delete or modify the file contents, 
    Create, delete or modify the files in a directory
x : Execute -->Go to inside the directory Ex. # cd /dir,Not required for file. It is required only for scripting files
```
ls [OPTIONS] FILE|DIRECTORY
- ls -l Filename
```
d rwx rwx rwx . 2 root root 6 Dec17 18:00 Filename
d ----> type of file
```
      there are 7 types of files
  ```
    - -----> regular file
    d -----> directory
    c ----->character device file (Ex. console file, open and close terminals, ...etc.,)
    b ----->block device file (Ex. device blocks like hard disks, CD/DVD disks)
    s -----> socket file (programmers will deal this file)
    p -----> pipe file (programmers will deal this file)
    l ----->linked file (nothing but short cut file)
  ```
```
rwx ----> owner permissions
rwx ----> group permissions
rwx ----> others(or world) permissions
. ---> No ACLs permission applied
root----> owner of the file
root ----> group ownership
6 -----> size of the file
Dec 7 18:00 -----> Date and Time of the created or modified File name -----> File name of that file
# ls -ld <directory name> (to see the long listing of the directories)
```
- ls -l /etc/passwd
```
-rw-r--r-- 1 root 3056 Sep 17 07:57 /etc/passwd
```
  ```
    -rw-r--r--: The file access permissions
    1: The number of hard links
    root: The user who is the owner of the file
    root: The group that is the owner of the file
    3056: The size of the file
    Sep: The month the file was created
    17: The day of the month the file was created
    07:57: The time of day the file was created
    /etc/passwd: The filename
  ```
 Here are the permission attributes with their respective octal values: 
  ```
    r: Read permission; 2 ^ 2 = 4 (bit 2 set)
    w: Write permission: 2 ^ 1 = 2 (bit 1 set)
    x: Execute permission: 2 ^ 0 = 1 (bit 0 set)
    -: No permission: 0 (no bits set)
  ```
 -rw-r--r--, as follows:
 ```
    -: The first character (byte) denotes the file type (a regular file, in our case).
    rw-: The next three-character sequence indicates the user owner permissions (in our case, read (r); write (w); octal value = 4 (r) + 2 (w) = 6 (rw)).
    r--: The next 3-byte sequence defines the group owner permissions (in our case, read (r); octal value = 4 (r)).
    r--: The last three characters denote the permissions for all other users in the system (in our case, read (r); octal value = 4 (r)).
```
## Example :
```
    rwx (777): Read, write, execute for all users including owner, group, and world.
    rwx r-x (755): Read, execute for all users; the file owner has write permissions.
    rwx r-x --- (750): Read, execute for owner and group; the owner has write permissions while others have no access.
    rwx --- --- (700): Read, write, execute for owner; everyone else has no permissions.
    rw- rw- rw- (666): Read, write for all users; there are no execute permissions
    rw- rw- r-- (664): Read, write for owner and group; read for others.
    rw- rw- --- (660): Read, write for owner and group; others have no permissions.
    rw- r-- r-- (644): Read, write for owner; read for group and others.
    rw- r-- --- (640): Read, write for owner; read for group; no permissions for others.
    rw- --- --- (600): Read, write for owner; no permissions for group and others.
    r-- --- --- (400): Read for owner; no permissions for others.
```
## The setuid permission
https://en.wikipedia.org/wiki/Setuid

With the setuid bit set, when an executable file is launched, it will run with the privileges of the file owner instead of the user who launched it. For example, if the executable is owned by root and launched by a regular user, it will run with root privileges. The setuid permission could pose a potential security risk when used inadequately, or when vulnerabilities of the underlying process could be exploited.

In the file access permission field, the setuid bit could have either of the following representations: 
```
    s replacing the corresponding executable bit (x) (when the executable bit is present)
    S (capital S) for a non-executable file
    
    For whom we change permissions: u = user (owner), g = group, o = others
    How we change permissions: + = add, - = remove, = = exactly as is
    What permission do we change: r = read, w = write, x = execute
```
chmod u+s myscript --->  -rwsrwxr-x (4775)

## The setgid permission

While setuid controls user impersonation privileges, setgid has a similar effect for group impersonation permissions.

An executable file with the setgid bit set runs with the privileges of the group that owns the file, instead of the group associated with the user who started it. In other words, the GID of the process is the same as the GID of the file.

When used on a directory, the setgid bit changes the default ownership behavior, in a way that files created within the directory will have group ownership of the parent directory, instead of the group associated with the user who created them. This behavior could be adequate in file-sharing situations when files can be changed by all users associated with the parent directory's owner group.

The setgid permission can be set via the following chmod command (for example, for the myscript executable file):

  chmod g+s myscript -->  -rwxrwsr-x (2775)
  
 ## More Examples:
 - chmod o+w myfile
 - chmod u-rw myufile
 - chmod u-r,ug-w,o-rwx myfile
 - chmod 777 myfile
 - chown [OPTIONS] [OWNER][:[GROUP]] FILE
 - sudo chown julian:developers myfile
 - sudo chown -R julian:julian mydir/
 - chgrp [OPTIONS] GROUP FILE
 - sudo chgrp developers myfile
 
## Using umask
The umask command is used to view or set the default file mode mask in the system. The file mode represents the default permissions for any new files and directories created by a user. For example, the default file mode masks in Ubuntu are given here:
```
    0002 for a regular user
    0022 for the root user
```
As a general rule in Linux, the default permissions for new files and directories are calculated with the following formulas:
```
    0666 – umask: For a new file created by a regular user
    0777 – umask: For a new directory created by a regular user
```
According to the preceding formula, on Ubuntu we have the following default permissions:
```
    File (regular user): 0666 – 0002 = 0664
    Directory (regular user): 0777 – 0002 = 0775
    File (root): 0666 – 0022 = 0644
    Directory (root): 0777 – 0022 = 0755
```
