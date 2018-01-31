# IISPoolMemoryLog
In this Power shell script i have worked on how to extract CPU memory usage for the IIS pool worker processes and maintain its log 
in a text file. We can schedule to run this script in windows task schedule. 

Powershell get all running process by below command:

              $list = get-process w3wp

The WMI Command-line (WMIC) tool provides a simple command-line interface to WMI. This allows you to use WMI to manage computers 
running Microsoft Windows.

              $wmip = get-WmiObject Win32_Process -filter $filter
              $cmdline = $wmip.CommandLine

Following is the MemoryInfo.txt Output file:


                  Wednesday, January 31, 2018 2:22:21 AM



                    Id     VM     PM     WS   CPU AppPoolName           
                    --     --     --     --   --- -----------           
                  5368 618.68  80.47  94.86  3.91 XYZ                  
                  9360 647.11 169.89  191.9 67.11 ABC
                  9404 890.71 185.21 221.07 35.58 ABCN              



                  Wednesday, January 31, 2018 2:52:22 AM



                    Id     VM     PM     WS   CPU AppPoolName           
                    --     --     --     --   --- -----------           
                  5368 618.68  80.57  95.24  5.17 XYZ                   
                  8456 807.79 135.27 152.63 14.95 ABC
                  9404 882.76 184.98 220.66 36.59 ABCN     
                  
                  
Research material:
======================

https://technet.microsoft.com/en-us/library/ee692684.aspx

http://www.anexinet.com/blog/powershell-script-to-list-worker-processors-and-their-application-pool-names-with-memory-consumption-and-cpu-utilization/

About CPU memory:
===================
http://getgreenshot.org/2010/07/24/a-few-words-on-memory-usage-or-working-set-vs-private-working-set/
working set size is split into two values, WS Private and WS Shareable.
https://superuser.com/questions/618686/private-bytes-vs-working-set-in-process-explorer

Private Bytes VS Working Set in Process Explorer:

I'm afraid its not quite that simple. In particular Private bytes is not very useful for answering the question you pose. 

The First thing to keep in mind is that a memory page may exist in main memory or in external storage (these days "disk paging" or "swap"). The second is that a process will have private pages of memory in its footprint, but will also use objects in shared pages, that other processes are using at the same time.

Working set is the size of the pages belonging to the process, which are currently stored in main memory. When a page of memory used by a process is moved to the page file, it is removed from the working set metric, and when its called back into main memory, its added in again. 

Working set does not exclusively refer to memory your process owns however. processes use many shared memory objects, and the size of these objects is reflected in the stat. unfortunately, when two processes share a 1MB object, both their processes show an 1MB allocation in the working set, so if you added up all the working sets, the 1MB object would be recorded twice, so the accumulation of all your working set sizes might in extreme cases, appear to exceed the size of the ram available. see more here: http://cybernetnews.com/cybernotes-windows-memory-usage-explained/

Private Bytes refers to the amount of Page file space that is allocated to the process (not necessarily used) in the event that the process's private memory footprint is completely paged out to swap. most of the time, the process is not entirely (or at all) page-file resident, so that's why private bytes appears to have "room" for further allocation. It is not however the case. 

Private bytes however only refers to the processes private memory, so this value may not reflect shared resources (even if the shared resource is only used by this process at present).

"Working set Private" ("WS Private bytes" or "Private WS" in process explorer depending on version) is probably the best metric for your use. it does not concern itself with page file, so you get an accurate representation of the processes impact on your physical ram, and it does not doubly-count shared objects. shared objects are tallied once (only for the process that created them), but that also means that from a single-process perspective you are not recording that your process uses shared objects created by another process, so your process might use more ram on another machine or in a situation where it was required to create the shared object itself instead of using another processes instance of it. Windows Task manager uses working set private as its memory usage metric. 

hope that helps
