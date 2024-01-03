# Generally useful \*nix command cheatsheet 

**whoami** Prints the username with which you logged in

**pwd** Prints your 'present working directory.'

**ls** 'Lists' the files in a current directory by itself, or given a path will list the files in that directory.

-   Useful flags:

    -    -a: list all files, even 'hidden' files

    -   -h: make file sizes human readable (e.g. 4.0K instead of 4000)

    -   -l: print in tabular 'list' form

        `ls -l /home/brian/a_directory/`

        -   This would print out a list of the files in this directory, regardless of your present working directory.

**cd** 'Change directory' from your pwd to some target.

-   If the directory is nested within your pwd you can type a relative path. If it is not, you should type an absolute path.

    `cd /home/brian/a_directory`

-   Useful shortcuts for directory locations:

    -   \~: Your home directory (e.g. /home/USERNAME)

    -   ..: The directory above the current one (e.g. if you're in /home/USERNAME, .. refers to /home)

**cp** Makes a 'copy' of a file and places it in a target directory

-   This would create a new copy with the same name in a new directory:

    `cp filename.txt /home/brian/newDirectory/`

-   This would create a new copy with a different name in a new directory:

    `cp filename.txt /home/brian/newDirectory/differentname.txt`

**mv** 'Move' a file from its current location to a new location.

-   The syntax is similar to cp.

-   This is also a simple way to change the name of a file in its current directory.

    `mv filename.txt differentname.txt`

**rm** 'Removes' a file now and forever.

-   Useful flags:

    -   -i interactive mode, requires you to confirm each file deletion.

    -   r used with a target directory to delete all of its contents as well as the directory itself

**rmdir** 'Removes a directory' now and forever.

-   The directory must be empty. If you're really sure you want to delete a full directory, use rm -r or perhaps rm -ir as above.

**mkdir** 'Makes a directory.'

-   Make a directory called 'newDirectory' in my pwd:

    `mkdir newDirectory`

-   Make a directory called 'newDirectory' in a specified path:

    `mkdir /home/brian/specifiedPath/newDirectory/`

**touch** Creates an empty file with the specified name. Sometimes useful.

-   Create a new text file called newFile.txt

    `touch newFile.txt`

**chown** Changes the ownership of a file.

-   Change the owner of secretDocument.txt from its current owner to 'brian'

    `chown brian secretDocument.txt`

**chgrp** Changes the group ownership of a file.

-   Change the group ownership of secretDocument.txt from its current group to 'trustedGroup'

    `chgrp trustedGroup secretDocument.txt`

**chmod** Changes the permissions of a file.

-   There are three sets of permissions for a file: **U**ser, **G**roup, and **O**ther. The 'user' is the owner of the file, the 'group' is the group, and 'other' is anyone who is not the owner and not in the group.

-   For each set, there are three permissions: **R**ead, **W**rite, and e**X**ecute.

-   A block of permissions will be printed in three triplets, representing the three permissions for each of the three sets:

    -   rwxrw-r-- means that the user can read, write, and execute; the group members can read and write; and anyone else can only read the file.

-   To change permissions of a file, issue chmod with an argument specifying which set of permissions you want to modify, a + to add permissions or a - to remove permissions, and the permissions you want to modify.

    -   This will add permissions for the user and group to read and write to secretDocument.txt.

        `chmod ug+rw secretDocument.txt`

    -   This will remove permissions for anyone who is not the user or the group to read or write to secretDocument.txt.

        `chmod o-rw secretDocument.txt`

**cat** 'Concatenates' or prints the entire contents of a file to the screen (STDOUT).

-   If you accidentally print out the contents of a gigantic file and want to kill it, you can do so with Ctrl-C.

**head** Print the first n lines of a file.

-   The default behavior is n=10. This can be modified with the -n flag, as in

    `head -n 100 secretDocument.txt`

    which would print the first 100 lines of that file.

**tail** Print the last n lines of a file.

-   The default is n=10. This can also be modified with the -n flag, as above.

-   Additionally, you can use tail to drop the first line of a file, like this:

    `tail -n +2 secretDocument.txt`

    which can be useful if you want to drop the line containing column headings, for example.

**sort** Print sorted output.

-   Useful flags;

    -   -k Specifies the column number you want to sort by. You can specify multiple columns.

    -   -g Specifies that you want to sort using numbers based on scientific notation. This is generally how I sort numbers. There are many other flags for different variable types (alphabetic, date/time, etc).

**cut** Prints a specified set of input data to the screen.

-   Useful flags:

-   -f Specifies the column numbers you want to display. Separate multiple columns with commas, or type ranges of columns like '1-10'.
