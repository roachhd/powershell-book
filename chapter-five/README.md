# Chapter 5. The PowerShell Pipeline 

The PowerShell pipeline chains together a number of commands similar to a production assembly. So, one command hands over its result to the next, and at the end, you receive the result.

**Topics Covered:**

Using the PowerShell Pipeline 
Object-oriented Pipeline 
Text Not Converted Until the End 
Table 5.1: Typical pipeline cmdlets and functions 
Streaming: Real-time Processing or Blocking Mode? 
"Blocking" Pipeline Commands 
Converting Objects into Text 
Making Object Properties Visible 
Formatting Pipeline Results 
Displaying Particular Properties 
Using Wildcard Characters 
Scriptblocks and "Aggregate" Properties 
Changing Column Headings 
Optimizing Column Width 
Sorting and Grouping Pipeline Results 
Sort Object and Hash Tables 
Grouping Information 
Using Grouping Expressions 
Filtering Pipeline Results 
Limiting Number of Objects 
Analyzing and Comparing Results 
Statistical Calculations 
Exporting Pipeline Results 
Suppressing Results 
HTML Outputs

The PowerShell pipeline chains together a number of commands similar to a production assembly. So, one command will hand over its result to the next, and at the end, you will receive the result.

## Using the PowerShell Pipeline

Command chains are really nothing new. The old console was able to forward (or "pipe") the results of a command to the next with the "pipe" operator "|". One of the more known usages was to pipe data to the tool _more_, which would then present the data screen page by screen page:

Dir | more

In contrast to the traditional concept of text piping, the PowerShell pipeline will take an object-oriented approach and implement it in real time. Have a look:

Dir | Sort-Object Length | Select-Object Name, Length |   
ConvertTo-Html | Out-File report.htm  
.report.htm

It returns an HTML report on the _windows_ directory contents sorted by file size. All of this can start with a _Dir_ command, which then passes its result to _Sort-Object_. The sorted result will then get limited to only the properties you want in the report. _ConvertTo-Html_ will convert the objects to HTML, which is then written to a file.

### Object-oriented Pipeline

What you see here is a true object-oriented pipeline so the results from a command remain rich objects. Only at the end of the pipeline will the results be reduced to text or HTML or whatever you choose for output.

Take a look at _Sort-Object_. It will sort the directory listing by file size. If the pipeline had simply fed plain text into _Sort-Object_, you would have had to tell _Sort-Object_ just where the file size information was to be found in the raw text. You would also have had to tell _Sort-Object_ to sort this information numerically and not alphabetically. Not so here. All you need to do is tell _Sort-Object_ which object's property you want to sort. The object nature tells _Sort-Object_ all it needs to know: where the information you want to sort is found and whether it is numeric or letters.

You only have to tell _Sort-Object_ which object's property to use for sorting because PowerShell will send results as rich .NET objects through the pipeline. _Sort-Object_ does the rest automatically. Simply replace _Length_ with another object's property, such as _Name_ or _LastWriteTime_, to sort according to these criteria. Unlike text, information in an object is clearly structured: this is a crucial PowerShell pipeline advantage.

### Text Not Converted Until the End

The PowerShell pipeline is always used, even when you provide only a single command. PowerShell will attach to your input the cmdlet _Out-Default_, which converts the resulting objects into text at the end of the pipeline.

Even a simple _Dir_ command is appended internally and converted into a pipeline command:

    Dir $env:windir | Out-Default

Of course, the real pipeline benefits show only when you start adding more commands. The chaining of several commands will allow you to use commands like Lego building blocks to assemble a complete solution from single commands. The following command will output only a directory's text files listing in alphabetical order:

    Dir $env:windir *.txt | Sort-Object

The cmdlets in Table 5.1 have been specially developed for the pipeline and the tasks frequently performed in it. They will all be demonstrated in the following pages of this chapter.

