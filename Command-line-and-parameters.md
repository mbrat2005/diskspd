DiskSpd is a command-line utility that is invoked using the following syntax:

`diskspd [options] target1 [ target2 [ target3 ...] ]`

All available options and parameters are listed in the tables below and are further described in the [Customizing tests](Customizing-tests) section. Test targets can be regular files (e.g. C:\testfile.dat), partitions (e.g. C:), or physical drives (e.g. #1). Parameters can either be specified as command line options or as part of an XML profile.

| Parameter` | Description |
| --- | --- |
| `-?` | Displays usage information for DiskSpd. |
| `-ag` | Group affinity – affinitize threads in a round-robin manner across Processor Groups, starting at group 0. This is default. Use `-n` to disable affinity. |
| `-ag#,#[,#,...]` | Advanced CPU affinity – affinitize threads round-robin to the CPUs provided. The g# notation specifies Processor Groups for the following CPU core #s. Multiple Processor Groups may be specified and groups/cores may be repeated. If no group is specified, 0 is assumed. Additional groups/processors may be added, comma separated, or on separate parameters. Examples: `-a0,1,2` and `-ag0,0,1,2` are equivalent. `-ag0,0,1,2,g1,0,1,2` specifies the first three cores in groups 0 and 1. `-ag0,0,1,2 -ag1,0,1,2` is an equivalent way of specifying the same pattern with two `-ag#` arguments. |
| `-b<size>[K\|M\|G]` | Block size in bytes or KiB, MiB, or GiB (default = 64K). |
| `-B<offset>[K\|M\|G\|b]` | Base target offset in bytes or KiB, MiB, GiB, or blocks from the beginning of the target (default offset = zero) |
| `-c<size>[K\|M\|G\|b]` | Create files of the specified size. Size can be stated in bytes or KiBs, MiBs, GiBs, or blocks. |
| `-C<seconds>` | Cool down time in seconds - continued duration of the test load after measurements are complete (default = zero seconds). |
| `-D<milliseconds>` | Capture IOPs higher-order statistics in intervals of `<milliseconds>`. These are per-thread per-target: text output provides IOPs standard deviation. The XML output provides the full IOPs time series in addition (default = 1000ms or 1 second). |
| `-d<seconds>` | Duration of measurement period in seconds, not including cool-down or warm-up time (default = 10 seconds). |
| `-f<size>[K\|M\|G\|b]` | Target size - use only the first <size> bytes or KiB, MiB, GiB or blocks of the specified targets, for example to test only the first sectors of a disk. |
| `-f<rst>` | Open file with one or more additional access hints specified to the operating system: `r`: the FILE_FLAG_RANDOM_ACCESS hint, `s`: the FILE_FLAG_SEQUENTIAL_SCAN hint and `t`: the FILE_ATTRIBUTE_TEMPORARY hint. Note that these hints are generally only applicable to cached I/O. |
| `-F<count>` | Total number of threads. Conflicts with `-t`, the option to set the number of threads per file. |
| `-g<bytes per ms>` | Throughput per-thread per-target is throttled to the given number of bytes per millisecond. This option is incompatible with completion routines. (See `-x`.) |
| `-h` | Deprecated but still honored; see `-Sh`. |
| `-i<count>` | Number of I/Os (burst size) to issue before pausing. Must be specified in combination with `-j`. |
| `-j<milliseconds>` | Pause in milliseconds before issuing a burst of I/Os. Must be specified in combination with `-i`. |
| `-I<priority>` | Set I/O priority to `<priority>`. Available values are: 1-very low, 2-low, 3-normal (default). |
| `-l` | Use large pages for I/O buffers. |
| `-L` | Measure latency statistics. Full per-thread per-target distributions are available using the XML result option. |
| `-n` | Disable default affinity. (See `-ag`.) |
| `-o<count>` | Number of outstanding I/O requests per-target per-thread. (1 = synchronous I/O, unless more than one thread is specified with by using `-F`.) (default = 2) |
| `-O<count>` | Total number of I/O requests per shared thread across all targets. (1 = synchronous I/O.)  Must be specified with `-F`. |
| `-p` | Start asynchronous (overlapped) I/O operations with the same offset. Only applicable with two or more outstanding I/O requests per thread (`-o2` or greater) |
| `-P<count>` | Print a progress dot after each specified `<count>` [default = 65536] of completed I/O operations. Counted separately by each thread. |
| `-r<alignment>[K\|M\|G\|b]` | Random I/O aligned to the specified number of `<alignment>` bytes or KiB, MiB, GiB, or blocks. Overrides `-s`. |
| `-R[text\|xml]` | Display test results in either text or XML format (default: text). |
| `-s[i]<size>[K\|M\|G\|b]` | Sequential stride size, offset between subsequent I/O operations in bytes or KiB, MiB, GiB, or blocks. Ignored if `-r` is specified (default access = sequential, default stride = block size). By default each thread tracks its own sequential offset. If the optional interlocked (`i`) qualifier is used, a single interlocked offset is shared between all threads operating on a given target so that the threads cooperatively issue a single sequential pattern of access to the target.  |
| `-S[bhruw]` | This flag modifies the caching and write-through modes for the test target. Any non-conflicting combination of modifiers can be specified (`-Sbu` conflicts, `-Shw` specifies `w` twice) and are order independent (`-Suw` and `-Swu` are equivalent). By default, caching is on and write-through is not specified. |
| `-S` | No modifying flags specified: disable software caching. Deprecated but still honored; see `-Su`. This opens the target with the FILE_FLAG_NO_BUFFERING flag. This is included in `-Sh`. |
| `-Sb` | Enable software cache (default, explicitly stated). Can be combined with `w`. |
| `-Sh` | Disable both software caching and hardware write caching. This opens the target with the FILE_FLAG_NO_BUFFERING and FILE_FLAG_WRITE_THROUGH flags and is equivalent to `-Suw`. |
| `-Sr` | Disable local caching for remote file systems. This leaves the remote system's cache enabled. Can be combined with `w`. |
| `-Su` | Disable software caching, for unbuffered I/O. This opens the target with the FILE_FLAG_NO_BUFFERING flag. This option is equivalent `-S` with no modifiers. Can be combined with `w`. |
| `-Sw` | Enable write-through I/O. This opens the target with the FILE_FLAG_WRITE_THROUGH flag. This can be combined with either buffered (`-Sw` or `-Sbw`) or unbuffered I/O (`-Suw`). It is included in `-Sh`. Note: SATA HDDs will generally not honor write through intent on individual I/Os. Devices with persistent write caches – certain enterprise flash drives and most storage arrays – will complete write-through writes when the write is stable in cache. In both cases, `-S`/`-Su` and `-Sh`/`-Suw` will see equivalent behavior. |
| `-t<count>` | Number of threads per target. Conflicts with `-F`, which specifies the total number of threads. |
| `-T<offset>[K\|M\|G\|b]` | Stride size between I/O operations performed on the same target by different threads in bytes or KiB, MiB, GiB, or blocks (default stride size = 0; starting offset = base file offset + (\<thread number> * \<offset>). Useful only when number of threads per target > 1. |
| `-v` | Verbose mode |
| `-w<percentage>` | Percentage of write requests to issue (default = 0, 100% read). The following are equivalent and result in a 100% read-only workload: omitting `-w`, specifying `-w` with no percentage and `-w0`. **CAUTION**: A write test will destroy existing data without issuing a warning. |
| `-W<seconds>` | Warmup time – duration of the test before measurements start (default = 5 seconds). |
| `-x` | Use I/O completion routines instead of I/O completion ports for cases specifying more than one I/O per thread  (see `-o`). Unless there is a specific reason to explore differences in the completion model, this should generally be left at the default. |
| `-X<filepath>` | Use an XML profile for configuring the workload. Cannot be used with any other parameters. The XML output `<Profile>` block can be used as a template. See the [diskspd.xsd](https://github.com/Microsoft/diskspd/blob/master/XmlProfileParser/diskspd.xsd) file for details. |
| `-z[seed]` | Set a random seed to the specified integer value. With no `-z`, seed=0. With plain `-z`, seed is based on system run time. |
| `-Z` | Zero the per-thread I/O buffers. Relevant for write tests. By default, the buffers are filled with a repeating pattern (0, 1, 2, …, 255, 0, 1, …). |
| `-Zr` | Initialize the write buffer with random content before every write. This option adds a run-time cost to the write performance. |
| `-Z<size>[K\|M\|G\|b]` | Separate read and write buffers and initialize a per-target write source buffer sized to the specified number of bytes or KiB, MiB, GiB, or blocks. This write source buffer is initialized with random data and per-I/O write data is selected from it at 4-byte granularity. |
| `-Z<size>[K\|M\|G\|b],<file>` | Specifies using a file as the source of data to fill the write source buffers. |

Table 1. Command line parameters

## Event parameters
The parameters in Table 2 specify events that can be used to start, end, cancel, or send notifications for a DiskSpd test run.

| Parameter | Description |
| --- | ---|
| `-ys<eventname>` | Signals event `<eventname>` before starting the actual run (no warm up). Creates a notification event if <eventname> does not exist. |
| `-yf<eventname>` | Signals event `<eventname>` after the test run completes (no cool-down). Creates a notification event if <eventname> does not exist. |
| `-yr<eventname>` | Waits on event `<eventname>` before starting the test run (including warm up). Creates a notification event if <eventname> does not exist. |
| `-yp<eventname>` | Stops the run when event `<eventname>` is set. CTRL+C is bound to this event. Creates a notification event if <eventname> does not exist. |
| `-ye<eventname>` | Sets event `<eventname>` and quits. |

Table 2. Event Parameters

## Event Tracing for Windows (ETW) parameters
Using the parameters in Table 3, you can have DiskSpd display data concerning events from an NT Kernel Logger trace session. Because event tracing (ETW) carries additional overhead, this is turned off by default.

| Parameter | Description |
| --- | ---|
| `-e<q\|c\|s>` | Use a high-performance timer (QPC), cycle count or system timer respectively (default = q, high-performance timer (QPC)). |
| `-ep` | Use paged memory for the NT Kernel Logger (default = non-paged memory). |
| `-ePROCESS` | Capture process start and end events. |
| `-eTHREAD` | Capture thread start and end events. |
| `-eIMAGE_LOAD` | Capture image load events. |
| `-eDISK_IO` | Capture physical disk I/O events. |
| `-eMEMORY_PAGE_FAULTS` | Capture all page fault events. |
| `-eMEMORY_HARD_FAULTS` | Capture hard fault events. |
| `-eNETWORK` | Capture TCP/IP, UDP/IP send and receive events. |
| `-eREGISTRY` | Capture registry events. |

Table 3. Event Tracing for Windows (ETW) parameters

## Size conventions for DiskSpd parameters
Several DiskSpd options take sizes and offsets specified as bytes or as a multiple of kilobytes, megabytes, gigabytes, or blocks: [K|M|G|b].

Conventions used for referring to multiples of bytes are a common source of confusion. In networking and communication, these multiples are universally in powers of ten: e.g. a 'GB' is 10<sup>9</sup> (1 billion) bytes. By comparison, a 'GB' of RAM is universally understood to be 2<sup>30</sup> (1,073,741,824) bytes. Storage has historically been in a gray area where file sizes are spoken of in powers of two, but storage system manufacturers refer to total capacity in powers of ten. Since storage is always accessed over at least one (SAS, SATA) or more (PCI, Ethernet, InfiniBand) communication links, this adds complexity into the already challenging exercise of understanding end-to-end flows.

The iB notation is an international convention which unambiguously refers to power of two-based sizing for numbers of bytes, as distinct from powers of ten which continue to use KB/MB/GB notation.
* 1KiB = 2<sup>10</sup> = 1,024 bytes
* 1MiB = 1024 KiB = 2<sup>20</sup> = 1,048,576 bytes
* 1GiB = 1024 MiB = 2<sup>30</sup> = 1,073,741,824 bytes

This notation will be used in this documentation. DiskSpd reports power of two-based quantities with the iB notation in text results. XML results are always reported in plain bytes.

To specify sizes to DiskSpd:
* bytes: use a plain number (65536)
* KiB: suffix a number with 'K' or 'k' (e.g. 64k)
* MiB: suffix a number with 'M' or 'm' (e.g. 1m)
* GiB: suffix a number with 'G' or 'g' (e.g. 10g)
* multiples of blocks: suffix a number with 'b' (e.g. 5b) - multiplying the block size specified with `-b`

Fractional sizes with decimal points such as 10.5 are not allowed.
