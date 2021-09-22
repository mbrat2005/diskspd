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
| Display statistics about physical disk I/O and memory events from the NT Kernel Logger | `diskspd -eDISK_IO -eMEMORY_PAGE_FAULTS testfile.dat` |
| Specify 90% of I/O to 10% of the target, 0% of I/O to the next 10% of the target, 5% of I/O to the next 20% of target and the remaining 5% of I/O to the remaining 60% of target (implied), using -rdpct. | `diskspd -c1G -b4K -r -o32 -w0 -d60 -Sh -rdpct90/10:0/10:5/20 testfile.dat` |
| Specify 90% of I/O to 10GiB of the target, 0% of I/O to the next 10GiB, 5% of I/O to the next 20GiB and the remaining 5% of I/O to the remaining target file size, using -rdabs. | `diskspd -c1G -b4K -r -o32 -t1 -w0 -d60 -Sh -rdabs90/10G:0/10G:5/20G testfile.dat` |
| Mix random and sequential workloads (20% random and 80% sequential I/O) | `diskspd -c1G -b4K -o32 -t1 -s8b -w50 -rs20 -d60 -Sh testfile.dat` |
| Limit IOPS to 1000 per thread | `diskspd -c1G -b4K -o32 -t1 -w50 -g1000i -d60 -Sh testfile.dat` |
| Create a text profile from a set of parameters | `diskspd -t1 -o4 -b4k -r4k -w0 -Sh -D -d60 -L -c1G -Rptext > profile.txt` |
| Create an XML profile from a set of parameters | `diskspd -t1 -o4 -b4k -r4k -w0 -Sh -D -d60 -L -c1G -Rpxml *1 *2 > profile.xml` |
| Substitute target files from a previously created XML profile (see above) | `diskspd -Xprofile.xml file1.dat, file2.dat` |
