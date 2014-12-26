
# Chapter 8. Loops

Loops repeat PowerShell code and are the heart of automation. In this chapter, you will learn the PowerShell loop constructs.

**Topics Covered:**

## ForEach-Object

Many PowerShell cmdlets return more than one result object. You can use a Pipeline loop: _foreach-object_ to process them all one after another.. In fact, you can easily use this loop to repeat the code multiple times. The next line will launch 10 instances of the Notepad editor:

1..10 | Foreach-Object { notepad }

_Foreach-Object_ is simply a cmdlet, and the script block following it really is an argument assigned to _Foreach-Object_:

1..10 | Foreach-Object -process { notepad }

Inside of the script block, you can execute any code. You can also execute multiple lines of code. You can use a semicolon to separate statements from each other in one line:

1..10 | Foreach-Object { notepad; "Launching Notepad!" }

In PowerShell editor, you can use multiple lines:

1..10 | Foreach-Object { notepad "Launching Notepad!" }

The element processed by the script block is available in the special variable $_:

1..10 | Foreach-Object { "Executing $_. Time" }

Most of the time, you will not feed numbers into _Foreach-Object_, but instead the results of another cmdlet. Have a look:

Get-Process | Foreach-Object { 'Process {0} consumes {1} seconds CPU time' -f $_.Name, $_.CPU }

### Invoking Methods

Because _ForEach-Object_ will give you access to each object in a pipeline, you can invoke methods of these objects. In [Chapter 7][1], you learned how to take advantage of this to close all instances of the Notepad. This will give you much more control. You could use _Stop-Process_ to stop a process. But if you want to close programs gracefully, you should provide the user with the opportunity to save unsaved work by also invoking the method _CloseMainWindow()_. The next line closes all instances of Notepad windows. If there is unsaved data, a dialog appears asking the user to save it first:

Get-Process notepad | ForEach-Object { $_.CloseMainWindow() }

You can also solve more advanced problems. If you want to close only those instances of Notepad that were running for more than 10 minutes, you can take advantage of the property StartTime. All you needed to do is calculate the cut-off date using _New-Timespan_. Let's first get a listing that tells you how many minutes an instance of Notepad has been running:

    Get-Process notepad | ForEach-Object {
      $info = $_ | Select-Object Name, StartTime, CPU, Minutes
      $info.Minutes = New-Timespan $_.StartTime | Select-Object -expandproperty TotalMinutes
      $info
    }

Check out a little trick. In the above code, the script block creates a copy of the incoming object using _Select-Object_, which selects the columns you want to view. We specified an additional property called _Minutes_ to display the running minutes, which are not part of the original object. _Select-Object_ will happily add that new property to the object. Next, we can fill in the information into the _Minutes_ property. This is done using _New-Timespan_, which calculates the time difference between now and the time found in _StartTime_. Don't forget to output the _$info_ object at the end or the script block will have no result.

To kill only those instances of Notepad that were running for more than 10 minutes, you will need a condition:

    Get-Process Notepad | Foreach-Object {
      $cutoff = ( (Get-Date) - (New-Timespan -minutes 10) )
      if ($_.StartTime -lt $cutoff) { $_ }
    }

This code would only return Notepad processes running for more than 10 minutes and you could pipe the result into _Stop-Process_ to kill those.

What you see here is a _Foreach-Object_ loop with an _If_ condition. This is exactly what _Where-Object_ does so if you need loops with conditions to filter out unwanted objects, you can simplify:

    Get-Process Notepad | Where-Object {
      $cutoff = ( (Get-Date) - (New-Timespan -minutes 10) )
      $_.StartTime -lt $cutoff
    }

## Foreach

There is another looping construct called _Foreach_. Don't confuse this with the _Foreach_ alias, which represents _Foreach-Object_. So, if you see a _Foreach_ statement inside a pipeline, this really is a _Foreach-Object_ cmdlet. The true Foreach loop is never used inside the pipeline. Instead, it can only live inside a code block.

