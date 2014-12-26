
# Chapter 7. Conditions 

Conditions are what you need to make scripts clever. Conditions can evaluate a situation and then take appropriate action. There are a number of condition constructs in the PowerShell language which that we will look at in this chapter.

In the second part, you'll employ conditions to execute PowerShell instructions only if a particular condition is actually met.

**Topics Covered:**

## Creating Conditions

A condition is really just a question that can be answered with yes (true) or no (false). The following PowerShell comparison operators allow you to compare values,

| ----- |
| Operator |  Conventional |  Description |  Example |  Result |
| -eq, -ceq, -ieq |  = |  equals |  10 -eq 15 |  $false |
| -ne, -cne, -ine |  <> |  not equal |  10 -ne 15 |  $true |
| -gt, -cgt, -igt |  > |  greater than |  10 -gt 15 |  $false |
| -ge, -cge, -ige |  >= |  greater than or equal to |  10 -ge 15 |  $false |
| -lt, -clt, -ilt |  < |  less than |  10 -lt 15 |  $true |
| -le, -cle, -ile |  <= |  less than or equal to |  10 -le 15 |  $true |
| -contains,  
-ccontains,  
-icontains |   |  contains |  1,2,3 -contains 1 |  $true |
| -notcontains,  
-cnotcontains,  
-inotcontains |   |  does not contain |  1,2,3 -notcontains 1 |  $false |

**Table 7.1:** Comparison operators

PowerShell doesn't use traditional comparison operators that you may know from other programming languages. In particular, the "=" operator is an assignment operator only in PowerShell, while ">" and "<" operators are used for redirection.

There are three variants of all comparison operators. The basic variant is case-insensitive so it does not distinguish between upper and lower case letters (if you compare text). To explicitly specify whether case should be taken into account, you can use variants that begin with "c" (case-sensitive) or "i" (case-insensitive).

### Carrying Out a Comparison

To get familiar with comparison operators, you can play with them in the interactive PowerShell console! First, enter a value, then a comparison operator, and then the second value that you want to compare with the first. When you hit (enter)), PowerShell executes the comparison. The result is always _True_ (condition is met) or _False_ (condition not met).

    4 -eq 10
    False
    "secret" -ieq "SECRET"
    True

As long as you compare only numbers or only strings, comparisons are straight-forward:

    123 -lt 123.5
    True

However, you can also compare different data types. However, these results are not always as straight-forward as the previous one:

    12 -eq "Hello"
    False
    12 -eq "000012"
    True
    "12" -eq 12
    True
    "12" -eq 012
    True
    "012" -eq 012
    False
    123 –lt 123.4
    True
    123 –lt "123.4"
    False
    123 –lt "123.5"
    True

Are the results surprising? When you compare different data types, PowerShell will try to convert the data types into one common data type. It will always look at the data type to the left of the comparison operator and then try and convert the value to the right to this data type.

### "Reversing" Comparisons

With the logical operator _-not_ you can reverse comparison results. It will expect an expression on the right side that is either true or _false_. Instead of -not, you can also use "!":

    $a = 10
    $a -gt 5
    True
    -not ($a -gt 5)
    False

    # Shorthand: instead of -not "!" can also be used:
    !($a -gt 5)
    False

You should make good use of parentheses if you're working with logical operators like _–not_. Logical operators are always interested in the result of a comparison, but not in the comparison itself. That's why the comparison should always be in parentheses.

### Combining Comparisons

You can combine several comparisons with logical operators because every comparison returns either _True_ or _False_. The following conditional statement would evaluate to true only if both comparisons evaluate to true:

    ( ($age -ge 18) -and ($sex -eq "m") )

You should put separate comparisons in parentheses because you only want to link the results of these comparisons and certainly not the comparisons themselves.

| ----- |
| Operator |  Description |  Left Value |  Right Value |  Result |
| -and |  Both conditions must be met |  True  
False  
False  
True |  False  
True  
False  
True |  False  
False  
False  
True |
| -or |  At least one of the two conditions must be met |  True  
False  
False  
True |  False  
True  
False  
True |  True  
True  
False  
True |
| -xor |  One or the other condition must be met, but not both |  True  
False  
False  
True |  True  
False  
True  
False |  False  
False  
True  
True |
| -not |  Reverses the result |  (not applicable) |  True  
False |  False  
True |

