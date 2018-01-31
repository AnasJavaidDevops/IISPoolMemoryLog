# IISPoolMemoryLog
In this Power shell script i have worked on how to extract CPU memory usage for the IIS pool worker processes and maintain its log 
in a text file. We can schedule to run this script in windows task schedule. 

Powershell get all running process by below command:

              $list = get-process w3wp

The WMI Command-line (WMIC) tool provides a simple command-line interface to WMI. This allows you to use WMI to manage computers 
running Microsoft Windows.

              $wmip = get-WmiObject Win32_Process -filter $filter
              $cmdline = $wmip.CommandLine