While _Foreach-Object_ obtains its entries from the pipeline, the _Foreach_ statement iterates over a collection of objects:

# ForEach-Object lists each element in a pipeline:   
Dir C: | ForEach-Object { $_.name }

# Foreach loop lists each element in a colection:   
foreach ($element in Dir C:) { $element.name }

The true _Foreach_ statement does not use the pipeline architecture. This is the most important difference because it has very practical consequences. The pipeline has a very low memory footprint because there is always only one object travelling the pipeline. In addition, the pipeline processes objects in real time. That's why it is safe to process even large sets of objects. The following line iterates through all files and folders on drive c:. Note how results are returned immediately:

Dir C: -recurse -erroraction SilentlyContinue | ForEach-Object { $_.FullName }

If you tried the same with foreach, the first thing you will notice is that there is no output for a long time. Foreach does not work in real time. So, it first collects all results before it starts to iterate. If you tried to enumerate all files and folders on your drive c:, chances are that your system runs out of memory before it has a chance to process the results. You must be careful with the following statement:

# careful!   
foreach ($element in Dir C: -recurse -erroraction SilentlyContinue) { $element.FullName }

On the other hand, _foreach_ is much faster than _foreach-object_ because the pipeline has a significant overhead. It is up to you to decide whether you need memory efficient real-time processing or fast overall performance:

Measure-Command { 1..10000 | Foreach-Object { $_ } } | Select-Object -expandproperty TotalSeconds   
0,9279656   
Measure-Command { foreach ($element in (1..10000)) { $element } } | Select-Object -expandproperty TotalSeconds   
0,0391117

## Do and While

_Do_ and _While_ generate endless loops. Endless loops are a good idea if you don't know exactly how many times the loop should iterate. You must set additional abort conditions to prevent an endless loop to really run endlessly. The loop will end when the conditions are met.

### Continuation and Abort Conditions

A typical example of an endless loop is a user query that you want to iterate until the user gives a valid answer. How long that lasts and how often the query will iterate depends on the user and his ability to grasp what you want.

    do {
      $Input = Read-Host "Your homepage"
    } while (!($Input -like "www.*.*"))

This loop asks the user for his home page Web address. _While_ is the criteria that has to be _met_ at the end of the loop so that the loop can be iterated once again. In the example, _-like_ is used to verify whether the input matches the www.*.* pattern. While that's only an approximate verification, it usually suffices. You could also use regular expressions to refine your verification. Both procedures will be explained in detail in [Chapter 13][2].

This loop is supposed to re-iterate only if the input is _false_. That's why "!" is used to simply invert the result of the condition. The loop will then be iterated until the input does _not_ match a Web address.

In this type of endless loop, verification of the loop criteria doesn't take place until the end. The loop will go through its iteration at least once because you have to query the user at least once before you can check the criteria.

There are also cases in which the criteria needs to be verified at the beginning and not at the end of the loop. An example would be a text file that you want to read one line at a time. The file could be empty and the loop should check before its first iteration whether there's anything at all to read. To accomplish this, just put the _While_ statement and its criteria at the beginning of the loop (and leave out _Do_, which is no longer of any use):

    # Open a file for reading:
    $file = [system.io.file]::OpenText("C:autoexec.bat")

    # Continue loop until the end of the file has been reached:
    while (!($file.EndOfStream)) {

      # Read and output current line from the file:
      $file.ReadLine()
    }

    # Close file again:
    $file.close

### Using Variables as Continuation Criteria

