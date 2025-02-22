Stage 2: Loading a File and Verifying Disk Allocation
-----------------------------------------------------

Overview:
In this stage, we load a manually created file (`sample.dat`) into the XFS file system and verify that disk blocks are correctly allocated to it.

Steps and Commands Used:

- Loading `sample.dat` into the XFS File System
  load --data sample.dat  (Executed in the XFS interface)
  - This command loads `sample.dat` into the disk, assigning a disk block to it.

- Verifying Disk Allocation
  df  (Executed in the XFS interface)
  - The `df` command checks the disk free list, showing whether a block has been allocated to `sample.dat`.

- Checking the Inode Table Entry
  copy 3 4 $HOME/myexpos/inode_table.txt  (Executed in the XFS interface)
  - This command copies the inode user table data into `inode_table.txt` inside the `myexpos` folder.

  dump --inodeusertable  (Executed in the XFS interface)
  - This command outputs the inode user table data directly into `inodeusertable.txt` inside the XFS interface folder.

Difference Between the Two Commands:
  - `copy 3 4 $HOME/myexpos/inode_table.txt` stores the file in the `myexpos` directory.
  - `dump --inodeusertable` outputs the data into `inodeusertable.txt` inside the XFS interface folder.

- Copying and Viewing Data Blocks from the XFS Disk
  copy 69 69 $HOME/myexpos/data.txt  (Executed in the XFS interface)
  - This command copies the data blocks from the XFS disk and displays them as a UNIX file (`data.txt`) in `myexpos`.

