A slightly more informative variation on `du`

For example:
* reports number of files and number of directories
* reports both base-1000 and base-1024 numbers. Mostly because I got tired of explaining the difference at work.
* by default doesn't produce output for 2 directories deeper than the directory we started in
* optionally filters out small-fry directories, e.g. -S 100M in the first example.
* optionally sorts by size
* reports apparent size, as well as difference in actualy disk use (which is usually only slightly higher due to filesystem overhead, but can be lower e.g. around sparse files or ZFS compression).
* ignores symlinks, since we only care about real files in their actual location


## Examples
Should e.g. make it easier to find where bulky things are, e.g.:
```
    # file-count /usr
      #FILES   #DIRS       ASIZE              DUDIFF         PATH
        2742       0    1.3G / 1.2Gi      +5.7M / +5.5Mi     /usr/bin
           7       0    115K / 112Ki       +16K / +16Ki      /usr/games
       29640    1910    342M / 326Mi       +70M / +67Mi      /usr/include
       77667    9504    8.7G / 8.1Gi      +182M / +174Mi     /usr/lib
         295       1     23M / 22Mi       +775K / +757Ki     /usr/lib32
         295       1     23M / 22Mi       +767K / +749Ki     /usr/libx32
      118728   16963   11.3G / 10.5Gi     +289M / +275Mi     /usr/local
         366       0    119M / 113Mi      +743K / +725Ki     /usr/sbin
      301078   29678   10.7G / 10Gi       +763M / +728Mi     /usr/share
      109760   30703    387M / 369Mi      +204M / +195Mi     /usr/src
      640578   88770     33G / 31Gi       +1.5G / +1.4Gi     /usr
    299 link duplicates
    INFO: we ignored 47935 symlinks
```

Okay, then what are the largest directories in `/usr/share` - thing larger than 500MB, sorted by size?
```
    # file-count /usr/share -S 500M -s 
    Output postponed until we are done, since we'll be sorting it...
      #FILES   #DIRS       ASIZE              DUDIFF         PATH
        6650    1641    644M / 614Mi       +18M / +18Mi      /usr/share/atom
       79618    4707    1.2G / 1.1Gi      +191M / +182Mi     /usr/share/texlive
       43219    7929    1.8G / 1.7Gi      +103M / +98Mi      /usr/share/doc
        2581     245      2G / 1.9Gi      +5.7M / +5.4Mi     /usr/share/fonts
       37884     187    3.4G / 3.2Gi       +82M / +78Mi      /usr/share/nltk_data
      301078   29678   10.7G / 10Gi       +763M / +728Mi     /usr/share
    INFO: we ignored 38360 symlinks
```

Show home directories larger than 100MB:
```
    # file-count /home -S 100M
      #FILES   #DIRS       ASIZE            DUDIFF         PATH
        5558      55     39G / 37Gi      -27.6M / -26.3Mi    /home/repository
      117069   10318    6.4G / 5.9Gi      -1.8G / -1.6Gi     /home/scarfboy
      122894   10585     46G / 43Gi       -1.8G / -1.7Gi     /home
```


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
 - figure out double output logic of root dir
 - clarify what exactly we do around links
 - deal with being handed multiple directories (?) 

## CONSIDERING:
 - 'stay on one device' feature