The truth is that the continuation criteria after _While_ works like a simple switch. If the expression is _$true_, then the loop will be iterated; if it is _$false_, then it won't. Conditions are therefore not mandatory, but simply provide the required _$true_ or _$false_. You could just as well have presented the loop with a variable instead of a comparison operation, as long as the variable contained _$true_ or _$false_.

    do {
      $Input = Read-Host "Your Homepage"
      if ($Input –like "www.*.*") {
        # Input correct, no further query:
        $furtherquery = $false
      } else {
        # Input incorrect, give explanation and query again:
        Write-Host –Fore "Red" "Please give a valid web address."
        $furtherquery = $true
      }
    } while ($furtherquery)
    Your Homepage: hjkh
    Please give a valid web address.
    Your Homepage: www.powershell.com

### Endless Loops without Continuation Criteria

You can also omit continuation criteria and instead simply use the fixed value _$true_ after _While_. The loop will then become a genuinely endless loop, which will never stop on its own. Of course, that makes sense only if you exit the loop in some other way. The _break_ statement can be used for this:

    while ($true) {
      $Input = Read-Host "Your homepage"
      if ($Input –like "www.*.*") {
        # Input correct, no further query:
        break
      } else {
        # Input incorrect, give explanation and ask again:
        Write-Host –Fore "Red" "Please give a valid web address."
      }
    }
    Your homepage: hjkh
    Please give a valid web address.
    Your homepage: www.powershell.com

## For

You can use the _For_ loop if you know exactly how often you want to iterate a particular code segment. _For_ loops are counting loops. You can specify the number at which the loop begins and at which number it will end to define the number of iterations, as well as which increments will be used for counting. The following loop will output a sound at various 100ms frequencies (provided you have a soundcard and the speaker is turned on):

    # Output frequencies from 1000Hz to 4000Hz in 300Hz increments
    for ($frequency=1000; $frequency –le 4000; $frequency +=300) {
      [System.Console]::Beep($frequency,100)
    }

### For Loops: Just Special Types of the While Loop

If you take a closer look at the _For_ loop, you'll quickly notice that it is actually only a specialized form of the _While_ loop. The _For_ loop, in contrast to the _While_ loop, evaluates not only one, but three expressions:

* **Initialization:** The first expression is evaluated when the loop begins.
* **Continuation criteria:** The second expression is evaluated before every iteration. It basically corresponds to the continuation criteria of the _While_ loop. If this expression is _$true_, the loop will iterate.
* **Increment:** The third expression is likewise re-evaluated with every looping, but it is not responsible for iterating. Be careful as this expression cannot generate output.

These three expressions can be used to initialize a control variable, to verify whether a final value is achieved, and to change a control variable with a particular increment at every iteration of the loop. Of course, it is entirely up to you whether you want to use the _For_ loop solely for this purpose.

A _For_ loop can become a _While_ loop if you ignore the first and the second expression and only use the second expression, the continuation criteria:

    # First expression: simple While loop:
    $i = 0
    while ($i –lt 5) {
      $i++
      $i
    }
    1
    2
    3
    4
    5

    # Second expression: the For loop behaves like the While loop:
    $i = 0
    for (;$i -lt 5;) {
      $i++
     $i
    }
    1
    2
    3
    4
    5

### Unusual Uses for the For Loop

Of course in this case, it might have been preferable to use the _While_ loop right from the start. It certainly makes more sense not to ignore the other two expressions of the _For_ loop, but to use them for other purposes. The first expression of the _For_ loop can be generally used for initialization tasks. The third expression sets the increment of a control variable, as well as performs different tasks in the loop. In fact, you can also use it in the user query example we just reviewed:

    for ($Input=""; !($Input -like "www.*.*"); $Input = Read-Host "Your homepage") {
      Write-Host -fore "Red" " Please give a valid web address."
    }

In the first expression, the _$input_ variable is set to an empty string. The second expression checks whether a valid Web address is in _$input_. If it is, it will use "!" to invert the result so that it is _$true_ if an invalid Web address is in _$input_. In this case, the loop is iterated. In the third expression, the user is queried for a Web address. Nothing more needs to be in the loop. In the example, an explanatory text is output.