**Table 7.2:** Logical operators

### Comparisons with Arrays and Collections

Up to now, you've only used the comparison operators in Table 7.1 to compare single values. In [Chapter 4][1], you've already become familiar with arrays. How do comparison operators work on arrays? Which element of an array is used in the comparison? The simple answer is all elements!

In this case, comparison operators work pretty much as a filter and return a new array that only contains the elements that matched the comparison.

    1,2,3,4,3,2,1 -eq 3
    3
    3

If you'd like to see only the elements of an array that don't match the comparison value, you can use _-ne_ (_not equal_) operator:

    1,2,3,4,3,2,1 -ne 3
    1
    2
    4
    2
    1

#### Verifying Whether an Array Contains a Particular Element

But how would you find out whether an array contains a particular element? As you have seen, -eq provides matching array elements only. _-contains_ and _-notcontains_. verify whether a certain value exists in an array.

    # -eq returns only those elements matching the criterion:
    1,2,3 –eq 5

    # -contains answers the question of whether the sought element is included in the array:
    1,2,3 -contains 5
    False
    1,2,3 -notcontains 5
    True

## Where-Object

In the pipeline, the results of a command are handed over to the next one and the _Where-Object_ cmdlet will work like a filter, allowing only those objects to pass the pipeline that meet a certain condition. To make this work, you can specify your condition to _Where-Object_.

### Filtering Results in the Pipeline

The cmdlet _Get-Process_ returns all running processes. If you would like to find out currently running instances of Notepad, you will need to set up the appropriate comparison term. You will first need to know the names of all the properties found in process objects. Here is one way of listing them:

    Get-Process | Select-Object -first 1 *
    __NounName                 : process
    Name                       : agrsmsvc
    Handles                    : 36
    VM                         : 21884928
    WS                         : 57344
    PM                         : 716800
    NPM                        : 1768
    Path                       :
    Company                    :
    CPU                        :
    FileVersion                :
    ProductVersion             :
    Description                :
    Product                    :
    Id                         : 1316
    PriorityClass              :
    HandleCount                : 36
    WorkingSet                 : 57344
    PagedMemorySize            : 716800
    PrivateMemorySize          : 716800
    VirtualMemorySize          : 21884928
    TotalProcessorTime         :
    BasePriority               : 8
    ExitCode                   :
    HasExited                  :
    ExitTime                   :
    Handle                     :
    MachineName                : .
    MainWindowHandle           : 0
    MainWindowTitle            :
    MainModule                 :
    MaxWorkingSet              :
    MinWorkingSet              :
    Modules                    :
    NonpagedSystemMemorySize   : 1768
    NonpagedSystemMemorySize64 : 1768
    PagedMemorySize64          : 716800
    PagedSystemMemorySize      : 24860
    PagedSystemMemorySize64    : 24860
    PeakPagedMemorySize        : 716800
    PeakPagedMemorySize64      : 716800
    PeakWorkingSet             : 2387968
    PeakWorkingSet64           : 2387968
    PeakVirtualMemorySize      : 21884928
    PeakVirtualMemorySize64    : 21884928
    PriorityBoostEnabled       :
    PrivateMemorySize64        : 716800
    PrivilegedProcessorTime    :
    ProcessName                : agrsmsvc
    ProcessorAffinity          :
    Responding                 : True
    SessionId                  : 0
    StartInfo                  : System.Diagnostics.ProcessStartInfo
    StartTime                  :
    SynchronizingObject        :
    Threads                    : {1964, 1000}
    UserProcessorTime          :
    VirtualMemorySize64        : 21884928
    EnableRaisingEvents        : False
    StandardInput              :
    StandardOutput             :
    StandardError              :
    WorkingSet64               : 57344
    Site                       :
    Container                  :

### Putting Together a Condition

As you can see from the previous output, the name of a process can be found in the _Name_ property. If you're just looking for the processes of the Notepad, your condition is: _name -eq 'notepad_:

    Get-Process | Where-Object { $_.name -eq 'notepad' }
    Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
    -------  ------    -----      ----- -----   ------     -- -----------
         68       4     1636       8744    62     0,14   7732 notepad
         68       4     1632       8764    62     0,05   7812 notepad

