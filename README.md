Variation on du that is easier to read, and should make it easier to find where bulky things are:

```
    # file-count /home -S 100M
      #FILES   #DIRS       ASIZE            DUDIFF         PATH
        4428      63     17G / 16Gi     +13.4M / +12.8Mi   /home/repository
        5373     477    2.5G / 2.3Gi      +31M / +30Mi     /home/scarfboy
       10045     734     20G / 19Gi       +40M / +38Mi     /home
		 
    # file-count /pool/postgres -d
     #FILES   #DIRS       ASIZE             DUDIFF         PATH
       1053      22    8.1G / 7.5Gi      -6.6G / -6.1Gi    /zzz/postgresql/9.3
        855      26     39M / 37Mi       +3.3M / +3.2Mi    /zzz/postgresql/9.5
       1910      50    8.1G / 7.6Gi      -6.6G / -6.1Gi    /zzz/postgresql
```

For example:
* reports number of files and number of directories
* reports both base-1000 and base-1024 numbers. Mostly because I got tired of explaining the difference at work.
* by default doesn't produce output for 2 directories deeper than the directory we started in
* optionally filters out small-fry directories, e.g. -S 100M in the first example.
* optionally sorts by size
* reports apparent size, as well as difference in actualy disk use (which is usually only slightly higher due to filesystem overhead, but can be lower e.g. around sparse files or ZFS compression).


Does not count symlinks.

## USAGE
```
Usage: file-count [options]

Options:
  -h, --help            show this help message and exit
  -n DEPTH, --depth=DEPTH
                        the maximum amount of directory depth to print,
                        relative to starting point. Default is 2, which should
                        not be so overwhelming.
  -C MINCOUNT, --mincount=MINCOUNT
                        don't show directories with fewer files than this
  -e EXTRADIGITS, --extradigits=EXTRADIGITS
                        Default is to show short overviewy sizes, you can ask
                        for more digits
  -S MINSIZE, --minsize=MINSIZE
                        don't show directories with file size summing to less
                        than this (note: understands values like 1.5M)
  -s, --sort-size       sort output by size (postpones output)
  -f, --sort-filecount  sort output by file count (postpones output)
  -d, --sort-dircount   sort output by directory count (postpones output)
```


## TODO:
 - clarify what exactly we do around links
 - deal with being handed multiple directories (?) 

## CONSIDERING:
 - 'stay on one device' feature