Just make sure that the commands you use in a pipeline actually do process information from the pipeline. While it is technically OK, the following line is really useless because _notepad.exe_ cannot process pipeline results:

    Dir $env:windir | Sort-Object | notepad

If you'd like to open pipeline results in an editor, you can put the results in a file first and then open the file with the editor:

    Dir $env:windir | Sort-Object | Out-File result.txt; notepad result.txt

| ----- |
| **Cmdlet/Function** |  **Description** |
| _Compare-Object_ |  Compares two objects or object collections and marks their differences |
| _ConvertTo-Html_ |  Converts objects into HTML code |
| _Export-Clixml_ |  Saves objects to a file (serialization) |
| _Export-Csv_ |  Saves objects in a comma-separated values file |
| _ForEach-Object_ |  Returns each pipeline object one after the other |
| _Format-List_ |  Outputs results as a list |
| _Format-Table_ |  Outputs results as a table |
| _Format-Wide_ |  Outputs results in several columns |
| _Get-Unique_ |  Removes duplicates from a list of values |
| _Group-Object_ |  Groups results according to a criterion |
| _Import-Clixml_ |  Imports objects from a file and creates objects out of them (deserialization) |
| _Measure-Object_ |  Calculates the statistical frequency distribution of object values or texts |
| _more_ |  Returns text one page at a time |
| _Out-File_ |  Writes results to a file |
| _Out-Host_ |  Outputs results in the console |
| _Out-Host -paging_ |  Returns text one page at a time |
| _Out-Null_ |  Deletes results |
| _Out-Printer_ |  Sends results to printer |
| _Out-String_ |  Converts results into plain text |
| _Select-Object_ |  Filters properties of an object and limits number of results as requested |
| _Sort-Object_ |  Sorts results |
| _Tee-Object_ |  Copies the pipeline's contents and saves it to a file or a variable |
| _Where-Object_ |  Filters results according to a criterion |

**Table 5.1:** Typical pipeline cmdlets and functions

### Streaming: Real-time Processing or Blocking Mode?

When you combine several commands in a pipeline, you'll want to understand when each separate command will actually be processed: consecutively or at the same time? The pipeline will process the results in real time, at least when the commands chained together in the pipeline support real-time processing. That's why there are two pipeline modes:

* **Sequential (slow) mode****:** In sequential mode, pipeline commands are executed one at a time. So the command's results are passed on to the next one only after the command has completely performed its task. This mode is slow and hogs memory because results are returned only after all commands finish their work and the pipeline has to store the entire results of each command. The sequential mode basically corresponds to the variable concept that first saves the result of a command to a variable before forwarding it to the next command.
* **Streaming Mode**** (quick):** The streaming mode immediately processes each command result. Every single result is passed directly onto the subsequent command. It will rush through the entire pipeline and is immediately output. This quick mode saves memory because results are output while the pipeline commands are still performing their tasks, and only one element is travelling the pipeline at a time. The pipeline doesn't have to store all of the command's results, but only one single result at a time.

### "Blocking" Pipeline Commands

Sorting can only take place when all results are available. That is why Sort-Object is an example of a "blocking" pipeline command, which will first collect all data before it hands over the sorted result to the next command. This also means there can be long processing times and it can even cause instability if you don't pay attention to memory requirements:

    # Attention: danger!
    Dir C: -recurse | Sort-Object

If you execute this example, you won't see any signs of life from PowerShell for a long time. If you let the command run too long, you may even run out of memory.

Here _Dir_ returns all files and directors on your drive C:. These results are passed by the pipeline to _Sort-Object_, and because _Sort-Object_ can only sort the results after all of them are available, it will collect the results as they come in. Those results eventually block too much memory for your system to handle. The two problem areas in sequential mode are:

First problem: You won't see any activity as long as data is being collected. The more data that has to be acquired, the longer the wait time will be for you. In the above example, it can take several minutes.