Here are two things to note: if the call does not return anything at all, then there are probably no Notepad processes running. Before you make the effort and use Where-Object to filter results, you should make sure the initial cmdlet has no parameter to filter the information you want right away. For example, Get-Process already supports a parameter called -name, which will return only the processes you specify:

    Get-Process -name notepad
    Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
    -------  ------    -----      ----- -----   ------     -- -----------
         68       4     1636       8744    62     0,14   7732 notepad
         68       4     1632       8764    62     0,05   7812 notepad

The only difference with the latter approach: if no Notepad process is running, Get-Process throws an exception, telling you that there is no such process. If you don't like that, you can always add the parameter -ErrorAction SilentlyContinue, which will work for all cmdlets and hide all error messages.

When you revisit your Where-Object line, you'll see that your condition is specified in curly brackets after the cmdlet. The $_ variable contains the current pipeline object. While sometimes the initial cmdlet is able to do the filtering all by itself (like in the previous example using -name), Where-Object is much more flexible because it can filter on any piece of information found in an object.

You can use the next one-liner to retrieve all processes whose company name begins with "Micro" and output name, description, and company name:

    Get-Process | Where-Object { $_.company -like 'micro*' } | Select-Object name, description, company
    Name                               Description                       Company
    ----                               -----------                       -------
    conime                             Console IME                       Microsoft Corporation
    dwm                                Desktopwindow-Manager             Microsoft Corporation
    ehmsas                             Media Center Media Status Aggr... Microsoft Corporation
    ehtray                             Media Center Tray Applet          Microsoft Corporation
    EXCEL                              Microsoft Office Excel            Microsoft Corporation
    explorer                           Windows-Explorer                  Microsoft Corporation
    GrooveMonitor                      GrooveMonitor Utility             Microsoft Corporation
    ieuser                             Internet Explorer                 Microsoft Corporation
    iexplore                           Internet Explorer                 Microsoft Corporation
    msnmsgr                            Messenger                         Microsoft Corporation
    notepad                            Editor                            Microsoft Corporation
    notepad                            Editor                            Microsoft Corporation
    sidebar                            Windows-Sidebar                   Microsoft Corporation
    taskeng                            Task Scheduler Engine             Microsoft Corporation
    WINWORD                            Microsoft Office Word             Microsoft Corporation
    wmpnscfg                           Windows Media Player Network S... Microsoft Corporation
    wpcumi                             Windows Parental Control Notif... Microsoft Corporation

Since you will often need conditions in a pipeline, there is an alias for _Where-Object_: "?". So, instead of _Where-Object_, you can also use "?'". However, it does make your code a bit unreadable:

    # The two following instructions return the same result: all running services
    Get-Service | Foreach-Object {$_.Status -eq 'Running' }
    Get-Service | ? {$_.Status -eq 'Running' }

## If-ElseIf-Else

