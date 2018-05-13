Note that there also some additional examples in Section 3 when describing more complex options.

| Test description | Sample command |
| --- | ---|
| Large area random concurrent reads of 4KB blocks | `diskspd -c2G -b4K -F8 -r -o32 -W60 -d60 -Sh testfile.dat` |
| Large area random concurrent writes of 4KB blocks | `diskspd -c2G -w100 -b4K -F8 -r -o32 -W60 -d60 -Sh testfile.dat` |
| Large area random serial reads of 4KB blocks. | `diskspd -c2G -b4K -r -o1 -W60 -d60 -Sh testfile.dat` |
| Large area random serial writes of 4KB blocks | `diskspd -c2G -w100 -b4K -r -o1 -W60 -d60 -Sh testfile.dat` |
| Large area sequential concurrent reads of 4KB blocks | `diskspd -c2G -b4K -F8 -T1b -s8b -o32 -W60 -d60 -Sh testfile.dat` |
| Large area sequential concurrent writes of 4KB blocks | `diskspd -c2G -w100 -b4K -F8 -T1b -s8b -o32 -W60 -d60 -Sh testfile.dat` |
| Large area sequential serial reads of 4KB blocks | `diskspd -c2G -b4K -o1 -W60 -d60 -Sh testfile.dat` |
| Large area sequential serial writes of 4KB blocks | `diskspd -c2G -w100 -b4K -o1 -W60 -d60 -Sh testfile.dat` |
| Small area concurrent reads of 4KB blocks | `diskspd -c100b -b4K -o32 -F8 -T1b -s8b -W60 -d60 -Sh testfile.dat` |
| Small area concurrent writes of 4KB blocks | `diskspd -c100b -w100 -b4K -o32 -F8 -T1b -s8b -W60 -d60 -Sh testfile.dat` |
| Display statstics about physical disk I/O and memory events from the NT Kernel Logger | `diskspd -eDISK_IO -eMEMORY_PAGE_FAULTS testfile.dat` |