In addition, the line-by-line reading of a text file can be implemented by a _For_ loop with less code:

    for ($file = [system.io.file]::OpenText("C:autoexec.bat"); !($file.EndOfStream); `  
    $line = $file.ReadLine())
    {
      # Output read line:
      $line
    }
    $file.close()
    REM Dummy file for NTVDM

In this example, the first expression of the loop opened the file so it could be read. In the second expression, a check is made whether the end of the file has been reached. The "!" operator inverts the result again. It will return _$true_ if the end of the file hasn't been reached yet so that the loop will iterate in this case. The third expression reads a line from the file. The read line is then output in the loop.

The third expression of the _For_ loop is executed before every loop cycle. In the example, the current line from the text file is read. This third expression is always executed invisibly, which means you can't use it to output any text. So, the contents of the line are output within the loop.

## Switch

_Switch_ is not only a condition, but also functions like a loop. That makes _Switch_ one of the most powerful statements in PowerShell. _Switch_ works almost exactly like the _Foreach_ loop. Moreover, it can evaluate conditions. For a quick demonstration, take a look at the following simple _Foreach_ loop:

    $array = 1..5
    foreach ($element in $array)
    {
      "Current element: $element"
    }
    Current element: 1
    Current element: 2
    Current element: 3
    Current element: 4
    Current element: 5

If you use switch, this loop would look like this:

    $array = 1..5
    switch ($array)
    {
      Default { "Current element: $_" }
    }
    Current element: 1
    Current element: 2
    Current element: 3
    Current element: 4
    Current element: 5

The control variable that returns the current element of the array for every loop cycle cannot be named for _Switch_, as it can for _Foreach_, but is always called _$__. The external part of the loop functions in exactly the same way. Inside the loop, there's an additional difference: while _Foreach_ always executes the same code every time the loop cycles, _Switch_ can utilize conditions to execute optionally different code for every loop. In the simplest case, the _Switch_ loop contains only the _default_ statement. The code that is to be executed follows it in curly brackets.

That means _Foreach_ is the right choice if you want to execute exactly the same statements for every loop cycle. On the other hand, if you'd like to process each element of an array according to its contents, it would be preferable to use _Switch_:

    $array = 1..5
    switch ($array)
    {
      1  { "The number 1" }
      {$_ -lt 3}  { "$_ is less than 3" }
      {$_ % 2}  { "$_ is odd" }
      Default { "$_ is even" }
    }
    The number 1
    1 is less than 3
    1 is odd
    2 is less than 3
    3 is odd
    4 is even
    5 is odd

If you're wondering why _Switch_ returned this result, take a look at [Chapter 7][1] where you'll find an explanation of how _Switch_ evaluates conditions. What's important here is the other, loop-like aspect of _Switch_.

## Exiting Loops Early

You can exit all loops by using the _Break_ statement, which will give you the additional option of defining additional stop criteria in the loop. The following is a little example of how you can ask for a password and then use _Break_ to exit the loop as soon as the password "secret" is entered.

    while ($true)
    {
      $password = Read-Host "Enter password"
      if ($password -eq "secret") {break}
    }

### Continue: Skipping Loop Cycles

The _Continue_ statement aborts the current loop cycle, but does continue the loop. The next example shows you how to abort processing folders and only focus on files returned by Dir:

    foreach ($entry in Dir $env:windir)
    {
      # If the current element matches the desired type, continue immediately with the next element:
      if (!($entry -is [System.IO.FileInfo])) { continue }

      "File {0} is {1} bytes large." -f $entry.name, $entry.length
    }

Of course, you can also use a condition to filter out sub-folders:

    foreach ($entry in Dir $env:windir)
    {
      if ($entry -is [System.IO.FileInfo]) {
        "File {0} is {1} bytes large." -f $entry.name, $entry.length
      }
    }

This also works in a pipeline using a Where-Object condition:

    Dir $env:windir | Where-Object { $_ -is [System.IO.FileInfo] }

### Nested Loops and Labels

Loops may be nested within each other. However, if you do nest loops, how do _Break_ and _Continue_ work? They will always affect the inner loop, which is the loop that they were called from. However, you can label loops and then submit the label to continue or break if you want to exit or skip outer loops, too.

The next example nests two _Foreach_ loops. The first (outer) loop cycles through a field with three WMI class names. The second (inner) loop runs through all instances of the respective WMI class. This allows you to output all instances of all three WMI classes. The inner loop checks whether the name of the current instance begins with "a"; if not, the inner loop will then invoke _Continue_ skip all instances not beginning with "a." The result is a list of all services, user accounts, and running processes that begin with "a":

    foreach ($wmiclass in "Win32_Service","Win32_UserAccount","Win32_Process")
    {
      foreach ($instance in Get-WmiObject $wmiclass) {
        if (!(($instance.name.toLower()).StartsWith("a"))) {continue}
        "{0}: {1}" –f $instance.__CLASS, $instance.name
      }
    }
    Win32_Service: AeLookupSvc
    Win32_Service: AgereModemAudio
    Win32_Service: ALG
    Win32_Service: Appinfo
    Win32_Service: AppMgmt
    Win32_Service: Ati External Event Utility
    Win32_Service: AudioEndpointBuilder
    Win32_Service: Audiosrv
    Win32_Service: Automatic LiveUpdate – Scheduler
    Win32_UserAccount: Administrator
    Win32_Process: Ati2evxx.exe
    Win32_Process: audiodg.exe
    Win32_Process: Ati2evxx.exe
    Win32_Process: AppSvc32.exe
    Win32_Process: agrsmsvc.exe
    Win32_Process: ATSwpNav.exe

As expected, the _Continue_ statement in the inner loop has had an effect on the inner loop where the statement was contained. But how would you change the code if you'd like to see only the first element of all services, user accounts, and processes that begins with "a"? Actually, you would do almost the exact same thing, except now _Continue_ would need to have an effect on the outer loop. Once an element was found that begins with "a," the outer loop would continue with the next WMI class:

    :WMIClasses foreach ($wmiclass in "Win32_Service","Win32_UserAccount","Win32_Process") {
      :ExamineClasses foreach ($instance in Get-WmiObject $wmiclass) {
        if (($instance.name.toLower()).StartsWith("a")) {
          "{0}: {1}" –f $instance.__CLASS, $instance.name
          continue WMIClasses
        }
      }
    }
    Win32_Service: AeLookupSvc
    Win32_UserAccount: Administrator
    Win32_Process: Ati2evxx.exe

## Summary

The cmdlet _ForEach-Object_ will give you the option of processing single objects of the PowerShell pipeline, such as to output the data contained in object properties as text or to invoke methods of the object. _Foreach_ is a similar type of loop whose contents do not come from the pipeline, but from an array or a collection.

In addition, there are endless loops that iterate a code block until a particular condition is met. The simplest type is _While_, which checks its continuation criteria at the beginning of the loop. If you want to do the checking at the end of the loop, choose _Do…While_. The _For_ loop is an extended _While_ loop, because it can count loop cycles and automatically terminate the loop after a designated number of iterations.

This means that _For_ is best suited for loops which need to be counted or must complete a set number of iterations. On the other hand, _Do...While_ and _While_ are designed for loops that have to be iterated as long as the respective situation and running time conditions require it.

Finally, _Switch_ is a combined _Foreach_ loop with integrated conditions so that you can immediately implement different actions independently of the read element. Moreover, _Switch_ can step through the contents of text files line-by-line and evaluate even log files of substantial size.

All loops can exit ahead of schedule with the help of _Break_ and skip the current loop cycle with the help of _Continue_. In the case of nested loops, you can assign an unambiguous name to the loops and then use this name to apply _Break_ or _Continue_ to nested loops.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-7-conditions.aspx
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-13-text-and-regular-expressions.aspx
