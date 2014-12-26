
# Chapter 2. Interactive PowerShell - Master-PowerShell | With Dr. Tobias Weltner

PowerShell has two faces: interactivity and script automation. In this chapter, you will first learn how to work with PowerShell interactively. Then, we will take a look at PowerShell scripts.

**Topics Covered:**

## PowerShell as a Calculator

You can use the PowerShell console to execute arithmetic operations the same way you use a calculator. Just enter a math expression and PowerShell will give you the result:

2+4 (Enter)

6

You can use all of the usual basic arithmetic operations. Even parentheses will work the same as when you use your pocket calculator:

(12+5) * 3 / 4.5 (Enter)

11.3333333333333

Parentheses play a special role in PowerShell. They always work from the inside out: the results inside the parentheses are produced before evaluating the expressions outside of the parentheses, i.e. (2*2)*2 = 4*2. For example, operations performed within parentheses have priority and ensure that multiplication operations do not take precedence over addition operations. As you'll discover in upcoming chapters, parentheses are also important when using PowerShell commands. For example, you can list the contents of sub-directories with the _dir_ command and then determine the number of files in a folder by enclosing the _dir_ command in parentheses.

(Dir $env:windir*.exe).Count (Enter)

12

In addition, there are two very similar constructions: @() and $().

@() will also execute the code inside the brackets but return the result always as an array. The previous line would have not returned the number of items if the folder contained only one or none file. This line will always count folder content reliably:

@(Dir $env:windir*.exe -ErrorAction SilentlyContinue).Count (Enter)

12

Try and replace the folder with some empty folder and you still get back the correct number of items. $() is used inside strings so you can use this line if you'd like to insert the result of some code into text:

"Number of EXE files in my windows folder: `  
$(@(Dir $env:windir*.exe -ErrorAction SilentlyContinue).Count)"(Enter)

Note that PowerShell always uses the decimal point for numbers. Some cultures use other characters in numbers, such as a comma. PowerShell does not care. It always uses the decimal point. Using a comma instead of a decimal point will return something entirely different:

4,3 + 2 (Enter)

4  
3  
2

The comma always creates an array. So in this example, PowerShell created an array with the elements 4 and 3. It then adds the number 2 to that array, resulting in an array of three numbers. The array content is then dumped by PowerShell into the console. So the important thing to take with you is that the decimal point is always a point and not a comma in PowerShell.

### Calculating with Number Systems and Units

The next arithmetic problem is a little unusual.

4GB / 720MB (Enter)

5.68888888888889

The example above calculates how many CD-ROMs can be stored on a DVD. PowerShell will support the common unit's kilobyte (KB), megabyte (MB), gigabyte (GB), terabyte (TB), and petabyte (PT). Just make sure you do not use a space between a number and a unit.

1mb (Enter)

1048576

These units can be in upper or lower case – PowerShell does not care. However, whitespace characters do matter because they are always token delimiters. The units must directly follow the number and must not be separated from it by a space. Otherwise, PowerShell will interpret the unit as a new command and will get confused because there is no such command.

Take a look at the following command line:

12 + 0xAF (Enter)

187

PowerShell can easily understand hexadecimal values: simply prefix the number with "0x":

0xAFFE (Enter)

45054

Here is a quick summary:

* **Operators:** Arithmetic problems can be solved with the help of operators. Operators evaluate the two values to the left and the right. For basic operations, a total of five operators are available, which are also called "arithmetic operators" ([Table2.1][1]).
* **Brackets:** Brackets group statements and ensure that expressions in parentheses are evaluated first.
* **Decimal point:** Fractions use a point as a decimal separator (never a comma).
* **Comma:** Commas create arrays and are irrelevant for normal arithmetic operations.
* **Special conversions:** Hexadecimal numbers are designated by the prefix "0x", which ensures that they are automatically converted into decimal values. If you add one of the KB, MB, GB, TB, or PB units to a number, the number will be multiplied by the unit. Whitespace characters aren't allowed between numbers and values.
* **Results and formats:** Numeric results are always returned as decimal values. You can use a format operator like _-f_ if you'd like to see the results presented in a different way. This will be discussed in detail later in this book.

| ----- |
| **Operator** |  **Description** |  **example** |  **result** |
| + |  **Adds two values** |  **5 + 4.5** |  **9.5** |
|  |   |  **2gb + 120mb** |  **2273312768** |
|  |   |  **0x100 + 5** |  **261** |
|  |   |  **"Hello " + "there"** |  **"Hello there"** |
| - |  **Subtracts two values** |  **5 - 4.5** |  **0.5** |
|  |   |  **12gb - 4.5gb** |  **8053063680** |
|  |   |  **200 - 0xAB** |  **29** |
| * |  **Multiplies two values** |  **5 * 4.5** |  **22.5** |
|  |   |  **4mb * 3** |  **12582912** |
|  |   |  **12 * 0xC0** |  **2304** |
|  |   |  **"x" * 5** |  **"xxxxx"** |
| / |  **Divides two values** |  **5 / 4.5** |  **1.11111111111111** |
|  |   |  **1mb / 30kb** |  **34.1333333333333** |
|  |   |  **0xFFAB / 0xC** |  **5454,25** |
| % |  **Supplies the rest of division** |  **5%4.5** |  **0.5** |

**Table 2.1:** Arithmetic operators

## Executing External Commands

PowerShell can also launch external programs in very much the same way as the classic console. For example, if you want to examine the settings of your network card, you can enter the command _ipconfig_—it works in PowerShell the same way it does in the traditional console:

