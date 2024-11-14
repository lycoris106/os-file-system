# Project: File System

## Problems
### 1. Large Files (4 points)
#### Preliminaries
Change the `FSIZE` in `kernel/param.h` to 200000. This parameter controls the total number of blocks in xv6.

#### Description
In this problem you'll increase the maximum size of an xv6 file. Currently xv6 files are limited to 268 blocks, or 268*BSIZE bytes (BSIZE is 1024 bits in xv6). This limitation comes from the fact that an xv6 inode contains 12 "direct" block numbers and one "singly-indirect" block number, which refers to a block that holds up to 256 more block numbers, for a total of 12+256=268 blocks.

The `bigfile` command creates the longest file it can, and reports the size. The command is already there, so you can try to run:

```shell
$ bigfile
..
wrote 268 blocks
bigfile: file is too small
```

The test fails because `bigfile` expects to create a file with 66666 blocks, but unmodified xv6 limits files to 268 blocks.

Your task is to change the xv6 file system code to support large files. **You need to implement "doubly-indirect" blocks**, containing 256 addresses of singly-indirect blocks, each of which can contain up to 256 addresses of data blocks. By modifying one direct block into a "doubly-indirect" block, a file will be able to consist of up to 65803 blocks (256 x 256 + 256 + 11). It is 11 because we have to sacrifice one direct block number for doubly-indirect block.

It is still not sufficient to accomplish our goal. You will need to implement an extra indirect block to achieve 66666 blocks.

⚠️ `bigfile` may take a couple of minutes to run.



### 2. Symbolic Links to Files (4 points)
#### Description
In this exercise you will add symbolic links to xv6. Symbolic links (or soft links) refer to a linked file by pathname; when a symbolic link is opened, the kernel follows the link to the referred file. Symbolic links resembles hard links, but hard links are restricted to pointing to file on the same disk, while symbolic links can cross disk devices. Although xv6 doesn't support multiple devices, implementing this system call is a good exercise to understand how pathname lookup works.

You will implement the `symlink(char *target, char *path)` system call, which creates a new symbolic link at `path` that refers to a file named `target`. 

In addition, you also need to handle `open` when encountering symbolic links. When a process specifies `O_NOFOLLOW` flags, `open` should open symlinks (not targets). If the target is also a symbolic link, you must recursively follow it until a non-link file is reached. If the links form a cycle, you must return an error code. You may approximate this by returning an error code if the depth of links reaches some threshold (e.g., 10).


### 3. Symbolic Links to Directories
#### Description
Instead of just implementing symbolic links to files, now you should also consider symbolic links to directories. We expect that a symbolic link to a directory should have these properties:
1. It can be a part of a path, and will redirect to what it links to.
2. You can `cd` a symbolic link if it links to a directory.

For example, `symlink("/y/", "/x/a")` creates a symbolic link `/x/a` links to `/y/`. The actual path of `/x/a/b` should be `/y/b`. Thus, if you write to `/x/a/b`, yo actually write to  `/y/b`. Also, if you `cd` in to `/x/a`, your working directory should become `/y/`.

You can check `testsymlinkdir` function in `symlinktest.c`. You will get this bonus point if you pass all tests.