Second problem: Because enormous amounts of data have to be stored temporarily before _Sort-Object_ can process them, the memory space requirement is very high. In this case, it's even higher so that the entire Windows system will respond more and more clumsily until finally you won't be able to control it any longer.

That's not all. In this specific case, confusing error messages may pile up. If you have _Dir_ output a complete recursive folder listing, it may encounter sub-directories where you have no access rights. While _Sort-Object_ continues to collect results (so no results appear), error messages are not collected by _Sort-Object_ and appear immediately. Error messages and results get out of sync and may be misinterpreted.

Whether a command supports streaming is up to the programmer. For _Sort-Object_, there are technical reasons why this command must first wait for all results. Otherwise, it wouldn't be able to sort the results. If you use commands that are not designed for PowerShell then their authors had no way to implement the special demands of PowerShell. For example, it will work if you use the traditional command _more.com_ to output information one page at a time, but _more.com_ is also a blocking command that could interrupt pipeline streaming:

  
Dir | more.com

  
Dir c: -recurse | more.com

But also genuine PowerShell cmdlets, functions, or scripts can block pipelines if the programmer doesn't use streaming. Surprisingly, PowerShell developers forgot to add streaming support to the integrated _more_ function. This is why _more_ essentially doesn't behave much differently than the ancient _more.com_ command:

    # If the preceding command can execute its task quickly, you may not notice that it can be   
    # a block:
    Dir | more.com
    # If the preceding command requires much time, its blocking action may cause issues:
    Dir c: -recurse | more.com

Tip: Use _Out-Host -Paging_ instead of more! _Out-Host_ is a true PowerShell cmdlet and will support streaming:

    Dir c: -recurse | Out-Host -paging

## Converting Objects into Text

At the end of a day, you want commands to return visible results, not objects. So, while results stay rich data objects while traveling the pipeline, at the end of the pipeline, they must be converted into text. This is done by (internally) adding _Out-Default_ to your input. The following commands are identical:

    Dir
    Dir | Out-Default

_Out-Default_ will transform the pipeline result into visible text. To do so, it will first call _Format-Table_ (or _Format-List_ when there are more than five properties to output) internally, followed by _Out-Host_. _Out-Host_ will output the text in the console. So, this is what happens internally:

    Dir | Format-Table | Out-Host

### Making Object Properties Visible

To really see all the object properties and not just the ones that PowerShell "thinks" are important, you can use Format-Table and add a "*" to select all object properties.

    Dir | Format-Table *

    PSPat PSPar PSChi PSDri PSPro PSIsC Mode Name Pare Exis Root Full Exte Crea Crea Last Last Last Last Attr
    h     entPa ldNam ve    vider ontai           nt     ts      Name nsio tion tion Acce Acce Writ Writ ibut
          th    e                   ner                               n    Time Time ssTi ssTi eTim eTim   es
                                                                                Utc  me   meUt e    eUtc
                                                                                          c
    ----- ----- ----- ----- ----- ----- ---- ---- ---- ---- ---- ---- ---- ---- ---- ---- ---- ---- ---- ----
    Mi... Mi... Ap... C     Mi...  True d... A... T... True C:  C...      2... 2... 2... 2... 2... 2... ...y
    Mi... Mi... Ba... C     Mi...  True d... B... T... True C:  C...      2... 2... 2... 2... 2... 2... ...y
    Mi... Mi... Co... C     Mi...  True d... C... T... True C:  C...      1... 1... 1... 1... 1... 1... ...y
    Mi... Mi... Debug C     Mi...  True d... D... T... True C:  C...      2... 2... 2... 2... 2... 2... ...y
    Mi... Mi... De... C     Mi...  True d... D... T... True C:  C...      1... 1... 3... 3... 3... 3... ...y

You now get so much information that columns shrink to an unreadable format.

For example, if you'd prefer not to reduce visual display because of lack of space, you can use the _-Wrap_ parameter, like this:

    Dir | Format-Table * -wrap