Ipconfig

Windows IP Configuration  
Wireless LAN adapter Wireless Network Connection:

Connection-specific DNS Suffix:  
Connection location IPv6 Address . : fe80::6093:8889:257e:8d1%8  
IPv4 address . . . . . . . . . . : 192.168.1.35  
Subnet Mask . . . . . . . . . . : 255.255.255.0  
Standard Gateway . . . . . . . . . : 192.168.1.1

This following command enables you to verify if a Web site is online and tells you the route the data packets are sent between a Web server and your computer:

Tracert powershell.com

Trace route to powershell.com [74.208.54.218] over a maximum of 30 hops:  
1 12 ms 7 ms 11 ms TobiasWeltner-PC [192.168.1.1]  
2 15 ms 16 ms 16 ms dslb-088-070-064-001.pools.arcor-ip.net [88.70.64.1]  
3 15 ms 16 ms 16 ms han-145-254-11-105.arcor-ip.net [145.254.11.105]  
(...)  
17 150 ms 151 ms 152 ms vl-987.gw-ps2.slr.lxa.oneandone.net [74.208.1.134]  
18 145 ms 145 ms 149 ms ratdog.info [74.208.54.218]

You can execute any Windows programs. Just type _notepad_ (Enter) or _explorer_ (Enter).

However, there's a difference between text-based commands like _ipconfig_ and Windows programs like _Notepad_. Text-based commands are executed synchronously, and the console waits for the commands to complete. Windows-based programs are executed asynchronously. Press (Ctrl)+(C) to cancel a text-based command.

Note that you can use the cmdlet Start-Process with all of its parameters when you want to launch an external program with special options. With _Start-Process_, you can launch external programs using different credentials; you can make PowerShell wait for Windows-based programs or control window size.

Type _cls_ (Enter) to clear the console screen.

### Starting the "Classic" Console

To temporarily switch back to the "classic" console, simply enter _cmd_ (Enter). ). Since the old console is just another text-based command, you can easily launch it from within PowerShell. To leave the old console, you can type _exit_ (Enter). Even PowerShell can be closed by entering _exit_. Most text-based commands use _exit_ to quit. Occasionally, the command _quit_ is required in commands instead of _exit_.

### Discovering Useful Console Commands

The _cmd_ command can be used for just one command when you specify the parameter _/c_. This is useful for invoking an old console command like _help_. This command has no external program that you can access directly from PowerShell. It's only available inside the classic console. Using this command will return a list of many other useful external console commands:

Cmd /c Help

For more information on a specific command, type HELP command-name

ASSOC Displays or modifies file extension associations.  
AT Schedules commands and programs to run on a computer.  
ATTRIB Displays or changes file attributes.  
BREAK Sets or clears extended CTRL+C checking.  
CACLS Displays or modifies access control lists (ACLs) of files.  
CALL Calls one batch program from another.  
CD Displays the name of or changes the current directory.  
CHCP Displays or sets the active code page number.  
CHDIR Displays the name of or changes the current directory.  
CHKDSK Checks a disk and displays a status report.  
CHKNTFS Displays or modifies the checking of disk at boot time.  
CLS Clears the screen.  
CMD Starts a new instance of the Windows command interpreter.  
COLOR Sets the default console foreground and background colors.  
COMP Compares the contents of two files or sets of files.  
COMPACT Displays or alters the compression of files on NTFS partitions.  
CONVERT Converts FAT volumes to NTFS. You cannot convert the current drive.  
COPY Copies one or more files to another location.  
DATE Displays or sets the date.  
DEL Deletes one or more files.  
DIR Displays a list of files and subdirectories in a directory.  
DISKCOMP Compares the contents of two floppy disks.  
DISKCOPY Copies the contents of one floppy disk to another.  
DOSKEY Edits command lines, recalls Windows commands, and creates macros.  
ECHO Displays messages, or turns command echoing on or off.  
ENDLOCAL Ends localization of environment changes in a batch file.  
ERASE Deletes one or more files.  
EXIT Quits the CMD.EXE program (command interpreter).  
FC Compares two files or sets of files, and displays the differences between them.  
FIND Searches for a text string in a file or files.  
FINDSTR Searches for strings in files.  
FOR Runs a specified command for each file in a set of files.  
FORMAT Formats a disk for use with Windows.  
FTYPE Displays or modifies file types used in file extension associations.  
GOTO Directs the Windows command interpreter to a labeled line in a batch program.  
GRAFTABL Enables Windows to display an extended character set in graphics mode.  
HELP Provides Help information for Windows commands.  
IF Performs conditional processing in batch programs.  
LABEL Creates, changes, or deletes the volume label of a disk.  
MD Creates a directory.  
MKDIR Creates a directory.  
MODE Configures a system device.  
MORE Displays output one screen at a time.  
MOVE Moves one or more files from one directory to another directory.  
PATH Displays or sets a search path for executable files.  
PAUSE Suspends processing of a batch file and displays a message.  
POPD Restores the previous value of the current directory saved by PUSHD.  
PRINT Prints a text file.  
PROMPT Changes the Windows command prompt.  
PUSHD Saves the current directory then changes it.  
RD Removes a directory.  
RECOVER Recovers readable information from a bad or defective disk.  
REM Records comments (remarks) in batch files or CONFIG.SYS.  
REN Renames a file or files.  
RENAME Renames a file or files.  
REPLACE Replaces files.  
RMDIR Removes a directory.  
SET Displays, sets, or removes Windows environment variables.  
SETLOCAL Begins localization of environment changes in a batch file.  
SHIFT Shifts the position of replaceable parameters in batch files.  
SORT Sorts input.  
START Starts a separate window to run a specified program or command.  
SUBST Associates a path with a drive letter.  
TIME Displays or sets the system time.  
TITLE Sets the window title for a CMD.EXE session.  
TREE Graphically displays the directory structure of a drive or path.  
TYPE Displays the contents of a text file.  
VER Displays the Windows version.  
VERIFY Tells Windows whether to verify that your files are written correctly to a disk.  
VOL Displays a disk volume label and serial number.  
XCOPY Copies files and directory trees.

