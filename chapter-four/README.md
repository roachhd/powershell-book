
# Chapter 4. Arrays and Hashtables 

Whenever a command returns more than one result, PowerShell will automatically wrap the results into an array. So dealing with arrays is important in PowerShell. In this chapter, you will learn how arrays work. We will cover simple arrays and also so-called "associative arrays," which are also called "hash tables."

**Topics Covered:**

* [PowerShell Commands Return Arrays](#powershell-commands-return-arrays) 
* [Discovering Arrays](#discovering-arrays)
* [Processing Array Elements in a Pipeline](#processing-array-elements-in-a-pipeline)
* [Working with Real Objects](working-with-real-objects)
* [Creating New Arrays](#creating-new-arrays)
* [Polymorphic Arrays](#polymorphic-arrays)
* [Arrays With Only One, Or No, Element](#arrays-with-only-one-or-no-element)
* [Addressing Array Elements](#addressing-array-elements)
* [Choosing Several Elements from an Array](#choosing-several-elements-from-an-array)
* [Adding Elements to an Array and Removing Them](#adding-elements-to-an-array-and-removing-them)
* [Using Hash Tables](#using-hash-tables)
* [Creating a New Hash Table](#creating-a-new-hash-table)
* [Creating Objects From Hash Tables](#creating-objects-from-hash-tables)
* [Using Hash tables To Calculate Properties](#using-hash-tables-to-calculate-properties)
* [Storing Arrays in Hash Tables](#storing-arrays-in-hash-tables)
* [Inserting New Keys in an Existing Hash Table](#inserting-new-keys-in-an-existing-hash-table)  
* [Modifying and Removing Values](#modifying-and-removing-values)
* [Copying Arrays and Hash Tables](#copying-arrays-and-hash-tables)
* [Strongly Typed Arrays](#strongly-typed-arrays)
* [Summary](#summary)


## PowerShell Commands Return Arrays

If you store the result of a command in a variable and then output it, you might at first think that the variable contains plain text:

```powershell
$a = ipconfig  
$a

Windows IP Configuration  
Ethernet adapter LAN Connection

Media state

. . . . . . . . . . . : Medium disconnected

Connection-specific DNS Suffix:  
Connection location IPv6 Address . : fe80::6093:8889:257e:8d1%8  
IPv4 address . . . . . . . . . . . : 192.168.1.35  
Subnet Mask . . . . . . . . . . . . : 255.255.255.0  
Standard Gateway . . . . . . . . . . : 192.168.1.1
```

In reality, the result consists of a number of pieces of data, and PowerShell returns them as an array. This occurs automatically whenever a command returns more than a single piece of data.

### Discovering Arrays

You can check the data type to find out whether a command will return an array:

```powershell
$a = "Hello"  
$a -is [Array]

False

$a = ipconfig  
$a -is [Array]

True

An array will always supports the property _Count_, which will return the number of elements stored in that array:

$a.Count

53
```

Here, the `ipconfig` command returned 53 single results that were all stored in `$a`. If you'd like to examine a single array element, you can specify its index number. If an array has 53 elements, then its valid index numbers are 0 to 52 (the index always starts at 0).

```powershell
$a[1]
```

Windows IP Configuration

It is important to understand just when PowerShell will use arrays. If a command returns just one result, it will happily return that exact result to you. Only when a command returns more than one result will it wrap them in an array.

```powershell
$result = Dir  
$result -is [array]

True

$result = Dir C:autoexec.bat  
$result -is [array]

False
```

Of course, this will make writing scripts difficult because sometimes you cannot predict whether a command will return one, none, or many results. That's why you can make PowerShell return any result as an array.

Use `@()` if you'd like to force a command to always return its result in an array. This way you find out the number of files in a folder:

```powershell 
$result = @(Dir $env:windir -ea 0)  
$result.Count
```

Or in a line:

```powershell
$result = @(Dir $env:windir -ea 0).Count
```

### Processing Array Elements in a Pipeline

`Ipconfig` will return each line of text as an array element. This is great since all the text lines are individual array elements, allowing you to process them individually in a pipeline. For example, you can filter out unwanted text lines:

```powershell
$result = ipconfig  
$result | Where-Object { $_ -like "*Address*"  

Connection location IPv6 Address . . . : fe80::6093:8889:257e:8d1%8  
IPv4 address . . . . . . . . . . . : 192.168.1.35  
Connection location IPv6 Address . : fe80::5efe:192.168.1.35%16

Connection location IPv6 Address . . . : fe80::14ab:a532:a7b9:cd3a%11
```

As such, the result of `ipconfig` was passed to _Where-Object_, which filtered out all text lines that did not contain the keyword you were seeking. With minimal effort, you can now reduce the results of _ipconfig_ to the information you deem relevant.

### Working with Real Objects

`Ipconfig` is a legacy command, not a PowerShell cmdlet. While it is a command that will return individual information stored in arrays, this individual information will consist of plain text. Real PowerShell cmdlets will return rich objects, not text, even though the results will appear as plain text:

```powershell
Dir  

Directory: Microsoft.PowerShell.CoreFileSystem::C:Users  
Katie Ball
Mode LastWriteTime Length Name  
\---- ------------- ------ ----  
d---- 10/01/2007 16:09 Application Data  
d---- 07/26/2007 11:03 Backup  
d-r-- 04/13/2007 15:05 Contacts  
d---- 06/28/2007 18:33 Debug  
d-r-- 10/04/2007 14:21 Desktop  
d-r-- 10/04/2007 21:23 Documents  
d-r-- 10/09/2007 12:21 Downloads  
(...)
```

Let's check if the return value is an array:

```powershell
$result = Dir  
$result.Count

82
```

Every element in an array will represent a file or a directory. So if you output an element from the array to the console, PowerShell will automatically convert the object into text:

```powershell 
$result[4]  

Directory: Microsoft.PowerShell.CoreFileSystem::C:Users  
Katie Ball
Mode LastWriteTime Length Name  
\---- ------------- ------ ----  
d-r-- 04.10.2007 14:21 Desktop
```

In reality, each element returned by _Dir_ (_Get-Childitem_) is really an object with a number of individual properties. Some of these properties surfaced in the previous example as column headers (like Mode, LastWriteTime, Length, and Name). The majority of properties did not show up, though. To see all object properties, you can pipe them on to _Select-Object_ and specify an "*" to show all properties. PowerShell will now output them as list rather than table since the console is too narrow to show them all

```powershell
$result[4] | Format-List *

PSPath : Microsoft.PowerShell.CoreFileSystem::  
C:UsersTobias WeltnerDesktop  
PSParentPath : Microsoft.PowerShell.CoreFileSystem::  
C:UsersTobias Weltner  
PSChildName : Desktop  
PSDrive : C  
PSProvider : Microsoft.PowerShell.CoreFileSystem  
PSIsContainer : True  
Mode : d-r--  
Name : Desktop  
Parent : Katie Ball
Exists : True  
Root : C:  
FullName : C:UsersTobias WeltnerDesktop  
Extension :  
CreationTime : 04/13/2007 01:54:53  
CreationTimeUtc : 04/12/2007 23:54:53  
LastAccessTime : 10/04/2007 14:21:20  
LastAccessTimeUtc : 10/04/2007 12:21:20  
LastWriteTime : 10/04/2007 14:21:20  
LastWriteTimeUtc : 10/04/2007 12:21:20  
Attributes : ReadOnly, Directory
```

You'll learn more about these types of objects in [Chapter 5][README.md].

## Creating New Arrays

You can easily create your own arrays. Simply use a comma to place elements into an array:

```powershell
$array = 1,2,3,4  
$array

1  
2  
3  
4
```

There's even a shortcut for number ranges:

```powershell 
$array = 1..4  
$array

1  
2  
3  
4
```

### Polymorphic Arrays

Just like variables, individual elements of an array can store any type of value you assign. This way, you can store whatever you want in an array, even a mixture of different data types. Again, you can separate the elements by using commas:

```powershell 
$array = "Hello", "World", 1, 2, (Get-Date)  
$array

Hello  
World  
1  
2  
Tuesday, August 21, 2007 12:12:28
```

>Why is the `Get-Date` cmdlet enclosed in parentheses? Just try it without parentheses. Arrays can only store data. _Get-Date_ is a command and no data. Since you want PowerShell to evaluate the command first and then put its result into the array, you will need to use parentheses. Parentheses will identify a sub-expression and tell PowerShell to evaluate and process it first.

### Arrays With Only One Or No Element

How do you create arrays with just one single element? Here's how:

```powershell
$array = ,1  
$array.Length

1
```

You'll need to use the construct _@(...)_to create an array without any elements at all:

```powershell
$array = @()  
$array.Length

0

$array = @(12)  
$array

12

$array = @(1,2,3,"Hello")  
$array

1  
2  
3  
Hello
```

Why would you want to create an empty array in the first place? Because you can add elements to it like this when you start with an empty array:

```powershell
$array = @()  
$array += 1  
$array += 3  

1  
3  
```

## Addressing Array Elements

Every element in an array is addressed using its index number. You will find that negative index numbers count from last to first. You can also use expressions that calculate the index value:

```powershell 
-5

  
$array[-1]

12

$array[$array.Count-1]

12

$array[$array.length-1]

12

  
(-5..12)[2]

-3
```

Remember, the first element in your array will always have the index number 0. The index `-1` will always give you the `last` element in an array. The example demonstrates that the total number of all elements will be returned in two properties: `Count` and `Length`. Both of these properties will behave identically.

Here is a real-world example using arrays and accessing individual elements. First, assume you have a path and want to access only the file name. Every string object has a built-in method called `Split()` that can split the text into chunks. All you will need to do is submit the split character that is used to separate the chunks:

```powershell
PS > $path = "c:foldersubfolderfile.txt"

PS > $array = $path.Split('')

PS > $array

c:   
folder   
subfolder   
file.txt
```

As you see, by splitting a path at the backslash, you will get its components. The file name is always the last element of that array. So to access the filename, you will access the last array element:

```powershell
PS > $array[-1]

file.txt
```

Likewise, if you are interested in the file name extension, you can change the split character and use `.` instead:

```powershell 
PS > $path.Split('.')[-1]

txt
```

### Choosing Several Elements from an Array

You can also access more than one array element at once by specifying multiple index numbers. The result is a new array that contains the subset that you picked from the original array:

```powershell
$list = dir $home

  
$list[1,4,7,12]

Directory: Microsoft.PowerShell.CoreFileSystem::C:Users  
Katie Ball
Mode LastWriteTime Length Name  
\---- ------------- ------ ----  
d---- 07/26/2007 11:03 Backup  
d-r-- 08/20/2007 07:52 Desktop  
d-r-- 08/12/2007 10:21 Favorites  
d-r-- 04/13/2007 01:55 Saved Games
```

The second line will select the second, fifth, eighth, and thirteenth elements (remember that the index begins at 0). You can use this approach to reverse the contents of an array:

```powershell
$array = 1..10

  
$array = $array[($array.length-1)..0]  
$array

10  
9  
...  
1
```

>Reversing the contents of an array using the approach described above is not particularly efficient because PowerShell has to store the result in a new array. Instead, you can use the special array functions of the .NET Framework. *see [Chapter 6](README.md). This will enable you to reverse the contents of an array very efficiently:

```powershell
$a = ipconfig  
$a

  
[array]::Reverse($a)  
$a
```

### Adding Elements to an Array and Removing Them

Arrays will always contain a fixed number of elements. You'll have to make a new copy of the array with a new size to add or remove elements later. You can simply use the `+=` operator to do that and then add new elements to an existing array:

```powershell
$array += "New Value"  
$array

1  
2  
3  
New Value
```

You will find that array sizes can't be modified so PowerShell will work behind the scenes to create a brand-new, larger array, copying the contents of the old array into it, and adding the new element. PowerShell will work exactly the same way when you want to delete elements from an array. Here, too, the original array is copied to a new, smaller array while disposing of the old array. For example, the next line will remove elements 4 and 5 using the indexes 3 and 4:

```powershell
$array = $array[0..2] + $array[5..10]  
$array.Count

9
```

As you can imagine, creating new arrays to add or remove array elements is a slow and expensive approach and is only useful for occasional array manipulations. A much more efficient way is to convert an array to an ArrayList object, which is a specialized array. You can use it as a replacement for regular arrays and benefit from the added functionality, which makes it easy to add, remove, insert or even sort array contents:

```powershell
PS > $array = 1..10  
PS > $superarray = [System.Collections.ArrayList]$array  
PS > $superarray.Add(11) | Out-Null  
PS > $superarray.RemoveAt(3)  
PS > $superarray.Insert(2,100)  
PS > $superarray

1  
2  
100  
3  
5  
6  
7  
8  
9  
10  
11

PS > $superarray.Sort()  
PS > $superarray

1  
2  
3  
5  
6  
7  
8  
9  
10  
11  
100

PS > $superarray.Reverse()  
PS > $superarray

100  
11  
10  
9  
8  
7  
6  
5  
3  
2  
1
```

## Using Hash Tables

Hash tables store `key-value pairs`. So, in hash tables you do not use a numeric index to address individual elements, but rather the key you assigned to a value.

### Creating a New Hash Table

When creating a new hash table, you can use `@{}` instead of `@()`, and specify the key-value pair that is to be stored in your new hash table. You can use semi-colons to separate key-value pairs:

```powershell
$list = @{Name = "PC01"; IP="10.10.10.10"; User="Katie Ball"}

Name Value  
\---- -----  
Name PC01  
IP 10.10.10.10  
User Katie Ball
  
$list["IP"]

10.10.10.10

  
$list["Name", "IP"]

PC01  
10.10.10.10

  
$list.IP

10.10.10.10

  
$key = "IP"  
$list.$key

10.10.10.10

  
$list.keys

Name  
IP  
User

  
$list[$list.keys]

PC01  
10.10.10.10  
Katie Ball
```

The example shows that you how to retrieve the values in the hash table using the assigned key. There are two forms of notation you can use to do this:

* **Square brackets:** _Either_ you use square brackets, like in arrays;
* **Dot notation:** _Or_ you use dot notation, like with objects, and specify respectively the key name with the value you want to return. The key name can be specified as a variable.

The square brackets can return several values at the same time exactly like arrays if you specify several keys and separate them by a comma. Note that the key names in square brackets must be enclosed in quotation marks (you don't have to do this if you use dot notation).

### Creating Objects From Hash Tables

One area where hash tables are used is when you want to return text results into real objects. First, create a hash table and then convert this into an object. Let's say you want to combine information you retrieved from different sources into one consolidated result object. Here is how you can do this:

```powershell
    PS> $info = @{}
    PS> $info.BIOSVersion = Get-WmiObject Win32_BIOS | Select-Object -ExpandProperty Version
    PS> $info.OperatingSystemVersion = Get-WmiObject win32_OperatingSystem | Select
    -Object -ExpandProperty Version
    PS> $info.PowerShellVersion = $PSVersionTable.psversion.ToString()
    PS> New-Object PSObject -property $info

    OperatingSystemVersion     BIOSVersion                PowerShellVersion
    ----------------------     -----------                -----------------
    6.1.7600                   SECCSD - 6040000           2.0
```

### Using Hash Tables to Calculate Properties

Another scenario where hash tables are used is to calculate properties that do not exist. For example, if you'd like to display file size in Megabytes instead of bytes, you can create a hash table with the keys "Name" and "Expression." "Name" will hold the name of the calculated property, and "Expression" will define a script block used to calculate the property:

    $MBSize = @{Name='Size (MB)'; Expression={ if ($_.Length -ne $null) {'{0:0.0} MB'  
     -f ($_.Length / 1MB) } else { 'n/a'} }}

You can now use your hash table to add the calculated property to objects:

    Dir $env:windir | Select-Object Name, LastWriteTime, $MBSize

Note: Because of a PowerShell bug, this will only work when you create the hash table with initial values like in the example above. It will not work when you first create an empty hash table and then add the key-value pairs in a second step.

Hash tables can control even more aspects when using them in conjunction with the family of _Format-*_ cmdlets. For example, if you use `Format-Table`, you can then pass it a hash table with formatting details:

* **Expression:** The name of object property to be displayed in this column
* **Width:** Character width of the column
* **Label:** Column heading
* **Alignment:** Right or left justification of the column

You can just define a hash table with the formatting information and pass it on to Format-Table:

    # Setting formatting specifications for each column in a hash table:
    $column1 = @{expression="Name"; width=30; label="filename"; alignment="left"}
    $column2 = @{expression="LastWriteTime"; width=40; label="last modification";   
    alignment="right"}

    # Output contents of a hash table:
    $column1
    Name                           Value
    ----                           -----
    alignment                      left
    label                          File name
    width                          30
    expression                     Name

    # Output Dir command result with format table and selected formatting:
    Dir | Format-Table $column1, $column2
    File Name                                           last modification
    ---------                                               ---------------
    Application data                                    10/1/2007  16:09:57
    Backup                                              07/26/2007 11:03:07
    Contacts                                            04/13/2007 15:05:30
    Debug                                               06/28/2007 18:33:29
    Desktop                                             10/4/2007  14:21:20
    Documents                                           10/4/2007  21:23:10
    (...)

You'll learn more about format cmdlets like _Format-Table_ in the [Chapter 5][1]

### Storing Arrays in Hash Tables

You can store classic array inside of hash tables, too. This is possible because hash tables use the semi-colon as key-value pair separators, which leaves the comma available to create classic arrays:

    # Create hash table with arrays as value:
    $test = @{ value1 = 12; value2 = 1,2,3 }

    # Return values (value 2 is an array with three elements):
    $test.value1
    12
    $test.value2
    1
    2
    3

### Inserting New Keys in an Existing Hash Table

If you'd like to insert new key-value pairs in an existing hash table, you can just specify the new key and the value that is to be assigned to the new key. Again, you can choose between the square brackets and dot notations.

    # Create a new hash table with key-value pairs
    $list = @{Name = "PC01"; IP="10.10.10.10"; User="Katie Ball"}

    # Insert two new key-value pairs in the list (two different notations are possible):
    $list.Date = Get-Date
    $list["Location"] = "Hanover"

    # Check result:
    $list
    Name                           Value
    ----                           -----
    Name                           PC01
    Location                       Hanover
    Date                           08/21/2007 13:00:18
    IP                             10.10.10.10
    User                           Katie Ball

You can create empty hash tables and then insert keys as needed because it's easy to insert new keys in an existing hash table:

    # Create empty hash table
    $list = @{}

    # Subsequently insert key-value pairs when required
    $list.Name = "PC01"
    $list.Location = "Hanover"
    (...)

### Modifying and Removing Values

If all you want to do is to change the value of an existing key in your hash table, just overwrite the value:

```powershell
$list["Date"] = (Get-Date).AddDays(-1)  
$list.Location = "New York"

Name Value  
\---- -----  
Name PC01  
Location New York  
Date 08/20/2007 13:10:12  
IP 10.10.10.10  
User Katie Ball
```

If you'd like to completely remove a key from the hash table, use _Remove()_ and as an argument specify the key that you want to remove:

```powershell
$list.remove("Date")
```

### Using Hash Tables for Output Formatting

An interesting use for hash tables is to format text. Normally, PowerShell outputs the result of most commands as a table and internally uses the cmdlet `Format-Table`:

```powershell
Dir  
Dir | Format-Table
```

If you use `Format-Table`, you can pass it a hash table with formatting specifications. This enables you to control how the result of the command is formatted.

Every column is defined with its own hash table. In the hash table, values are assigned to the following four keys:

* **Expression:** The name of object property to be displayed in this column
* **Width:** Character width of the column
* **Label:** Column heading
* **Alignment:** Right or left justification of the column

All you need to do is to pass your format definitions to `Format-Table` to ensure that your listing shows just the name and date of the last modification in two columns:

```powershell
$column1 = @{expression="Name"; width=30; `  
label="filename"; alignment="left"}  
$column2 = @{expression="LastWriteTime"; width=40; `  
label="last modification"; alignment="right"}

  
$column1

Name Value  
\---- -----  
alignment left  
label File name  
width 30  
expression Name

  
Dir | Format-Table $column1, $column2

File Name Last Modification  
\--------- ---------------  
Application Data 10/1/2007 16:09:57  
Backup 07/26/2007 11:03:07  
Contacts 04/13/2007 15:05:30  
Debug 06/28/2007 18:33:29  
Desktop 10/4/2007 14:21:20  
Documents 10/4/2007 21:23:10  
(...)
```

You'll learn more about format cmdlets like `Format-Table` in the [Chapter 5][1].

## Copying Arrays and Hash Tables

Copying arrays or hash tables from one variable to another works, but may produce unexpected results. The reason is that arrays and hash tables are not stored directly in variables, which always store only a single value. When you work with arrays and hash tables, you are dealing with a _reference_ to the array or hash table. So, if you copy the contents of a variable to another, only the reference will be copied, not the array or the hash table. That could result in the following unexpected behavior:

```powershell
$array1 = 1,2,3  
$array2 = $array1  
$array2[0] = 99  
$array1[0]

99
```

Although the contents of _$array2_ were changed in this example, this affects _$array1_ as well, because they are both identical. The variables _$array1_ and _$array2_ internally reference the same storage area. Therefore, you have to create a copy if you want to copy arrays or hash tables,:

```powershell
$array1 = 1,2,3  
$array2 = $array1.Clone()  
$array2[0] = 99  
$array1[0]

1
```

Whenever you add new elements to an array (or a hash table) or remove existing ones, a copy action takes place automatically in the background and its results are stored in a new array or hash table. The following example clearly shows the consequences:

```powershell
$array1 = 1,2,3  
$array2 = $array1

  
$array2 += 4  
$array2[0]=99

  
$array1[0]

1
```

## Strongly Typed Arrays

Arrays are typically polymorphic: you can store any type of value you want in any element. PowerShell automatically selects the appropriate type for each element. If you want to limit the type of data that can be stored in an array, use "strong typing" and predefine a particular type. You should specify the desired variable type in square brackets. You also specify an open and closed square bracket behind the variable type because this is an array and not a normal variable:

```powershell
[int[]]$array = 1,2,3

  
$array += 4  
$array += 12.56  
$array += "123"

  
$array += "Hello"
```

The value `Hello` cannot be converted into the type `System.Int32`.

```powershell 
Error: "Input string was not in a correct format."  
At line:1 char:6  
\+ $array <<<< += "Hello"
```

In the example, `$array` was defined as an array of the `Integer` type. Now, the array is able to store only whole numbers. If you try to store values in it that cannot be turned into whole numbers, an error will be reported.

## Summary

Arrays and hash tables can store as many separate elements as you like. Arrays assign a sequential index number to elements that always begin at 0. Hash tables in contrast use a key name. That's why every element in hash tables consists of a key-value pair.

You create new arrays with `@(Element1, Element2, ...)`. You can also leave out `@()` for arrays and only use the comma operator. You create new hash tables with `@{key1=value1;key2=value2; ...)`. `@{}` must always be specified for hash tables. Semi-colons by themselves are not sufficient to create a new hash table.

You can address single elements of an array or hash able by using square brackets. Specify either the index number (for arrays) or the key (for hash tables) of the desired element in the square brackets. Using this approach you can select and retrieve several elements at the same time.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/05/chapter-5-the-powershell-pipeline.aspx
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/05/chapter-6-working-with-objects.aspx
