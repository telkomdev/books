### Fix /$ cd ho-bash: cannot create temp file for here-document: No space left on device

First Check all partition:
```shell
$ df -h
```

The fastest way to locate your folders that are too full 
is by narrowing down the folder file size in levels from the root folder. 
You start with the root folder by:

```shell
sudo du -h --max-depth=1 /
```

Then - EITHER you increase the depth, i.e. the levels below:

```shell
sudo du -h --max-depth=2 /
```
OR - quicker - you you look which folder has eaten up the most disk space, and do the same on this folder:

```shell
sudo du -h --max-depth=1 /home/<user>/<overfull-folder>
```
Once you found it, just remove that one:

```shell
rm -rf <path to overfull-folder>
```

#### Error `df: unrecognized option '--max-depth=1'`

Try to replace `--max-depth=1` flag with `-d 1`
```shell
sudo du -h -d 1 /
```