You can use all of the above commands in your PowerShell console. To try this, pick some commands from the list. For example:

Cmd /c help vol

As an added safety net, you can run PowerShell without administrator privileges when experimenting with new commands. That will protect you against mistakes as most dangerous commands can no longer be executed without administrator rights:

defrag c:

You must have Administrator privileges to defragment a volume.   
Use an administrator command line and then run the program again.

Remember to start your PowerShell explicitly with administrator rights if you must use admin privileges and have enabled User Account Control.. To do this, right-click _PowerShell.exe_ and in the context menu, select _Run as Administrator_.

![][2]

**Figure 2.1:** Run PowerShell as administrator.

_(Run without administrator privileges whenever possible)_

### Security Restrictions

While you can launch _notepad_, you cannot launch _wordpad_:

wordpad

The term "wordpad" is not recognized as a cmdlet, function,   
operable program or script file. Verify the term and try again.  
At line:1 char:7  
\+ wordpad <<<<

Here, PowerShell simply did not know where to find WordPad, so if the program is not located in one of the standard system folders, you can specify the complete path name like this:

C:programsWindows NTaccessorieswordpad.exe

The term " C:program" is not recognized as a cmdlet,   
function, operable program or script file. Verify the   
term and try again.  
At line:1 char:21  
\+ C:programsWindows <<<< NTaccessorieswordpad.exe

Since the path name includes whitespace characters and because PowerShell interprets them as separators, PowerShell is actually trying to start the program _C:program_. So if path names include spaces, quote it. But that can cause another problem:

"C:programsWindows NTaccessorieswordpad.exe"

C:programsWindows NTaccessorieswordpad.exe

PowerShell now treats quoted information as string and immediately outputs it back to you. You can prefix it with an ampersand to ensure that PowerShell executes the quoted text:

& "C:programsWindows NTaccessorieswordpad.exe"

Finally, WordPad starts.

Wouldn't it be easier to switch from the current folder to the folder with the program we're looking, and then launch the program right there?

Cd "C:programsWindows NTaccessories"  
wordpad.exe

The term "wordpad" is not recognized as a cmdlet,  
function, operable program or script file.  
Verify the term and try again.  
At line:1 char:11  
\+ wordpad.exe <<<<

This results in another red exception because PowerShell wants a relative or absolute path. So, if you don't want to use absolute paths like in the example above, you need to specify the relative path where "." represents the current folder:

.wordpad.exe

### Special Places

You won't need to provide the path name or append the file extension to the command name if the program is located in a folder that is listed in the PATH environment variable. That's why common programs, such as _regedit_, _notepad_, _powershell_,or _ipconfig_ work as-is and do not require you to type in the complete path name or a relative path.

You can put all your important programs in one of the folders listed in the environment variable Path. You can find this list by entering:

$env:Path

C:Windowssystem32;C:Windows;C:WindowsSystem32Wbem;C:program  
FilesSoftexOmniPass;C:WindowsSystem32WindowsPowerShellv1.0;c  
:program FilesMicrosoft SQL Server90Toolsbinn;C:program File  
sATI TechnologiesATI.ACECore-Static;C:program FilesMakeMsi;C:  
program FilesQuickTimeQTSystem

You'll find more on variables, as well as special environment variables, in the [next chapter][3].

As a clever alternative, you can add other folders containing important programs to your _Path_ environment variables, such as:

$env:path += ";C:programsWindows NTaccessories"  
wordpad.exe

After this change, you can launch _WordPad_ just by entering its program name. Note that your change to the environment variable _Path_ is valid only in the current PowerShell session. If you'd like to permanently extend _Path_, you will need to update the path environment variable in one of your profile scripts. Profile scripts start automatically when PowerShell starts and customize your PowerShell environment. Read more about profile scripts in [Chapter 10][4].

* **Watch out for whitespace characters:** If whitespace characters occur in path names, you can enclose the entire path in quotes so that PowerShell doesn't interpret whitespace characters as separators. Stick to single quotes because PowerShell "resolves" text in double quotation marks, replacing variables with their values, and unless that is what you want you can avoid it by using single quotes by default.
* **Specifying a path:** You must tell the console where it is if the program is located somewhere else. To do so, specify the absolute or relative path name of the program.
* **The "&" changes string into commands:** PowerShell doesn't treat text in quotes as a command. Prefix a string with "&" to actually execute it. The "&" symbol will allow you to execute any string just as if you had entered the text directly on the command line.

& ("note" + "pad")

If you have to enter a very long path names, remember (Tab), the key for automatic completion:

C:(Tab)

Press (Tab) again and again until the suggested sub-directory is the one you are looking for. Add a "" and press (Tab) once again to specify the next sub-directory.

The moment a whitespace character turns up in a path, the tab-completion quotes the path and inserts an "&" before it.

## Cmdlets: PowerShell Commands

PowerShell's internal commands are called cmdlets. The mother of all cmdlets is called _Get-Command_:

Get-Command -commandType cmdlet

It retrieves a list of all available cmdlets, whose names always consist of an action (verb) and something that is acted on (noun). This naming convention will help you to find the right command. Let's take a look at how the system works.

If you're looking for a command for a certain task, you can first select the verb that best describes the task. There are relatively few verbs that the strict PowerShell naming conditions permit ([Table 2.2][5]). If you know that you want to obtain something, the proper verb is "get." That already gives you the first part of the command name, and now all you have to do is to take a look at a list of commands that are likely candidates:

Get-Command -verb get

CommandType Name Definition   
\----------- ---- ----------   
Cmdlet Get-Acl Get-Acl [[-Path] ]...  
Cmdlet Get-Alias Get-Alias [[-Name] ...  
Cmdlet Get-Event Get-Event [[-SourceIdentifie...  
Cmdlet Get-EventLog Get-EventLog [-LogName] ] ...  
Cmdlet Get-History Get-History [[-Id] ...  
Cmdlet Get-Host Get-Host [-Verbose] [-Debug]...  
Cmdlet Get-HotFix Get-HotFix [[-Id] ...  
Cmdlet Get-Item Get-Item [-Path]  ...  
Cmdlet Get-ItemProperty Get-ItemProperty [-Path] ] [-...  
Cmdlet Get-Location Get-Location [-PSProvider ...  
Cmdlet New-WebServiceProxy New-WebServiceProxy [-Uri] <...  
Cmdlet Restart-Service Restart-Service [-Name] ...  
Cmdlet Start-Service Start-Service [-Name] **

Omits the specified items. The value of this parameter qualifies the Path parameter. Enter a path element or pattern, such as "*.txt". Wildcards are permitted.

| ----- |
| Required? |  false |
| Position? |  named |
| Default value |   |
| Accept pipeline input?   |  false |
| Accept wildcard characters?  |  false |

**-Filter **

Specifies a filter in the provider's format or language. The value of this parameter qualifies the Path parameter. The syntax of the filter, including the use of wildcards, depends on the provider. Filters are more efficient than other parameters, because the provider applies them when retrieving the objects, rather than having Windows PowerShell filter the objects after they are retrieved.

| ----- |
| Required? |  false |
| Position? |  2 |
| Default value |   |
| Accept pipeline input?   |  false |
| Accept wildcard characters?  |  false |

**-Force **

Allows the cmdlet to get items that cannot otherwise not be accessed by the user, such as hidden or system files. Implementation varies from provider to provider. For more information, see about_Providers. Even using the Force parameter, the cmdlet cannot override security restrictions.

| ----- |
| Required? |  false |
| Position? |  named |
| Default value |   |
| Accept pipeline input?   |  false |
| Accept wildcard characters?  |  false |

**-Include **

Retrieves only the specified items. The value of this parameter qualifies the Path parameter. Enter a path element or pattern, such as "*.txt". Wildcards are permitted.

The Include parameter is effective only when the command includes the Recurse parameter or the path leads to the contents of a directory, such as C:Windows*, where the wildcard character specifies the contents of the C:Windows directory.

| ----- |
| Required? |  false |
| Position? |  named |
| Default value |   |
| Accept pipeline input?   |  false |
| Accept wildcard characters?  |  false |

**-LiteralPath **

Specifies a path to one or more locations. Unlike Path, the value of LiteralPath is used exactly as it is typed. No characters are interpreted as wildcards. If the path includes escape characters, enclose it in single quotation marks. Single quotation marks tell Windows PowerShell not to interpret any characters as escape sequences.

| ----- |
| Required? |  true |
| Position? |  1 |
| Default value |   |
| Accept pipeline input?   |  true (ByPropertyName) |
| Accept wildcard characters?  |  false |

**-Name **

Retrieves only the names of the items in the locations. If you pipe the output of this command to another command, only the item names are sent.

| ----- |
| Required? |  false |
| Position? |  1 |
| Default value |   |
| Accept pipeline input?   |  false |
| Accept wildcard characters?  |  false |

(...)

### Using Named Parameters

Named parameters really work like key-value pairs. You can specify the name of a parameter (which always starts with a hyphen), then a space, then the value you want to assign to the parameter. Let's say you'd like to list all files with the extension _*.exe_ that are located somewhere in the folder _c:windows_ or in one of its sub-directories. You can use this command:

Get-ChildItem -path c:windows -filter *.exe -recurse -name

There are clever tricks to make life easier. You don't have to specify the complete parameter name as long as you type as much of the parameter name to make it unambiguous:

Get-ChildItem -pa c:windows -fi *.exe -r -n

Just play with it: If you shorten parameter names too much, PowerShell will report ambiguities and list the parameters that are conflicting:

Get-ChildItem -pa c:windows -f *.exe -r -n

Get-ChildItem : Parameter cannot be processed because  
the parameter name 'f' is ambiguous. Possible matches  
include: -Filter -Force.  
At line:1 char:14  
\+ Get-ChildItem <<<< -pa c:windows -f *.exe -r -n

You can also turn off parameter recognition. This is necessary only rarely when the argument reads like a parameter name

Write-Host -BackgroundColor

Write-Host : Missing an argument for parameter  
'BackgroundColor'. Specify a parameter of type  
"System.consoleColor" and try again.  
At line:1 char:27  
\+ Write-Host -BackgroundColor <<<<

You can always quote the text. Or you can expressly turn off parameter recognition by typing "--". Everything following these two symbols will no longer be recognized as a parameter:

Write-Host "-BackgroundColor"

-BackgroundColor

Write-Host \-- -BackgroundColor

-BackgroundColor

### Switch Parameter

Sometimes, parameters really are no key-value pairs but simple yes/no-switches. If they're specified, they turn on a certain functionality. If they're left out, they don't turn on the function. For example, the parameter _-recurse_ ensures that _Get-ChildItem_ searches not only the _-path_ specified sub-directories, but all sub-directories. And the switch parameter _-name_ makes _Get-ChildItem_ output only the names of files (as string rather than rich file and folder objects).

The help on _Get-ChildItem_ will clearly identify switch parameters and place a "" after the parameter name:

Get-Help Get-Childitem -parameter recurse

-recurse   
Gets the items in the specified locations and all child  
items of the locations.  
(...)

### Positional Parameters

For some often-used parameters, PowerShell assigns a "position." This enables you to omit the parameter name altogether and simply specify the arguments. With positional parameters, your arguments need to be submitted in just the right order according to their position numbers.

That's why you could have expressed the command we just discussed in one of the following ways:

Get-ChildItem c:windows *.exe -recurse -name  
Get-ChildItem -recurse -name c:windows *.exe  
Get-ChildItem -name c:windows *.exe -recurse

In all three cases, PowerShell will identify and eliminate the named arguments _-recurse_ and _-name_ first because they are clearly specified. The remaining arguments are "unnamed" and need to be assigned based on their position:

Get-ChildItem c:windows *.exe

The parameter _-path_ has the position _1_, and no value has yet been assigned to it. So, PowerShell attaches the first remaining argument to this parameter.

-path   
Specifies a path to one or more locations. Wildcards are  
permitted. The default location is the current directory (.).

Required? false  
Position? 1  
Standard value used   
Accept pipeline input? true (ByValue, ByPropertyName)  
Accept wildcard characters? true

The parameter _-filter_ has the position 2. Consequently, it is assigned the second remaining argument. The position specification will make it easier to use a cmdlet because you don't have to specify any parameter names for the most frequently and commonly used parameters.

Here is a tip: In daily interactive PowerShell scripting, you will want short and fast commands so use aliases, positional parameters, and abbreviated parameter names. Once you write PowerShell scripts, you should not use these shortcuts. Instead, you can use the true cmdlet names and stick to fully named parameters. One reason is that scripts can be portable and not depend on specific aliases you may have defined. Second, scripts are more complex and need to be as readable and understandable as possible. Named parameters help other people better understand what you are doing.

### Common Parameters

Cmdlets also support a set of generic "CommonParameters":

  
This cmdlet supports the common parameters: -Verbose,  
-Debug, -ErrorAction, -ErrorVariable, and -OutVariable.  
For more information, type "get-help about_commonparameters".

These parameters are called "common" because they are permitted for (nearly) all cmdlets and behave the same way.

| ----- |
| **Common Parameter** |  **Type** |  **Description** |
| _-Verbose_ |  Switch |  Generates as much information as possible. Without this switch, the cmdlet restricts itself to displaying only essential information |
| _-Debug_ |  Switch |  Outputs additional warnings and error messages that help programmers find the causes of errors. You can find more information in [Chapter 11][6]. |
| _-ErrorAction_ |  Value |  Determines how the cmdlet responds when an error occurs. Permitted values:  
_NotifyContinue:_ Reports error and continues (default)  
_NotifyStop:_ Reports error and stops  
_SilentContinue:_ Displays no error message, continues  
_SilentStop:_ Displays no error message, stops  
_Inquire:_ Asks how to proceed  
You can find more information in [Chapter 11][6]. |
| _-ErrorVariable_ |  Value |  Name of a variable in which in the event of an error information about the error is stored. You can find more information in [Chapter 11][6]. |
| _-OutVariable_ |  Value |  Name of a variable in which the result of a cmdlet is to be stored. This parameter is usually superfluous because you can directly assign the value to a variable. The difference is that it will no longer be displayed in the console if you assign the result to a variable.

$result = Get-ChildItem

It will be output to the console and stored in a variable if you assign the result additionally to a variable:

Get-ChildItem -OutVariable result

 |

**Table 2.3:** Common parameters in effect for (nearly) all cmdlets

## Aliases: Shortcuts for Commands

Cmdlet names with their verb-noun convention are very systematic, yet not always practical. In every day admin life, you will want short and familiar commands. This is why PowerShell has a built-in alias system as it comes with a lot of pre-defined aliases. This is why in PowerShell, both Windows admins and UNIX admins, can list folder contents with commands they are accustom to using. There are pre-defined "historic" aliases called "dir" and "ls" which both point to the PowerShell cmdlet _Get-ChildItem._

Get-Command dir  
CommandType Name Definition  
\----------- ---- ----------  
Alias dir Get-ChildItem

Get-Alias -Definition Get-Childitem  
CommandType Name Definition  
\----------- ---- ----------  
Alias dir Get-ChildItem  
Alias gci Get-ChildItem  
Alias ls Get-ChildItem

Get-ChildItem c:Dir c:ls c:  

So, aliases have two important tasks in PowerShell:

* **Historical:** NFind and use important cmdlets by using familiar command names you know from older shells
* **Speed:** Fast access to cmdlets using short alias names instead of longer formal cmdlet names

### Resolving Aliases

Use these lines if you'd like to know what "genuine" command is hidden in an alias:

$alias:Dir

Get-ChildItem

$alias:ls

Get-ChildItem

Get-Command Dir

Get-Command Dir  
CommandType Name Definition  
\----------- ---- ----------  
Alias dir Get-ChildItem

_$alias:Dir_ lists the element _Dir_ of the drive _alias:_. That may seem somewhat surprising because there is no drive called _alias:_ in the classic console. PowerShell supports many additional virtual drives, and _alias:_ is only one of them. If you want to know more, the cmdlet _Get-PSDrive_ lists them all. You can also list _alias:_ like any other drive with _Dir_. The result would be a list of aliases in their entirety:

Dir alias:

CommandType Name Definition  
\----------- ---- ----------  
alias ac Add-Content  
alias asnp Add-PSSnapin  
alias clc Clear-Content  
(...)

_Get-Command_ can also resolve aliases. Whenever you want to know more about a particular command, you can submit it to _Get-Command_, and it will tell you the command type and where it is located.

You can also get the list of aliases using the cmdlet _Get-Alias_. You will receive a list of individual alias definitions:

Get-alias -name Dir  
Get-ChildItem

You can use the parameter _-Definition_ to list all aliases for a given cmdlet.

Get-alias -definition Get-ChildItem  

This will get you all aliases pointing to the cmdlet or command you submitted to _-Definition._

As it turns out, there's even a third alias for _Get-ChildItem_ called "_gci_". There are more approaches to the same result. The next examples find alias definitions by doing a keyword search and by grouping:

Dir alias: | Out-String -Stream | Select-String "Get-ChildItem"

Count Name Group  
\----- ---- -----  
1 Add-Content {ac}  
1 Add-PSSnapin {asnp}  
1 Clear-Content {clc}  
1 Clear-Item {cli}  
1 Clear-ItemProperty {clp}  
1 Clear-Variable {clv}  
3 Copy-Item {cpi, cp, copy}  
1 Copy-ItemProperty {cpp}  
1 Convert-Path {cvpa}  
1 Compare-Object {diff}  
1 Export-Alias {epal}  
1 Export-Csv {epcsv}  
1 Format-Custom {fc}  
1 Format-List {fl}  
2 ForEach-Object {foreach, %}  
1 Format-Table {ft}  
1 Format-Wide {fw}  
1 Get-Alias {gal}  
3 Get-Content {gc, cat, type}  
3 Get-ChildItem {gci, ls, Dir}  
1 Get-Command {gcm}  
1 Get-PSDrive {gdr}  
3 Get-History {ghy, h, history}  
1 Get-Item {gi}  
2 Get-Location {gl, pwd}  
1 Get-Member {gm}  
1 Get-ItemProperty {gp}  
2 Get-Process {gps, ps}  
1 Group-Object {group}  
1 Get-Service {gsv}  
1 Get-PSSnapin {gsnp}  
1 Get-Unique {gu}  
1 Get-Variable {gv}  
1 Get-WmiObject {gwmi}  
1 Invoke-Expression {iex}  
2 Invoke-History {ihy, r}  
1 Invoke-Item {ii}  
1 Import-Alias {ipal}  
1 Import-Csv {ipcsv}  
3 Move-Item {mi, mv, move}  
1 Move-ItemProperty {mp}  
1 New-Alias {nal}  
2 New-PSDrive {ndr, mount}  
1 New-Item {ni}  
1 New-Variable {nv}  
1 Out-Host {oh}  
1 Remove-PSDrive {rdr}  
6 Remove-Item {ri, rm, rmdir, del...}  
2 Rename-Item {rni, ren}  
1 Rename-ItemProperty {rnp}  
1 Remove-ItemProperty {rp}  
1 Remove-PSSnapin {rsnp}  
1 Remove-Variable {rv}  
1 Resolve-Path {rvpa}  
1 Set-Alias {sal}  
1 Start-Service {sasv}  
1 Set-Content {sc}  
1 Select-Object {select}  
1 Set-Item {si}  
3 Set-Location {sl, cd, chdir}  
1 Start-Sleep {sleep}  
1 Sort-Object {sort}  
1 Set-ItemProperty {sp}  
2 Stop-Process {spps, kill}  
1 Stop-Service {spsv}  
2 Set-Variable {sv, set}  
1 Tee-Object {tee}  
2 Where-Object {where, ?}  
2 Write-Output {write, echo}  
2 Clear-Host {clear, cls}  
1 Out-Printer {lp}  
1 Pop-Location {popd}  
1 Push-Location {pushd}

### Creating Your Own Aliases

_Set-Alias_ adds additional alias definitions. You can actually override commands with aliases since aliases have precedence over other commands. Take a look at the next example:

Edit  
Set-Alias edit notepad.exe  
Edit

_Edit_ typically launches the console-based Editor program. You can press (Alt)+(F) and then (X) to exit without completely closing the console window.

If you create a new alias called "Edit" and set it to "notepad.exe", the command _Edit_ will be re-programmed. The next time you enter it, PowerShell will no longer run the old Editor program, but the _Notepad_.

$alias:edit

### Removing or Permanently Keeping an Alias

How do you remove aliases? You don't. All new aliases are discarded as soon as you exit PowerShell. All of your own aliases will be gone the next time you start PowerShell. "Built-in" aliases like "dir" and "cd" will still be there.

Try these options if you'd like to keep your own aliases permanently:

* **Manually each time:** Set your aliases after every start manually using _Set-Alias_. That is, of course, rather theoretical.
* **Automated in a profile:** Let your alias be set automatically when PowerShell starts: add your aliases to a start profile. You'll learn how to do this in [Chapter 10][4].
* **Import and export:** You can use the built-in import and export function for aliases.

For example, if you'd like to export all currently defined aliases as a list to a file, enter:

Export-Alias

Because you haven't entered any file names after _Export-Alias_, the command will ask you what the name are under which you want to save the list. Type in:

_alias1_ (Enter)

The list will be saved. You can look at the list afterwards and manipulate it. For example, you might want the list to include a few of your own alias definitions:

Notepad alias1

You can import the list to activate the alias definitions:

Import-Alias alias1

Import-Alias : Alias not allowed because an alias with the  
name "ac" already exists.  
At line:1 char:13  
\+ Import-Alias <<<< alias1

_Import-Alias_ will notify you that it cannot create some aliases of the list because these aliases already exist. Specify additionally the option _-Force_ to ensure that _Import-Alias_ overwrites existing aliases:

Import-Alias alias1 -Force

You can add the _Import-Alias_ instruction to your start profile and specify a permanent path to the alias list. This will make PowerShell automatically read this alias list when it starts. Later, you can add new aliases. Then, it will suffice to update the alias list with _Export-Alias_ and to write over the old file. This is one way for you to keep your aliases permanently.

### Overwriting and Deleting Aliases

You can overwrite aliases with new definitions any time as long as an alias is not write-protected. Just redefine the alias with the cmdlet _Set-Alias_. Use this command if you'd like to remove an alias completely and don't want to wait until you end PowerShell:

Del alias:edit

This instruction deletes the "Edit" alias. Here, the uniform provider approach becomes evident. The very same "Del" command will allow you to delete files and sub-directories in the file system as well. Perhaps you're already familiar with the command from the classic console:

Del C:garbage.txt

Here is an example that finds all aliases that point to no valid target, which is a great way of finding outdated or damaged aliases:

Get-Alias | ForEach-Object {  
if (!(Get-Command $_.Definition -ea SilentlyContinue)) {$_}}

## Functions: PowerShell-"Macros"

Aliases are simple shortcuts to call commands with another name (shortcut names), or to make the transition to PowerShell easier (historic aliases). However, the arguments of a command can never be included in an alias. You will need to use functions if you want that.

### Calling Commands with Arguments

If you find yourself using the command ping frequently to verify network addresses, you may want to make this easier by creating a shortcut that not only calls _ping.exe_, but also appends standard arguments to it. Let's see how you can automate this call:

Ping -n 1 -w 100 10.10.10.10

Aliases won't work here because they can't specify command arguments. Functions can:

function quickping { ping -n 1 -w 100 $args }  
quickping 10.10.10.10

Pinging 10.10.10.10 with 32 bytes of data:  
Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Ping statistics for 10.10.10.10:  
Packets: Sent = 1, Received = 1, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms

Set-Alias qp quickping  
qp 10.10.10.10

Pinging 10.10.10.10 with 32 bytes of data:  
Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Ping statistics for 10.10.10.10:  
Packets: Sent = 1, Received = 1, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms

Unlike alias definitions, functions can run arbitrary code that is placed in brackets. Any additional information a user submitted to the function can be found in $args if you don't specify explicit parameters. _$args_ is an array and holds every piece of extra information submitted by the caller as separate array elements. You'll read more about functions later.

## Invoking Files and Scripts

To run files (like documents or scripts), PowerShell uses the same rules that apply to executables: either, you specify an absolute or relative path, or the file needs to be located in one of the special trustworthy folders defined in the _Path_ environment variable.

  
Get-Process | ConvertTo-Html | Out-File test.htm

  
test.htm

The term "test.htm" is not recognized as a cmdlet, function,  
operable program, or script file. Verify the term and try again.  
At line:1 char:8  
\+ test.htm <<<<

  
.test.htm

### Starting Scripts

Scripts and batch files are pseudo-executables. The script itself is just a plain text file, but it can be run by its associated script interpreter.

### Running Batch Files

Batch files are text files with the extension ".bat". They may include all the commands allowed in a classic cmd.exe console. When a batch file is opened, the classic console immediately starts to execute the commands it contains. Let's check it out. First, create this test file:

Notepad ping.bat

Now enter this text:

@echo off  
echo An attacker can do dangerous things here  
pause  
Dir %windir%  
pause  
Dir %windir%system

Save the text and close _Notepad_. Your batch file is ready for action. Try to launch the batch file by entering its name:

Ping

The batch file won't run. Because it has the same name and you didn't specify any IP address or Web site address, the _ping_ command spits out its internal help message. If you want to launch your batch file, you're going to have to specify either the relative or absolute path name.

.ping

Your batch file will open and then immediately runs the commands it contains.

PowerShell has just defended a common attack. If you were using the classic console, you would have been tricked by the attacker. Switch over to the classic console to see for yourself:

Cmd  
Ping 10.10.10.10

An attacker can do dangerous things here  
Press any key . . .

If an attacker had smuggled a batch file named "ping.bat" into your current folder, then the _ping_ command, harmless though it might seem, could have had catastrophic consequences. A classic console doesn't distinguish between files and commands. It will look _first_ in the current folder, find the batch file, and execute it immediately. Such a mix-up will never happen in the PowerShell console. So, return to your much-safer PowerShell environment:

Exit

### Running VBScript Files

VBScript is another popular automation language as its scripts are tagged with the file extension ".vbs". What we have just discussed about batch files also applies to these scripts:

Notepad test.vbs

Enter this VBScript code in Notepad and save it as _test.vbs_:

result = InputBox("Enter your name")  
WScript.Echo result

Next, run the script:

Cscript.exec:samplestest.vbs (Enter)

The script opens a small dialog window and asks for some information. The information entered into the dialog is then output to the console where PowerShell can receive it. This way, you can easily merge VBScript logic into your PowerShell solutions. You can even store the results into a variable and process it inside PowerShell:

$name = cscript.exe c:samplestest.vbs

"Your name is $name"

If you do not get back the name you entered into the dialog, but instead the VBScript copyright information, then the VBScript interpreter has output the copyright information first, which got in the way. The safest way is to turn off the copyright message explicitly:

$name = cscript.exe //NOLOGO c:samplestest.vbs

You can also generally turn off VBScript logos. Try calling wscript.exe to open the settings dialog, and turn off the logo.

### Running PowerShell Scripts

PowerShell has its own script files with the file extension ".ps1". While you will learn much more about PowerShell scripts in [Chapter 10][4], you already know enough to write your first script. Use the Windows editor to create and open your first script:

Notepad $env:temptest.ps1

You can now enter any PowerShell code you want, and save the file. Once saved, you can also open your script with more sophisticated and specialized script editors. PowerShell comes with an editor called PowerShell ISE, and here is how you'd open the file you created with Notepad:

Ise $env:temptest.ps1

Try to run your script after you've created it:

.test.ps1

File "C:UsersUserAtest.ps1" cannot be loaded because the  
execution of scripts is disabled on this system. Please see  
"get-help about_signing" for more details.  
At line:1 char:10  
\+ .test.ps1 <<<<

You'll probably receive an error message similar to the one in the above example. All PowerShell scripts are initially disabled. You need to allow PowerShell to execute scripts first. This only needs to be done once:

Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

This grants permission to run locally stored PowerShell scripts. Scripts from untrusted sources, such as the Internet, will need to carry a valid digital signature or else they won't run. This is to protect you from malicious scripts, but if you want to, you can turn this security feature off. Replace _RemoteSigned_ with _Bypass_. The implications of signatures and other security settings will be discussed in [Chapter 10][4]. For now, the line above is enough for you to experiment with your own PowerShell scripts. To restore the original setting, set the setting to _Undefined_:

Set-ExecutionPolicy Undefined -Scope CurrentUser

To get a complete picture, also try using the -List parameter with _Get-ExecutionPolicy_:

Get-ExecutionPolicy -List  
Scope            ExecutionPolicy  
\-----            ---------------  
MachinePolicy    Undefined  
UserPolicy       Undefined  
Process          Undefined  
CurrentUser      RemoteSigned  
LocalMachine     Restricted

You now see all execution policies. The first two are defined by Group Policy so a corporation can centrally control execution policy. The scope "Process" refers to your current session only. So, you can use this scope if you want to only temporarily change the execution policy. No other PowerShell session will be affected by your change. "CurrentUser" will affect only you, but no other users. That's how you can change this scope without special privileges. "LocalMachine," which is the only scope available in PowerShell v.1, will affect any user on your machine. This is the perfect place for companies to set initial defaults that can be overridden. The default setting for this scope is "Restricted."

The effective execution policy is the first policy from top to bottom in this list that is not set to "Undefined." If all policies are set to "Undefined," then scripts are prohibited.

Note: To turn off signature checking altogether, you can set the execution policy to "Bypass." This can be useful if you must run scripts regularly that are stored on file servers outside your domain. Otherwise, you may get security warnings and confirmation dialogs. Always remember: execution policy exists to help and protect you from potentially malicious scripts. If you are confident you can safely identify malicious scripts, then nothing is wrong by turning off signature checking. However, we recommend not using the "Bypass" setting if you are new to PowerShell.

## Summary

The PowerShell console can run all kinds of commands interactively. You simply enter a command and the console will return the results.

Cmdlets are PowerShell's own internal commands. A cmdlet name is always composed of a verb (what it does) and a noun (where it acts upon).

To find a particular command, you can either guess or use _Get-Command_. For example, this will get you a list if you wanted to find all cmdlets dealing with event logs:

Get-Command -Noun EventLog

Search for the verb "Stop" to find all cmdlets that stop something:

Get-Command -Verb Stop

You can also use wildcards. This will list all cmdlets with the keyword "computer":

Get-Command *computer* -commandType cmdlet

Once you know the name of a particular cmdlet, you can use _Get-Help_ to get more information. This function will help you view help information page by page:

Get-Help Stop-Computer

Help Stop-Computer -examples

Help Stop-Computer -parameter *

Cmdlets are just one of six command types you can use to get work done:
* **Alias:** Shortcuts to other commands, such as _dir_ or _ls_
* **Function:** "Macros" that run code and resemble "self-made" new commands
* **Cmdlet:** Built-in PowerShell commands
* **Application:** External executables, such as _ipconfig_, _ping_ or _notepad_
* **PowerShell scripts:** Files with extension *.ps1 which can contain any valid PowerShell code
* **Other files:** Batch files, VBScript script files, or any other file associated with an executable

If commands are ambiguous, PowerShell will stick to the order of that list. So, since the command type "Alias" is at the top of that list, if you define an alias like "ping", it will be used instead of _ping.exe_ and thus can override any other command type.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2011/12/15/interactive-powershell.aspx#table2.1
[2]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch2_2D00_1.png
[3]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/02/chapter-3-variables.aspx
[4]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-10-scripts.aspx
[5]: http://powershell.com/cs/blogs/ebookv2/archive/2011/12/15/interactive-powershell.aspx#table2.2
[6]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-11-error-handling.aspx