Still, the horizontal table design is unsuitable for more than just a handful of properties. This is why PowerShell will use _Format-List_, instead of _Format-Table_, whenever there are more than five properties to display. You should do the same:

    Dir | Format-List *

You will now see a list of several lines for each object's property. For a folder, it might look like this:

    PSPath            : Microsoft.PowerShell.CoreFileSystem::C:UsersTobias WeltnerMusic
    PSParentPath      : Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltner
    PSChildName       : Music
    PSDrive           : C
    PSProvider        : Microsoft.PowerShell.CoreFileSystem
    PSIsContainer     : True
    Mode              : d-r--
    Name              : Music
    Parent            : Tobias Weltner
    Exists            : True
    Root              : C:
    FullName          : C:UsersTobias WeltnerMusic
    Extension         :
    CreationTime      : 13.04.2007 01:54:53
    CreationTimeUtc   : 12.04.2007 23:54:53
    LastAccessTime    : 10.05.2007 21:37:26
    LastAccessTimeUtc : 10.05.2007 19:37:26
    LastWriteTime     : 10.05.2007 21:37:26
    LastWriteTimeUtc  : 10.05.2007 19:37:26
    Attributes        : ReadOnly, Directory

A file has slightly different properties:

    PSPath            : Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltnerviews.PS1
    PSParentPath      : Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltner
    PSChildName       : views.PS1
    PSDrive           : C
    PSProvider        : Microsoft.PowerShell.CoreFileSystem
    PSIsContainer     : False
    Mode              : -a---
    Name              : views.PS1
    Length            : 4045
    DirectoryName     : C:UsersTobias Weltner
    Directory         : C:UsersTobias Weltner
    IsReadOnly        : False
    Exists            : True
    FullName          : C:UsersTobias Weltnerviews.PS1
    Extension         : .PS1
    CreationTime      : 18.09.2007 16:30:13
    CreationTimeUtc   : 18.09.2007 14:30:13
    LastAccessTime    : 18.09.2007 16:30:13
    LastAccessTimeUtc : 18.09.2007 14:30:13
    LastWriteTime     : 18.09.2007 16:46:12
    LastWriteTimeUtc  : 18.09.2007 14:46:12
    Attributes        : Archive

The property names are located on the left and their content on the right. You now know how to find out which properties an object contains.

### Formatting Pipeline Results