_Where-object_ works great in the pipeline, but it is inappropriate if you want to make longer code segments dependent on meeting a condition. Here, the _If..ElseIf..Else_ statement works much better. In the simplest case, the statement will look like this:

    If (condition) {# If the condition applies, this code will be executed}

The condition must be enclosed in parentheses and follow the keyword _If_. If the condition is met, the code in the curly brackets after it will be executed, otherwise, it will not. Try it out:

    If ($a -gt 10) { "$a is larger than 10" }

It's likely, though, that you won't (yet) see a result. The condition was not met, and so the code in the curly brackets wasn't executed. To get an answer, you can make sure that the condition is met:

    $a = 11
    if ($a -gt 10) { "$a is larger than 10" }
    11 is larger than 10

Now, the comparison is true, and the _If_ statement ensures that the code in the curly brackets will return a result. As it is, that clearly shows that the simplest _If_ statement usually doesn't suffice in itself, because you would like to _always_ get a result, even when the condition isn't met. You can expand the _If_ statement with _Else_ to accomplish that:

    if ($a -gt 10)
    {
      "$a is larger than 10"
    }
    else
    {
      "$a is less than or equal to 10"
    }

Now, the code in the curly brackets after _If_ is executed if the condition is met. However, if the preceding condition isn't true, the code in the curly brackets after _Else will be executed_. If you have several conditions, you may insert as many _ElseIf_ blocks between _If_ and _Else_ as you like:

    if ($a -gt 10)
    {
      "$a is larger than 10"
    }
    elseif ($a -eq 10)
    {
      "$a is exactly 10"
    }
    else
    {
      "$a is less than 10"
    }

The _If_ statement here will always execute the code in the curly brackets after the condition that is met. The code after _Else_ will be executed when none of the preceding conditions are true. What happens if several conditions are true? Then the code after the first applicable condition will be executed and all other applicable conditions will be ignored.

    if ($a -gt 10)
    {
      "$a is larger than 10"
    }
    elseif ($a -eq 10)
    {
      "$a is exactly 10"
    }
    elseif ($a –ge 10)
    {
      "$a is larger than or equal to 10"
    }
    else
    {
      "$a is smaller than 10"
    }

The fact is that the _If_ statement doesn't care at all about the condition that you state. All that the _If_ statement evaluates is _$true_ or _$false_. If condition evaluates _$true_, the code in the curly brackets after it will be executed, otherwise, it will not. Conditions are only a way to return one of the requested values _$true_ or _$false_. But the value could come from another function or from a variable:

    # Returns True from 14:00 on, otherwise False:
    function isAfternoon { (get-date).Hour -gt 13 }
    isAfternoon
    True

    # Result of the function determines which code the If statement executes:
    if (isAfternoon) { "Time for break!" } else { "It's still early." }
    Time for break!

This example shows that the condition after _If_ must always be in parentheses, but it can also come from any source as long as it is _$true_ or _$false_. In addition, you can also write the _If_ statement in a single line. If you'd like to execute more than one command in the curly brackets without having to use new lines, then you should separate the commands with a semi-colon ";".

## Switch

If you'd like to test a value against many comparison values, the _If_ statement can quickly become unreadable. The _Switch_ code is much cleaner:

    # Test a value against several comparison values (with If statement):
    $value = 1
    if ($value -eq 1)
    {
      " Number 1"
    }
    elseif ($value -eq 2)
    {
      " Number 2"
    }
    elseif ($value -eq 3)
    {
      " Number 3"
    }
    Number 1

    # Test a value against several comparison values (with Switch statement):
    $value = 1
    switch ($value)
    {
      1  { "Number 1" }
      2  { "Number 2" }
      3  { "Number 3" }
    }
    Number 1

This is how you can use the _Switch statement_: the value to switch on is in the parentheses after the _Switch keyword_. That value is matched with each of the conditions on a case-by-case basis. If a match is found, the action associated with that condition is then performed. You can use the default comparison operator, the _–eq_ operator, to verify equality.

### Testing Range of Values

The default comparison operator in a switch statement is _-eq_, but you can also compare a value with other comparison statements. You can create your own condition and put it in curly brackets. The condition must then result in either _true_ or _false_:

    $value = 8
    switch ($value)
    {
      # Instead of a standard value, a code block is used that results in True for numbers smaller than 5:
      {$_ -le 5}  { "Number from 1to 5" }

      # A value is used here; Switch checks whether this value matches $value:
      6  { "Number 6" }

      # Complex conditions areallowed as they are here, where –and is used to combine two comparisons:
      {(($_ -gt 6) -and ($_ -le 10))}  { "Number from 7 to 10" }
    }
    Number from 7 to 10

* The code block _{$_ -le 5}_ includes all numbers less than or equal to 5.
* The code block _{(($_ -gt 6) -and ($_ -le 10))}_ combines two conditions and results in true if the number is either larger than 6 or less than-equal to 10. Consequently, you can combine any PowerShell statements in the code block and also use the logical operators listed in Table 7.2.

Here, you can use the initial value stored in _$__ for your conditions, but because _$__ is generally available anywhere in the Switch block, you could just as well have put it to work in the result code:

    $value = 8
    switch ($value)
    {
      # The initial value (here it is in $value) is available in the variable $_:
      {$_ -le 5}  { "$_ is a number from 1 to 5" }
      6  { "Number 6" }
      {(($_ -gt 6) -and ($_ -le 10))}  { "$_ is a number from 7 to 10" }
    }
    8 is a number from 7 to 10

### No Applicable Condition

In contrast to _If_, the _Switch_ clause will execute all code for all conditions that are met. So, if there are two conditions that are both met, _Switch_ will execute them both whereas _If_ had only executed the first matching condition code. To change the Switch default behavior and make it execute only the first matching code, you should use the statement _continue_ inside of a code block.

If no condition is met, the _If_ clause will provide the _Else_ statement, which serves as a catch-all. Likewise, _Switch_ has a similar catch-all called _default_:

    $value = 50
    switch ($value)
    {
      {$_ -le 5}  { "$_is a number from 1 to 5" }
      6  { "Number 6" }
      {(($_ -gt 6) -and ($_ -le 10))}  { "$_ is a number from 7 to 10" }
      # The code after the next statement will be executed if no other condition has been met:
      default {"$_ is a number outside the range from 1 to 10" }
    }
    50 is a number outside the range from 1 to 10

### Several Applicable Conditions

If more than one condition applies, then _Switch_ will work differently from _If_. For _If_, only the first applicable condition was executed. For _Switch_, all applicable conditions are executed:

    $value = 50
    switch ($value)
    {
      50  { "the number 50" }
      {$_ -gt 10}  {"larger than 10"}
      {$_ -is [int]}  {"Integer number"}
    }
    The Number 50
    Larger than 10
    Integer number

Consequently, all applicable conditions will ensure that the following code is executed. So in some circumstances, you may get more than one result.

Try out that example, but assign 50.0 to $value. In this case, you'll get just two results instead of three. Do you know why? That's right: the third condition is no longer fulfilled because the number in _$value_ is no longer an integer number. However, the other two conditions continue to remain fulfilled.

If you'd like to receive only one result, you can add the continue or break statement to the code.

    $value = 50
    switch ($value)
    {
      50  { "the number 50"; break }
      {$_ -gt 10}  {"larger than 10"; break}
      {$_ -is [int]}  {"Integer number"; break}
    }
    The number 50

The keyword _break_ tells PowerShell to leave the Switch construct. In conditions, _break_ and _continue_ are interchangeable. In loops, they work differently. While _breaks_ exits a loop immediately, _continue_ would only exit the current iteration.

### Using String Comparisons

The previous examples have compared numbers. You could also naturally compare strings since you now know that _Switch_ uses only the normal _–eq_ comparison operator behind the scenes and that their string comparisons are also permitted.. The following code could be the basic structure of a command evaluation. As such, a different action will be performed, depending on the specified command:

    $action = "sAVe"
    switch ($action)
    {
      "save"  { "I save..." }
      "open"  { "I open..." }
      "print"  { "I print..." }
      Default  { "Unknown command" }
    }
    I save...

#### Case Sensitivity

Since the _–eq_ comparison operator doesn't distinguish between lower and upper case, case sensitivity doesn't play a role in comparisons. If you want to distinguish between them, you can use the _–case_ option. Working behind the scenes, it will replace the _–eq_ comparison operator with _–ceq_, after which case sensitivity will suddenly become crucial:

    $action = "sAVe"
    switch -case ($action)
    {
      "save"  { "I save..." }
      "open"  { "I open..." }
      "print"  { "I print..." }
      Default  { "Unknown command" }
    }
    Unknown command

#### Wildcard Characters

In fact, you can also exchange a standard comparison operator for _–like_ and _–match_ operators and then carry out wildcard comparisons. Using the _–wildcard_ option, you can activate the _-like_ operator, which is conversant, among others, with the "*" wildcard character:

    $text = "IP address: 10.10.10.10"
    switch -wildcard ($text)
    {
      "IP*"  { "The text begins with IP: $_" }
      "*.*.*.*"  { "The text contains an IP address string pattern: $_" }
      "*dress*"  { "The text contains the string 'dress' in arbitrary locations: $_" }
    }
    The text begins with IP: IP address: 10.10.10.10
    The text contains an IP address string pattern: IP address: 10.10.10.10
    The text contains the string 'dress' in arbitrary locations: IP address: 10.10.10.10

#### Regular Expressions

Simple wildcard characters ca not always be used for recognizing patterns. Regular expressions are much more efficient. But they assume much more basic knowledge, which is why you should take a peek ahead at [Chapter 13][2], discussion of regular expression in greater detail.

With the _-regex_ option, you can ensure that _Switch_ uses the _–match_ comparison operator instead of _–eq_, and thus employs regular expressions. Using regular expressions, you can identify a pattern much more precisely than by using simple wildcard characters. But that's not all!. As in the case with the _–match_ operator, you will usually get back the text that matches the pattern in the _$matches_ variable. This way, you can even parse information out of the text:

    $text = "IP address: 10.10.10.10"
    switch -regex ($text)
    {
      "^IP"  { "The text begins with IP: $($matches[0])" }
      "d{1,3}.d{1,3}.d{1,3}.d{1,3}"  { "The text contains an IP address string pattern: $($matches[0])" }
      "b.*?dress.*?b"  { " The text contains the string 'dress' in arbitrary locations: $($matches[0])" }
    }
    The text begins with IP: IP
    The text contains an IP address string pattern: 10.10.10.10
    The text contains the string 'dress' in arbitrary locations: IP address

The result of the –match comparison with the regular expression is returned in _$matches_, a hash table with each result, because regular expressions can, depending on their form, return several results. In this example, only the first result you got by using _$matches[0]_ should interest you.. The entire expression is embedded in _$(...)_ to ensure that this result appears in the output text.

### Processing Several Values Simultaneously

Until now, you have always passed just one value for evaluation to _Switch_. But _Switch_ can also process several values at the same time. To do so, you can pass to _Switch_ the values in an array or a collection. In the following example, _Switch_ is passed an array containing five elements. _Switch_ will automatically take all the elements, one at a time, from the array and compare each of them, one by one:

    $array = 1..5
    switch ($array)
    {
      {$_ % 2} { "$_ is uneven."}
      Default { "$_ is even."}
    }
    1 is uneven.
    2 is even.
    3 is uneven.
    4 is even.
    5 is uneven.

There you have it: _Switch_ will accept not only single values, but also entire arrays and collections. As such, _Switch_ would be an ideal candidate for evaluating results on the PowerShell pipeline because the pipeline character ("|") is used to forward results as arrays or collections from one command to the next.

The next line queries _Get-Process_ for all running processes and then pipes the result to a script block (_& {...}_). In the script block, _Switch_ will evaluate the result of the pipeline, which is available in _$input_. If the _WS_ property of a process is larger than one megabyte, this process is output. _Switch_ will then filter all of the processes whose WS property is less than or equal to one megabyte:

    Get-Process | & { Switch($input) { {$_.WS -gt 1MB} { $_ }}}

However, this line is extremely hard to read and seems complicated. You can formulate the condition in a much clearer way by using _Where-Object_:

    Get-Process | Where-Object { $_.WS -gt 1MB }

This variant also works more quickly because _Switch_ had to wait until the pipeline has collected the entire results of the preceding command in _$input_. In _Where-Object_, it processes the results of the preceding command precisely when the results are ready. This difference is especially striking for elaborate commands:

    # Switch returns all files beginning with "a":
    Dir | & { switch($Input) { {$_.name.StartsWith("a")} { $_ } }}

    # But it doesn't do so until Dir has retrieved all data, and that can take a long time:
    Dir -Recurse | & { switch($Input) { {$_.name.StartsWith("a")} { $_ } }}

    # Where-Object processes the incoming results immediately:
    Dir -recurse | Where-Object { $_.name.StartsWith("a") }

    # The alias of Where-Object ("?") works exactly the same way:
    Dir -recurse | ? { $_.name.StartsWith("a") }

## Summary

Intelligent decisions are based on conditions, which in their simplest form can be reduced to plain _Yes_ or _No_ answers. Using the comparison operators listed in Table 7.1, you can formulate such conditions and even combine these with the logical operators listed in Table 7.2 to form complex queries.

The simple _Yes/No_ answers of your conditions will determine whether particular PowerShell instructions can carried out or not. In their simplest form, you can use the Where-Object cmdlet in the pipeline. It functions there like a filter, allowing only those results through the pipeline that correspond to your condition.

If you would like more control, or would like to execute larger code segments independently of conditions, you can use the _If_ statement, which evaluates as many different conditions as you wish and, depending on the result, will then execute the allocated code. This is the typical "If-Then" scenario: _if_ certain conditions are met, _then_ certain code segments will be executed.

An alternative to the _If_ statement is the _Switch_ statement. Using it, you can compare a fixed initial value with various possibilities. _Switch_ is the right choice when you want to check a particular variable against many different possible values.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/07/chapter-4-arrays-and-hashtables.aspx
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-13-text-and-regular-expressions.aspx
