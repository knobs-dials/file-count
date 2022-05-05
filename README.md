A slightly more informative variation on `du`
* reports size and also contained number of files and directories
* reports both base-1000 and base-1024 numbers
  * Mostly because I got tired of explaining the difference at work.
* doesn't produce output for 2 (by default) directories deeper than the directory we started in - less spammy
* optionally sorts by size
* optionally filters out small-fry directories, e.g. -S 100M in the first example.
* reports apparent size, as well as difference in actualy disk use 
  * disk use is usually slightly higher due to filesystem overhead, but can be lower e.g. around sparse files, ZFS compression, and such
* can avoid walking onto other devices,  e.g. useful for "where is stuff on my system disk"

## What we do around links
* we ignore symlinks. We only care about real files in their actual location. 
  * We still report how many symlinks we saw.
* We avoid counting hardlinks towards size twice.
  * This means we prefer our total size to be correct - at the cost of only counting it towards the first directory we saw it in. Something's gotta give if your tree is actually a graph and not even an acyclic one.
  * We still report how many hardlink duplicates we saw.
* we ignore links to `/`. 
  * A few things like to link to that (wine, /proc) and it means we end up counting the entire filesystem when you never asked. If you want that, specify `/`

## Examples
"Where are bulky things under /usr ?":
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

"Okay, `/usr/share` seems big, what are the directories in there larger than 500MB, sorted by size?"
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

"Show home directories larger than 100MB:"
```
    # file-count /home -S 100M
      #FILES   #DIRS       ASIZE            DUDIFF         PATH
        5558      55     39G / 37Gi      -27.6M / -26.3Mi    /home/repository
      117069   10318    6.4G / 5.9Gi      -1.8G / -1.6Gi     /home/scarfboy
      122894   10585     46G / 43Gi       -1.8G / -1.7Gi     /home
```

"What's the largest uses on my root disk?"
```
    # file-count -v -D -S 2G /
      #FILES   #DIRS       ASIZE              DUDIFF         PATH
    INFO: Not crossing device boundaries onto '/data/pool'
       98508    2597    3.8G / 3.6Gi      +336M / +321Mi     /data/seafile-data
       98508    2601    3.8G / 3.6Gi      +336M / +321Mi     /data
    INFO: Not crossing device boundaries onto '/dev'
       28045   12864    3.5G / 3.2Gi       +62M / +59Mi      /root
    INFO: Not crossing device boundaries onto '/run'
    INFO: Not crossing device boundaries onto '/sys'
       80414   10118    8.7G / 8.1Gi      +189M / +180Mi     /usr/lib
      201242   31877   14.6G / 13.6Gi     +523M / +499Mi     /usr/local
      318293   38898    8.8G / 8.2Gi      +835M / +796Mi     /usr/share
      720512  107115     34G / 32Gi       +1.8G / +1.7Gi     /usr
      107261   15283     20G / 19Gi       +282M / +269Mi     /var/lib
         923      46    2.4G / 2.2Gi      +2.2M / +2.1Mi     /var/log
      111797   16625     23G / 22Gi       +292M / +278Mi     /var
    8886 link duplicates
    INFO: ignored 1 items
    INFO: we ignored 59226 symlinks
```



## USAGE
```
Usage: file-count [options]

Options:
  -h, --help            show this help message and exit
  -v, --verbose         Report more details
  -D, --stay-on-device  Don't follow mounts/symlinks onto other devices
  -n DEPTH, --depth=DEPTH
                        the maximum amount of directory depth to print,
                        relative to starting point. Default is 2, which should
                        not be so overwhelming.
  -C MINCOUNT, --mincount=MINCOUNT
                        don't show directories with fewer files than this
  -S MINSIZE, --minsize=MINSIZE
                        don't show directories with file size summing to less
                        than this (note: understands values like 1.5M)
  -s, --sort-size       sort output by size (postpones output)
  -f, --sort-filecount  sort output by file count (postpones output)
  -d, --sort-dircount   sort output by directory count (postpones output)
  -e EXTRADIGITS, --extradigits=EXTRADIGITS
                        Default is to show short overviewy sizes, you can ask
                        for more digits

```


## TODO:
 - think about charset handling (maybe switch to work completely in bytes)
 - consider counting how many times the filesystem would have us cross devices