Transforming objects produced by the pipeline is carried out by formatting cmdlets. There are four choices:

    Get-Command -verb format
    CommandType     Name                             Definition
    -----------     ----                             ----------
    Cmdlet          Format-Custom                    Format-Custom [[-Property] ] [-De...
    Cmdlet          Format-List                      Format-List [[-Property] ] [-Grou...
    Cmdlet          Format-Table                     Format-Table [[-Property] ] [-Aut...
    Cmdlet          Format-Wide                      Format-Wide [[-Property] ] [-AutoSi...

These formatting cmdlets are not just useful for converting all of an object's properties into text, but you can also select the properties you want to see.

### Displaying Particular Properties

To accomplish this, you type the property that you want to see and not just an asterisk behind the cmdlet. If you do not want to explicitly use a table or list format, it is considered best practice to use _Select-Object_ rather than _Format-*_ because _Select-Object_ will automatically determine the best formatting and will also return objects that can be processed by subsequent cmdlets. When you use Format-* cmdlets, objects are converted into formatting information, which can only be interpreted by _Out-*_ cmdlets which is why _Format-*_ cmdlets must be used only at the end of your pipeline.

The next instruction will retrieve you a directory listing with only _Name_ and _Length_. Because sub-directories don't have a property called _Length_, you will see that the _Length_ column for the sub-directory is empty:

    Dir | Select-Object Name, Length
    Name                                                                  Length
    ----                                                                  ------
    Sources
    Test
    172.16.50.16150.dat                                                   16
    172.16.50.17100.dat                                                   16
    output.htm                                                            10834
    output.txt                                                            1338

### Using Wildcard Characters

Wildcard characters are allowed. So, the next command will get you information about your video controller and output all properties that have a resolution keyword:

    Get-WmiObject Win32_VideoController | Select-Object *resolution*
                CurrentHorizontalResolution               CurrentVerticalResolution
                ---------------------------               -------------------------
                                       1680                                    1050

### Scriptblocks and "Aggregate" Properties

Script blocks can be used as columns as they basically act as PowerShell instructions included in brackets that work like synthetic properties to calculate their value. Within a script block, the variable $_ will contain the actual object. The script block could convert the Length property into kilobytes if you'd like to output file sizes in kilobytes rather than bytes:

    Dir | Select-Object name, { [int]($_.Length/1KB) }
    Name                                                                      [int]($_.Length/1KB)
    ----                                                                       ---------------------
    output.htm                                                                                  11
    output.txt                                                                                  13
    backup.pfx                                                                                   2
    cmdlet.txt                                                                                  23

Or maybe you'd like your directory listing to show how many days have passed since a file or a folder was last modified. By using the _New-TimeSpan_ cmdlet, you can calculate how much time has elapsed up to the current date. To see how this works, you can look at the line below as an example that calculates the time difference between January 1, 2000, and the current date:/p>

    New-TimeSpan "01/01/2000"
    Days              : 4100
    Hours             : 21
    Minutes           : 13
    Seconds           : 15
    Milliseconds      : 545
    Ticks             : 3543163955453834
    TotalDays         : 4100,8842077012
    TotalHours        : 98421,2209848287
    TotalMinutes      : 5905273,25908972
    TotalSeconds      : 354316395,545383
    TotalMilliseconds : 354316395545,383

Use this script block to output how many days have elapsed from the _LastWriteTime_ property up to the current date and to read it out in its own column:

    {(New-TimeSpan $_.LastWriteTime ).Days}

_Dir_ would then return a sub-directory listing that shows how old the file is in days:

    Dir | Select-Object Name, Length, {(New-TimeSpan $_.LastWriteTime ).Days}
    Name                 Length (New-TimeSpan $_.LastWriteTime (Get-Date)).Days
    ----                 ------ -----------------------------------------------
    Application data                                                         61
    Backup                                                                   55
    Contacts                                                                158
    Debug                                                                    82
    Desktop                                                                  19
    Documents                                                                 1
    (...)

### Changing Column Headings

When you use synthetic properties, you will notice that column headings look strange because PowerShell puts code in them that computes the column contents. However, after reading the last chapter, you now know that you can use a hash table to format columns more effectively and that you can also rename them:

    $column = @{Expression={ [int]($_.Length/1KB) }; Name="KB" }
    Dir | Select-Object name, $column
    Name                                                                                                       KB
    ----                                                                                                       --
    output.htm                                                                                                 11
    output.txt                                                                                                 13
    backup.pfx                                                                                                  2
    cmdlet.txt                                                                                                 23

### Optimizing Column Width

Because the pipeline processes results in real time, PowerShell cannot know how wide of a space the column elements will occupy. As a result, it will tend to be generous in sizing columns. If you specify the _-AutoSize_ parameter, _Format-Table_ will collect all results first before setting the maximum width for all elements. You can optimize output, but the results will no longer be output in real time:

    $column = @{Expression={ [int]($_.Length/1KB) }; Label="KB" }
    Dir | Format-Table name, $column -AutoSize
    Name        KB
    ----        --
    output.htm  11
    output.txt  13
    backup.pfx   2
    cmdlet.txt  23

## Sorting and Grouping Pipeline Results

Using the cmdlets _Sort-Object_ und _Group-Object_, you can sort and group other command results. In the simplest scenario, you can just append _Sort-Object_ to a pipeline command and your output will already be sorted. It's really very simple:

    Dir $env:windir | Sort-Object

When you do that, _Sort-Object_ will select the property it uses for sorting. It's better to choose the sorting criterion yourself as every object's property may be used as a sorting criterion. For example, you could use one to create a descending list of a sub-directory's largest files:

    Dir $env:windir | Sort-Object -property Length -descending

You must know which properties are available to use _Sort-Object_ and all the other following cmdlets. In the last section, you learned how to do that. Send the result of a cmdlet to _Select-Object *_, and you'll get a list of all properties available that you can use for sorting:

    Dir $env:windir | Select-Object *

_Sort-Object_ can sort by more than one property at the same time. For example, if you'd like to sort all the files in a folder by type first (_Extension_ property) and then by name (_Name_ property), you can specify both properties:

    Dir | Sort-Object Extension, Name

### Sort Object and Hash Tables

_Sort-Object_ can use hash tables to better control the sorting. Let's assume that you want to sort a folder by file size and name. While the file size should be sorted in descending order, file names should be sorted in ascending order. You can solve this problem by passing Sort-Object to a hash table (see [Chapter 4][1]).

    Dir | Sort-Object @{expression="Length";Descending=$true},@{expression="Name";  
    Ascending=$true}

The hash table will allow you to append additional information to a property so you can separately specify for each property your preferred sorting sequence.

### Grouping Information

_Group-Object_ works by grouping objects based on one or more properties and then counting the groups. You will only need to specify the property you want to use as your grouping option. The next line will return a status overview of services:

    Get-Service | Group-Object Status
    Count Name       Group
    ----- ----       -----
       91 Running    {AeLookupSvc, AgereModemAudio, Appinfo, Ati External Event Utility...}
       67 Stopped    {ALG, AppMgmt, Automatic LiveUpdate - Scheduler, BthServ...}

The number of groups will depend only on how many different values are found in the property specified in the grouping operation. The results' object contains the properties _Count_, _Name_, and _Group_. Services are grouped according to the desired criteria in the _Group_ property. The following will show you how to obtain a list of all currently running services:

    $result = Get-Service | Group-Object Status
    $result[0].Group

In a file system, Group-Object could group files based on type:

    Dir $env:windir | Group-Object Extension
    Dir $env:windir | Group-Object Extension | Sort-Object Count -descending
    Count Name                      Group
    ----- ----                      -----
       22                           {Application data, Backup, Contacts, Debug...}
       16 .ps1                      {filter.ps1, findview.PS1, findview2.PS1, findview3.PS1...}
       12 .txt                      {output.txt, cmdlet.txt, ergebnis.txt, error.txt...}
        4 .csv                      {ergebnis.csv, history.csv, test.csv, test1.csv}
        3 .bat                      {ping.bat, safetycopy.bat, test.bat}
        2 .xml                      {export.xml, now.xml}
        2 .htm                      {output.htm, report.htm}

### Using Grouping Expressions

The criteria used to group objects can also be calculated. The next example uses a script block which returns _True_ if the file size exceeds 100 KB or False otherwise. All files larger than 100KB are in the True group:.

    Dir | Group-Object {$_.Length -gt 100KB}

    Count Name                      Group
    ----- ----                      -----
       67 False                     {Application data, Backup, Contacts, Debug...}
        2 True                      {export.xml, now.xml} in the column Count is reported which

The script block is not limited to returning _True_ or _False_. The next example will use a script block that returns a file name's first letter. The result: _Group-Object_ will group the sub-directory contents by first letters:

    Dir | Group-Object {$_.name.SubString(0,1).toUpper()}
    Count Name                      Group
    ----- ----                      -----
        4 A                         {Application data, alias1, output.htm, output.txt}
        2 B                         {Backup, backup.pfx}
        2 C                         {Contacts, cmdlet.txt}
        5 D                         {Debug, Desktop, Documents, Downloads...}
        5 F                         {Favorites, filter.ps1, findview.PS1, findview2.PS1...}
        3 L                         {Links, layout.lxy, liste.txt}
        3 M                         {MSI, Music, meinskript.ps1}
        3 P                         {Pictures, p1.nrproj, ping.bat}
        7 S                         {Saved Games, Searches, Sources, SyntaxEditor...}
       15 T                         {Test, test.bat, test.csv, test.ps1...}
        2 V                         {Videos, views.PS1}
        1 [                         {[test]}
        1 1                         {1}
        4 E                         {result.csv, result.txt, error.txt, export.xml}
        4 H                         {mainscript.ps1, help.txt, help2.txt, history.csv}
        1 I                         {info.txt}
        2 N                         {netto.ps1, now.xml}
        3 R                         {countfunctions.ps1, report.htm, root.cer}
        2 U                         {unsigned.ps1, .ps1}

This way, you can even create listings that are divided into sections:

    Dir | Group-Object {$_.name.SubString(0,1).toUpper()} | ForEach-Object { ($_.Name)*7;  
     "======="; $_.Group}
    (...)
    BBBBBBB
    =======
    d----        26.07.2007     11:03            Backup
    -a---        17.09.2007     16:05       1732 backup.pfx
    CCCCCCC
    =======
    d-r--        13.04.2007     15:05            Contacts
    -a---        13.08.2007     13:41      23586 cmdlet.txt
    DDDDDDD
    =======
    d----        28.06.2007     18:33            Debug
    d-r--        30.08.2007     15:56            Desktop
    d-r--        17.09.2007     13:29            Documents
    d-r--        24.09.2007     11:22            Downloads
    -a---        26.04.2007     11:43       1046 drive.vbs
    (...)

You can use the parameter -NoElement if you don't need the grouped objects and only want to know which groups exist. This will save a lot of memory:

    Get-Process | Group-Object -property Company -noelement
    Count Name
    ----- ----
       50
        1 AuthenTec, Inc.
        2 LG Electronics Inc.
        1 Symantec Corporation
        2 ATI Technologies Inc.
       30 Microsoft Corporation
        1 Adobe Systems, Inc.
        1 BIT LEADER
        1 LG Electronics
        1 Intel Corporation
        2 Apple Inc.
        1 BlazeVideo Company
        1 ShellTools LLC
        2 Infineon Technologies AG
        1 Just Great Software
        1 Realtek Semiconductor
        1 Synaptics, Inc.

## Filtering Pipeline Results

If you're only interested in certain objects, you can use _Where-Object_ to filter results. For example, you can filter them based on their Status property if you want to list only running services.

    Get-Service | Where-Object { $_.Status -eq "Running" }
    Status   Name               DisplayName
    ------   ----               -----------
    Running  AeLookupSvc        Applicationlookup
    Running  AgereModemAudio    Agere Modem Call Progress Audio
    Running  Appinfo            Applicationinformation
    Running  AppMgmt            Applicationmanagement
    Running  Ati External Ev... Ati External Event Utility
    Running  AudioEndpointBu... Windows-Audio-Endpoint-building
    Running  Audiosrv           Windows-Audio
    Running  BFE                Basis filter Engine
    Running  BITS               Intelligent Background Transmiss...
    (...)

_Where-Object_ takes a script block and evaluates it for every pipeline object. The current object that is travelling the pipeline is found in $_. So _Where-Object_ really works like a condition (see [Chapter 7][2]): if the expression results in _$true_, the object will be let through.

Here is another example of what a pipeline filter could look like:

    Get-WmiObject Win32_Service | Where-Object {($_.Started -eq $false) -and ($_.StartMode -eq   
    "Auto")} | Format-Table
               ExitCode Name                          ProcessId StartMode           State               Status
               -------- ----                          --------- ---------           -----               ------
                      0 Automatic Li...                       0 Auto                Stopped             OK
                      0 ehstart                               0 Auto                Stopped             OK
                      0 LiveUpdate Notic...                   0 Auto                Stopped             OK
                      0 WinDefend                             0 Auto                Stopped             OK

### Limiting Number of Objects

_Select-Object_ has a dual purpose. It can select the columns (properties) you want to see, and it can show only the first or the last results.

    # List the five largest files in a directory:
    Dir | Sort-Object Length -descending | Select-Object -first 5

    # List the five longest-running processes:
    Get-Process | Sort-Object StartTime | Select-Object -last 5 |  
     Select-Object ProcessName, StartTime

If you aren't logged on with administrator privileges, you may not retrieve the information from some processes. However, you can avoid exceptions by adding _-ErrorAction SilentlyContinue_ (shortcut: -ea 0):

Get-Process | Sort-Object StartTime -ea 0 | Select-Object -last 5 |  
Select-Object ProcessName, StartTime

## Analyzing and Comparing Results

Using the cmdlets _Measure-Object_ and _Compare-Object_, you can measure and evaluate PowerShell command results. For example, _Measure-Object_ will allow you to determine how often particular object properties are distributed. _Compare-Object_ will enable you to compare before-and-after snapshots.

### Statistical Calculations

Using the _Measure-Object_ cmdlet, you can get statistic information. For example, if you want to check file sizes, let _Dir_ give you a directory listing and then examine the _Length_ property:

    Dir $env:windir | Measure-Object Length -average -maximum -minimum -sum
    Count    : 50
    Average  : 36771,76
    Sum      : 1838588
    Maximum  : 794050
    Minimum  : 0
    Property : Length

_Measure-Object_ also accepts text files and discovers the frequency of characters, words, and lines in them:

    Get-Content $env:windirwindowsupdate.log | Measure-Object -character -line -word

## Exporting Pipeline Results

If you'd like to save results into a text file or print it, rather than outputting it into the console, append Format-* | Out-* to any PowerShell command:

    Get-Process | Format-Table -AutoSize | Out-File $env:tempsomefile.txt -Width 1000

Out-File will support the parameter -Encoding, which you can use to set the output format If you don't remember which encoding formats are allowed. Just specify an invalid value and then the error message will tell you which values are allowed:

    Dir | Out-File -encoding Dunno
    Out-File : Cannot validate argument "Dunno" because it does not belong to the set   
    "unicode, utf7, utf8, utf32, ascii, bigendianunicode, default, oem".

`Out-*` cmdlets turn results into plain text so you are reducing the richness of your results (_Out-GridView_ is the only exception to the rule which displays the results in an extra window as a mini-spreadsheet).

Export it instead and use one of the _xport-*_ cmdlets to preserve the richness of your results. For example, to open results in Microsoft Excel, do this:

    Get-Process | Export-CSV -UseCulture -NoTypeInformation -Encoding UTF8   
    $env:temp:report.csv
    Invoke-Item $env:tempreport.csv

### Suppressing Results

You can send the output to _Out-Null_ if you want to suppress command output:

    # This command not only creates a new directory but also returns the new directory:
    md testdirectory
        Directory: Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltner
    Mode                LastWriteTime     Length Name
    ----                -------------     ------ ----
    d----        19.09.2007     14:31            testdirectory
    rm testdirectory

    # Here the command output is sent to "nothing":
    md testdirectory | Out-Null
    rm testdirectory

    # That matches the following redirection:
    md testdirectory > $null
    rm testdirectory

### HTML Outputs

If you'd like, PowerShell can also pack its results into (rudimentary) HTML files. Converting objects into HTML formats is done by _ConvertTo-Html_:

    Get-Process | ConvertTo-Html | Out-File output.hta
    .output.hta
    Get-Process | Select-Object Name, Description | ConvertTo-Html -title "Process Report" |   
    Out-File output.hta
    .output.hta

* * *

Posted [Mar 12 2012, 09:00 PM][3] by [ps1][4]

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/07/chapter-4-arrays-and-hashtables.aspx
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-7-conditions.aspx
