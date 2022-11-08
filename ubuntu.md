# Steps For User and Group Provisioning

1.  Insure you Create a Group That Gives Read/Write Access to Everyone
    in the Group. This Group Will Allow Everyone to Share and Create New
    Work. Let the Group Name Be the Same as Your Lab Group

    a.  Example For han_lab we have: sudo addgroup han_lab

    b.  For my box I have the tony_lab group

# User Provisioning

1.  You will need to decide whether to assign "Sudo" permissions to
    certain users or whether you wish to have that belong only to the
    "root" user. This permission is very useful for installing software
    packages.

2.  For standard users we want to have their home directories in
    /mnt/data1

    a.  First Create Directory /mnt/data1/home as "mkdir
        /mnt/data1/home"

    b.  Then create owernship as: chown root:han_lab /mnt/data1/home

    c.  Create a Standard User "tony" for the group "tony_lab" with a
        home directory at /mnt/data1/home do the following:

sudo useradd -m -g tony_lab -d /mnt/data1/home/tony tony

3.  Check that ownership for the new user is correct:

> root@adminbox:/mnt/data1/home# ls -l
>
> drwxr-x\-\-- 2 tony tony_lab 5 Nov 3 04:29 tony

So the folder tony is owned by "tony" and has the group "tony_lab" but
notice that only execute permission belongs to the group and all the
other permissions belong to the user "tony" The above is the "710" Octal
permission. You can change it by using the chmod command and figuring
your octal from this link: [chmod
calculator](https://chmod-calculator.com/)

a.  Now set User Password

b.  As Root Type: passwd username (username=tony as example)

c.  The automated steps will let you complete the task


4.  Scratch Space Permissions

    a.  We want to create the same structure in scratch as we have for
        /data1

    b.  As root go to /mnt/scratch and type: mkdir home

    c.  Then type: chown root:groupname home where groupname is your lab
        group such as han_lab.

    d.  Do the ls -l and you will see:

> root@adminbox:/mnt/scratch# ls -l
>
> drwxr-xr-x 2 root tony_lab 2 Nov 3 05:15 home

e.  Now we can create our user's directory under home

f.  cd /mnt/scratch/home

g.  As root: mkdir username example mkdir tony

h.  Change Ownership chown -R tony:tony_lab tony

i.  Change Permissions to only allow the user to Read/Write the folder

    i.  chmod -R 710 tony

    ii. You will see: drwx\--x\-\-- 2 tony tony_lab 2 Nov 3 05:21 tony



5.  Remote Storage Folders and Permissions

    a.  The same pattern is repeated for this mounted space as the
        scratch space

    b.  After creating the home directory and user directory you will
        see:

> root@adminbox:/mnt/storage/home# ls -l
>
> drwx\--x\-\-- 2 tony tony_lab 4096 Nov 3 05:37 tony

6.  Organizing Folders for Users and Groups

We have discussed the creation of user home folders and that the default
mask should create a sufficient security mask.

The next thing to consider is how the group can share information with
each other.

A folder tree for the shared folder might look like:

![image1](https://user-images.githubusercontent.com/1626000/200649058-b4701c3d-f065-493c-aee6-53422d238fcf.png)


Using the following command below we have placed the file mask 764:

-   chmod -R 764 shared

-   The mask allows:

    -   Root full Access and Root is the Owner

    -   The Group tony_lab has read and write permission

    -   The Public has read permission

7.  Installation of software packages

    a.  As a general rule installing software packages such as R and
        Python will by default occur in the root disk directory which is
        /. This will not be an issue since every root disk has 512 GB of
        space.

    b.  If for some reason you are installing a package that has an
        accompanying dataset that is greater than 40 GB make sure it is
        installed in your /mnt/data1 directory as its root directory.

        i.  Generally this should not happen. Although applications can
            use large datasets, the application itself should let you
            install the dataset on a separate mount point like/mnt/data1
