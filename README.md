# DFIR Triage Collector

Collector - an automated single file, when executed, will collect artifacts from Windows, Linux, and Mac systems to later be leveraged to rapidly conduct triage analysis on a endpoint.

## What is DFIR Triage Collector

The main features are:

* Quick collection (it's really fast)
* Raw file collection process does not use Windows API
* Collection of key artifacts by default.
* Ability to specify custom targets for collection.
* Acquisition of special and in-use files, including alternate data streams,
  system files, and hidden files.
* Glob and regular expression patterns are available to specify custom targets.
* Data is collected into a zip file, allowing the user to modify the compression
  level, set an archive password, and file name.
* Specification of a SFTP or S3 destination for the file archive.

DFIR Triage Collector uses .NET Core and runs natively on Windows, Linux, and MacOS. Self
contained applications for the following are included in releases for
version 3.1 and higher.

* Windows x86
* Windows x64
* Linux x64
* MacOS x64

## SYNOPSIS

Below is the output of Collector:

```text
.\Collector.exe -h
Collector Version 1.0.1.0

Usage: Collector [Options]... [Files]...

The Collector tool gathers forensic artifacts from hosts with NTFS file systems quickly, securely and minimizes impact to the host.

The available options are:
-od
        Defines the directory that the zip archive will be created in. Defaults to current working directory.
Usage: -od <directory path>

-of
        Defines the name of the zip archive will be created. Defaults to host machine's name.
Usage: -of <archive name>

-c
        Optional argument to provide custom list of artifact files and directories (one entry per line). NOTE: Please see CUSTOM_PATH_TEMPLATE.txt for sample.
Usage: -c <path to config file>

-d
        Same as '-c' but will collect default paths included in Collector in addition to those specified in the provided config file.
Usage: -d <path to config file>

-u
        SFTP username

-p
        SFTP password

-s
        SFTP Server resolvable hostname or IP address and port. If no port is given then 22 is used by default.  Format is <server name>:<port>
 Usage: -s <ip>:<port>
 
-os
        Defines the output directory on the SFTP server, as it may be a different location than the ZIP generate on disk. Can be full or relative path.
 Usage: -os <directory path>

--s3-bucket
        The S3 Bucket URL to write output to.

--s3-accesskey
        The AWS IAM Access Key which has access to upload to the Bucket URL spcified in --s3-bucket

--s3-secret
        The AWS IAM Secrey key associated with the access key listed in --s3-accesskey

--s3-region
        The region (ie. us-east-1) the S3 Bucket is located in

--no-uploadcleanup
        Disables the removal of the .zip file used for collection after uploading. Only applies if SFTP or S3 option is enabled.

--dry-run
        Collect artifacts to a virtual zip archive, but does not send or write to disk.

--force-native
        Uses the native file system instead of a raw NTFS read. Unix-like environments always use this option.

-zp
        Uses a password to encrypt the archive file

-zl
        Uses a number between 1-9 to change the compression level of the archive file

--usnjrnl
        Enables collecting $UsnJrnl

-l
        Sets the file path to write log messages to. Defaults to ./Collector.log
 Usage: -l Collector_run.log

-q
        Disables logging to the console and file.
 Usage: -q

-v
        Increases verbosity of the console log. By default the console only shows information or greater events and the file log shows all entries. Disabled when `-q` is used.
 Usage: -v
```

## Default Collection Paths

Collector gathers forensic artifacts from hosts with NTFS file systems
quickly, securely and minimizes impact to the host. All collection paths are
case-insensitive.

**Note:** See CollectionPaths.cs for a full list of default files collected and
for the underlying patterns used for collection. You can easily extend this list
through the use of patterns as shown in CUSTOM_PATH_TEMPLATE.txt or by opening
a pull request.

The standard list of collected artifacts are as follows.

### Windows

System Root (ie `C:\Windows`):

* `%SYSTEMROOT%\Tasks\**`
* `%SYSTEMROOT%\Prefetch\**`
* `%SYSTEMROOT%\System32\sru\**`
* `%SYSTEMROOT%\System32\winevt\Logs\**`
* `%SYSTEMROOT%\System32\Tasks\**`
* `%SYSTEMROOT%\System32\Logfiles\W3SVC1\**`
* `%SYSTEMROOT%\System32\Logfiles\SUM\*.mbd`
* `%SYSTEMROOT%\Appcompat\Programs\**`
* `%SYSTEMROOT%\SchedLgU.txt`
* `%SYSTEMROOT%\inf\setupapi.dev.log`
* `%SYSTEMROOT%\System32\drivers\etc\hosts`
* `%SYSTEMROOT%\System32\config\SAM`
* `%SYSTEMROOT%\System32\config\SOFTWARE`
* `%SYSTEMROOT%\System32\config\SECURITY`
* `%SYSTEMROOT%\System32\config\SOFTWARE`
* `%SYSTEMROOT%\System32\config\SAM.LOG1`
* `%SYSTEMROOT%\System32\config\SOFTWARE.LOG1`
* `%SYSTEMROOT%\System32\config\SECURITY.LOG1`
* `%SYSTEMROOT%\System32\config\SOFTWARE.LOG1`
* `%SYSTEMROOT%\System32\config\SAM.LOG2`
* `%SYSTEMROOT%\System32\config\SOFTWARE.LOG2`
* `%SYSTEMROOT%\System32\config\SECURITY.LOG2`
* `%SYSTEMROOT%\System32\config\SOFTWARE.LOG2`

Program Data (ie `C:\ProgramData`):

* `%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup\**`

Drive Root (ie `C:\`)

* `%SYSTEMDRIVE%\$Recycle.Bin\**\$I*`
* `%SYSTEMDRIVE%\$Recycle.Bin\$I*`
* `%SYSTEMDRIVE%\$LogFile`
* `%SYSTEMDRIVE%\$MFT`

User Profiles (ie `C:\Users\*`):

* `C:\Users\*\NTUser.DAT`
* `C:\Users\*\NTUser.DAT.LOG1`
* `C:\Users\*\NTUser.DAT.LOG2`
* `C:\Users\*\AppData\Roaming\Microsoft\Windows\Recent\**`
* `C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`
* `C:\Users\*\AppData\Roaming\Mozilla\Firefox\Profiles\**`
* `C:\Users\*\AppData\Local\Microsoft\Windows\WebCache\**`
* `C:\Users\*\AppData\Local\Microsoft\Windows\Explorer\**`
* `C:\Users\*\AppData\Local\Microsoft\Windows\Terminal Server Client\Cache\**`
* `C:\Users\*\AppData\Local\Microsoft\Windows\UsrClass.dat`
* `C:\Users\*\AppData\Local\Microsoft\Windows\UsrClass.dat.LOG1`
* `C:\Users\*\AppData\Local\Microsoft\Windows\UsrClass.dat.LOG2`
* `C:\Users\*\AppData\Local\ConnectedDevicesPlatform\**`
* `C:\Users\*\AppData\Local\Google\Chrome\User Data\Default\History\**`
* `C:\Users\*\AppData\Local\Microsoft\Edge\User Data\Default\History\**`

### macOS

**Note**: Modern macOS systems have functionality that will prompt the user to
approve on a per-application basis, access to sensitive locations on a system.
This can be overridden through modifying the System Preferences to give the Collector
binary and it's parent process (such as Terminal) full disk access.

System paths:

* `/etc/hosts.allow`
* `/etc/hosts.deny`
* `/etc/hosts`
* `/etc/passwd`
* `/etc/group`
* `/etc/rc.d/**`
* `/var/log/**`
* `/private/etc/rc.d/**`
* `/private/etc/hosts.allow`
* `/private/etc/hosts.deny`
* `/private/etc/hosts`
* `/private/etc/passwd`
* `/private/etc/group`
* `/private/var/log/**`
* `/System/Library/StartupItems/**`
* `/System/Library/LaunchAgents/**`
* `/System/Library/LaunchDaemons/**`
* `/Library/StartupItems/**`
* `/Library/LaunchAgents/**`
* `/Library/LaunchDaemons/**`
* `/.fseventsd/**`

Libraries paths:

* `**/Library/*Support/Google/Chrome/Default/*`
* `**/Library/*Support/Google/Chrome/Default/History*`
* `**/Library/*Support/Google/Chrome/Default/Cookies*`
* `**/Library/*Support/Google/Chrome/Default/Bookmarks*`
* `**/Library/*Support/Google/Chrome/Default/Extensions/**`
* `**/Library/*Support/Google/Chrome/Default/Extensions/Last*`
* `**/Library/*Support/Google/Chrome/Default/Extensions/Shortcuts*`
* `**/Library/*Support/Google/Chrome/Default/Extensions/Top*`
* `**/Library/*Support/Google/Chrome/Default/Extensions/Visited*`

User paths:

* `/root/.*history`
* `/Users/*/.*history`

Other Paths:

* `**/places.sqlite*`
* `**/downloads.sqlite*`

### Linux

System Paths:

* `/etc/hosts.allow`
* `/etc/hosts.deny`
* `/etc/hosts`
* `/etc/passwd`
* `/etc/group`
* `/etc/crontab`
* `/etc/cron.allow`
* `/etc/cron.deny`
* `/etc/anacrontab`
* `/etc/apt/sources.list`
* `/etc/apt/trusted.gpg`
* `/etc/apt/trustdb.gpg`
* `/etc/resolv.conf`
* `/etc/fstab`
* `/etc/issues`
* `/etc/issues.net`
* `/etc/insserv.conf`
* `/etc/localtime`
* `/etc/timezone`
* `/etc/pam.conf`
* `/etc/rsyslog.conf`
* `/etc/xinetd.conf`
* `/etc/netgroup`
* `/etc/nsswitch.conf`
* `/etc/ntp.conf`
* `/etc/yum.conf`
* `/etc/chrony.conf`
* `/etc/chrony`
* `/etc/sudoers`
* `/etc/logrotate.conf`
* `/etc/environment`
* `/etc/hostname`
* `/etc/host.conf`
* `/etc/fstab`
* `/etc/machine-id`
* `/etc/screen-rc`
* `/etc/rc.d/**`
* `/etc/cron.daily/**`
* `/etc/cron.hourly/**`
* `/etc/cron.weekly/**`
* `/etc/cron.monthly/**`
* `/etc/modprobe.d/**`
* `/etc/modprobe-load.d/**`
* `/etc/*-release`
* `/etc/pam.d/**`
* `/etc/rsyslog.d/**`
* `/etc/yum.repos.d/**`
* `/etc/init.d/**`
* `/etc/systemd.d/**`
* `/etc/default/**`
* `/var/log/**`
* `/var/spool/at/**`
* `/var/spool/cron/**`
* `/var/spool/anacron/cron.daily`
* `/var/spool/anacron/cron.hourly`
* `/var/spool/anacron/cron.weekly`
* `/var/spool/anacron/cron.monthly`
* `/boot/grub/grub.cfg`
* `/boot/grub2/grub.cfg`
* `/sys/firmware/acpi/tables/DSDT`

User paths:

* `/root/.*history`
* `/root/.*rc`
* `/root/.*_logout`
* `/root/.ssh/config`
* `/root/.ssh/known_hosts`
* `/root/.ssh/authorized_keys`
* `/root/.selected_editor`
* `/root/.viminfo`
* `/root/.lesshist`
* `/root/.profile`
* `/root/.selected_editor`
* `/home/*/.*history`
* `/home/*/.ssh/known_hosts`
* `/home/*/.ssh/config`
* `/home/*/.ssh/autorized_keys`
* `/home/*/.viminfo`
* `/home/*/.profile`
* `/home/*/.*rc`
* `/home/*/.*_logout`
* `/home/*/.selected_editor`
* `/home/*/.wget-hsts`
* `/home/*/.gitconfig`
* `/home/*/.mozilla/firefox/*.default*/**/*.sqlite*`
* `/home/*/.mozilla/firefox/*.default*/**/*.json`
* `/home/*/.mozilla/firefox/*.default*/**/*.txt`
* `/home/*/.mozilla/firefox/*.default*/**/*.db*`
* `/home/*/.config/google-chrome/Default/History*`
* `/home/*/.config/google-chrome/Default/Cookies*`
* `/home/*/.config/google-chrome/Default/Bookmarks*`
* `/home/*/.config/google-chrome/Default/Extensions/**`
* `/home/*/.config/google-chrome/Default/Last*`
* `/home/*/.config/google-chrome/Default/Shortcuts*`
* `/home/*/.config/google-chrome/Default/Top*`
* `/home/*/.config/google-chrome/Default/Visited*`
* `/home/*/.config/google-chrome/Default/Preferences*`
* `/home/*/.config/google-chrome/Default/Login Data*`
* `/home/*/.config/google-chrome/Default/Web Data*`


## EXAMPLES

### Standard collection

```text
Collector.exe
```

### Linux/macOS collection

```text
./Collector
```

### Collect artifacts and store data in "C:\Temp\LRData"

```text
Collector.exe -od "C:\Temp\LRData"
```

### Collect artifacts and store data in ".\LRData"

```text
Collector.exe -od LRData
```

### Disable log file

```text
Collector.exe -q
```

### Collect artifacts and send data to SFTP server 8.8.8.8

```text
Collector.exe -u username -p password -s 8.8.8.8
```

### Collect artifacts and send data to S3 

```text
Collector.exe --s3-bucket bucketname --s3-accesskey ABCDEG123456 --s3-secret abcd123456/a123abDEfgHIjk467--s3-region us-east-1
```

### Collect to another folder and filename

```text
Collector -od data -of important-data.zip
```

### Collect USN $J Journal

```text
Collector --usnjrnl
```

### Collect custom list of artifacts from a file containing paths

The sample `custom.txt`, requires a **tab delimiter** between pattern
definition and pattern. Lines starting with `#` will be ignored:

```text
# Static paths are fixed, case-insensitive paths to compare
# against files found on a system. This is the fastest search
# method available, please use when possible.
#
static  C:\Windows\System32\Config\SAM
#
# Glob paths leverage glob patterns specified at
# `https://github.com/dazinator/DotNet.Glob`. This is faster than RegEx and
# should be favored unless more complex patterns are required. Useful for
# scanning for files by name or extension recursively. Also useful for
# collecting a folder recursively.
#
glob    **\malware.exe
#
# Regex paths leverage the .NET Regex capabilities and will search for
# specified patterns across accessible files. This is the slowest option and
# should be saved for unique use cases that are not supported by globbing.
#
regex   .*\Windows\Temp\[a-z]{8}\+*
```

This can then be supplied to Collector for a custom collection of just these paths:

```text
Collector.exe -c custom.txt
```

### Collection of custom paths in addition to the default paths

```text
Collector -d custom.txt
```

## Custom collection paths

Collector allows for the specification of custom collection paths with the use of
a configuration file provided after `-c` or `-d` at the command line. A brief
summary of the format is below, though full details are available within the
`CUSTOM_PATH_TEMPLATE.txt` provided in the repository.

The custom collection path file allows for the specification of files to collect
from a target system. The format is tab delimited, where the first field is a
pattern type indicator and the second field is the pattern to collect.

* **NOTE**: As previously mentioned, all collection paths are case-insensitive.
* **NOTE**: The path specifier needs to match the platform you are collecting
  from. For Windows, it must be `\` and `/` for macOS and Linux.
* **NOTE**: You must use tabs to delimit the patterns. Spaces will not
  work. This means that spaces are allowed in the second field containing
  pattern content

### Pattern Types

There are 4 pattern types, summarized below:

* static
  * This format allows for the specification of a specific file at a known path.
  * This is the fastest pattern type, as it is performing a string comparison.
  * Example: `static    C:\Windows\System32\config\SAM`
* glob
  * This format allows the specification of basic patterns. Most commonly used
    to collect the contents of a folder, even recursively. Has a few common
    implementations, demonstrated in the examples below.
  * While not as fast as static paths, it allows for some common pattern
    matching and is faster than leveraging regular expressions.
  * Example: `glob    C:\Users\*\ntuser.dat` - collects the NTUser.dat from each user.
  * Example: `glob    C:\**\malware.exe` - collects files named `malware.exe`
    regardless of what folder they are in, recursively.
  * Example: `glob    C:\Users\*\AppData\Microsoft\Windows\Recent\*.lnk` -
    collects all files ending with `.lnk`
  * Example: `glob    **\*malware*` - collects all files recursively.
  * More details at [github.com/dazinator/DotNet.Glob](https://github.com/dazinator/DotNet.Glob)
* regex
  * Allows the specification of advanced patterns through .NET's regular
    expression implementation.
  * Example: `regex    C:\[0-9]+.exe` - collect all numeric-only executables in
    the root of the `C:\` drive.
* force
  * Same as the static option, though will attempt collection even if the file
    is not identified in the file enumeration process.
  * This is useful in the collection of alternate data streams and special
    files not generally exposed to directory traversal functions.
  * Example: `force    C:\$Extend\$UsnJrnl:$J`

## Acknowledgment 

This tool was originally forked from version 2.2.0 of CyLR.
