
# Chapter 6. Working with Objects

In this chapter, you will learn what objects are and how to get your hands on PowerShell objects before they get converted to simple text.

**Topics Covered:**
Objects = Properties + Methods 
Creating a New Object 
Adding Properties 
Adding Methods 
Properties: What an Object "Is" 
Properties Containing Objects 
Read-Only and Read-Write Properties 
Table 6.1: Properties of the RawUI object 
Property Types 
Listing All Properties 
Methods: What an Object "Can Do" 
Eliminating "Internal" Methods 
Get_ and Set_ Methods 
Standard Methods 
Table 6.2: Standard methods of a .NET object 
Calling a Method 
Call Methods with Arguments 
Which Arguments are Required? 
Several Method "Signatures" 
Playing with PromptForChoice 
Working with Real-Life Objects 
Storing Results in Variables 
Using Object Properties 
PowerShell-Specific Properties 
Table 6.3: Different property types 
Using Object Methods 
Different Method Types 
Table 6.4: Different types of methods 
Using Static Methods 
Table 6.5: Mathematical functions from the [Math] library 
Finding Interesting .NET Types 
Converting Object Types 
Using Static Type Members 
Using Dynamic Object Instance Members 
Creating New Objects 
Creating New Objects with New-Object 
Using Constructors 
New Objects by Conversion 
Loading Additional Assemblies: Improved Internet Download 
Using COM Objects 
Which COM Objects Are Available? 
How Do You Use COM Objects? 
Summary 


## Objects = Properties + Methods

In real life, you already know what an object is: everything you can touch. Objects in PowerShell are similar. Let's turn a typical real-world object, like a pocketknife, into a PowerShell object.

How would you describe this object to someone, over the telephone? You would probably carefully examine the object and then describe what it _is_ and what it _can do_:

* **Properties:** A pocketknife has particular properties, such as its color, manufacturer, size, or number of blades. The object _is_ red, weights 55 grams, has three blades, and is made by the firm Idera. So properties< describe what an object _is_.
* **Methods:** n addition, you can do things with this object, such as cut, turn screws, or pull corks out of wine bottles. The object _can_ cut, screw, and remove corks. Everything that an object _can_ is called its _methods_.

In the computing world, an object is very similar: its nature is described by properties, and the actions it can perform are called its methods. Properties and methods are called _members_.

### Creating a New Object

Let's turn our real-life pocketknife into a virtual pocketknife. Using _New-Object_, PowerShell can generate new objects, even a virtual pocketknife. First, you will need a new and empty object:

    $pocketknife = New-Object Object

This new object is actually pretty useless. If you call for it, PowerShell will return "nothing":

    $pocketknife

### Adding Properties

Next, let's start describing what our object _is_. To do that, you can add properties to the object.

    # Adding a new property:
    Add-Member -MemberType NoteProperty -Name Color-Value Red -InputObject $pocketknife

You can uUse the _Add-Member_ cmdlet to add properties. Here, you added the property _color_ with the value _red_ to the object _$pocketknife_. If you call for the object now, it suddenly has a property telling the world that its color is red:

    $pocketknife
    Color
    -----
    Red

You can then add more properties to describe the object even better. This time, we use positional parameters to shorten the code necessary to add members to the object:

    $pocketknife | Add-Member NoteProperty Weight 55
    $pocketknife | Add-Member NoteProperty Manufacturer Idera
    $pocketknife | Add-Member NoteProperty Blades 3

By now, you've described the object in _$pocketknife_ with a total of four properties. If you output the object in _$pocketknife_ in the PowerShell console, PowerShell will automatically convert the object into readable text:

    # Show all properties of the object all at once:
    $pocketknife
    Color                                    Weight   Manufacturer                      Blades
    -----                                    -------  ----------                        -------
    Red                                          55   Idera                                   3

You will now get a quick overview of its properties when you output the object to the console. You can access the value of a specific property by either using _Select-Object_ with the parameter _-expandProperty_, or add a dot, and then the property name:

    # Display a particular property:
    $pocketknife | Select-Object -expandProperty Manufacturer
    $pocketknife.manufacturer

### Adding Methods

With every new property you added to your object, _$pocketknife_ has been gradually taking shape, but it still really can't do anything. Properties only describe what an object _is_, not what it can _do_.

The actions your object can do are called its _methods_. So let's teach your object a few useful methods:

    # Adding new methods:
    $pocketknife | Add-Member ScriptMethod cut { "I'm whittling now" }
    $pocketknife | Add-Member ScriptMethod screw { "Phew...it's in!" }

    $pocketknife | Add-Member ScriptMethod corkscrew { "Pop! Cheers!" }

Again, you used the _Add-Member cmdlet_, but this time you added a method instead of a property (in this case, a _ScriptMethod_). The value is a scriptblock marked by brackets, which contains the PowerShell instructions you want the method to perform. If you output your object, it will still look the same because PowerShell only visualizes object properties, not methods:

    $pocketknife
    Color                                      Weight Manufacturer                          Blades
    -----                                     ------- ----------                           -------
    Red                                            55 Idera                                 3

You can add a dot and then the method name followed by two parentheses to use any of the three newly added methods. They are part of the method name, so be sure to not put a space between the method name and the opening parenthesis. Parentheses formally distinguishes properties from methods.

For example, if you'd like to remove a cork with your virtual pocketknife, you can use this code:

    $pocketknife.corkscrew()
    Pop! Cheers!

Your object really does carry out the exact script commands you assigned to the _corkscrew()_ method. So, methods perform actions, while properties merely provide information. Always remember to add parentheses to method names. If you forget them, something interesting like this will happen:

    # If you don't use parentheses, you'll retrieve information on a method:
    $pocketknife.corkscrew
    Script              : "Pop! Cheers!"
    OverloadDefinitions : {System.Object corkscrew();}
    MemberType          : ScriptMethod
    TypeNameOfValue     : System.Object
    Value               : System.Object corkscrew();
    Name                : corkscrew
    IsInstance          : True

You just received a method description. What's interesting about this is mainly the _OverloadDefinitions_ property. As you'll see later, it reveals the exact way to use a command for any method. In fact, the _OverloadDefinitions_ information is in an additional object. For PowerShell, absolutely everything is an object so you can store the object in a variable and then specifically ask the _OverloadDefinitions_ property for information:

    # Information about a method is returned in an object of its own:
    $info = $pocketknife.corkscrew
    $info.OverloadDefinitions
    System.Object corkscrew();

The "virtual pocketknife" example reveals that objects are containers that contain data (properties) and actions (methods).

Our virtual pocketknife was a somewhat artificial object with no real use. Next, let's take a look at a more interesting object: PowerShell! There is a variable called $host which represents your PowerShell _host_.

## Properties: What an Object "Is"

There are just two important rules: Properties describe an object. And object properties are automatically turned into text when you output the object to the console. That's enough to investigate any object. Check out the properties in _$host_!

    $Host
    Name             : ConsoleHost
    Version          : 1.0.0.0
    InstanceId       : e32debaf-3d10-4c4c-9bc6-ea58f8f17a8f
    UI               : System.Management.Automation.Internal.Host.InternalHostUserInterface
    CurrentCulture   : en-US
    CurrentUICulture : en-US
    PrivateData      : Microsoft.PowerShell.ConsoleHost+ConsoleColorProxy

The object stored in the variable _$host_ apparently contains seven properties. The properties' names are listed in the first column. So, if you want to find out which PowerShell version you're using, you could access and return the _Version_ property:

    $Host.Version
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    1      0      0      0

It works—you get back the PowerShell host version. The version isn't displayed as a single number. Instead, PowerShell displays four columns: _Major_, _Minor_, _Build_, and _Revision_. Whenever you see columns, you know these are object properties that PowerShell has just converted into text. So, the version in itself is again a special object designed to store version numbers. Let's check out the data type that the _Version_ property uses:

    $version = $Host.Version
    $version.GetType().FullName
    System.Version

The version is not stored as a _String_ object but as a _System.Version_ object. This object type is perfect for storing versions, allowing you to easily read all details about any given version:

    $Host.Version.Major
    1
    $Host.Version.Build
    0

Knowing an object type is very useful because once you know there is a type called _System.Version_, you can use it for your own purposes as well. Try to convert a simple _string_ of your choice into a rich _version_ object! To do that, simply make sure the string consists of four numbers separated by dots (the typical format for versions), then make PowerShell convert the string into a System.Version type. You can convert things by adding the target type in square brackets in front of the string:

    [System.Version]'12.55.3.28334'
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    12     55     3      28334

The _CurrentCulture_ property is just another example of the same concept. Read this property to find out its type:

    $Host.CurrentCulture
    LCID             Name             DisplayName
    ----             ----             -----------
    1033             en-US            English (United States)

    $Host.CurrentCulture.GetType().FullName
    System.Globalization.CultureInfo

Country properties are again stored in a highly specialized type that describes a culture with the properties _LCID_, _Name_, and _DisplayName_. If you want to know which international version of PowerShell you are using, you can read the _DisplayName_ property:

    $Host.CurrentCulture.DisplayName
    English (United States)
    $Host.CurrentCulture.DisplayName.GetType().FullName
    System.String

Likewise, you can convert any suitable string into a _CultureInfo-object_. Try this if you wanted to find out details about the 'de-DE' locale:

    [System.Globalization.CultureInfo]'de-DE'
    LCID             Name             DisplayName
    ----             ----             -----------
    1031             de-DE            German (Germany)

You can also convert the LCID into a _CultureInfo_ object by converting a suitable number:

    [System.Globalization.CultureInfo]1033
    LCID             Name             DisplayName
    ----             ----             -----------
    1033             en-US            English (United States)

### Properties Containing Objects

The properties of an object store data. In turn, this data is stored in various other objects.Two properties in _$host_ seem to be special: _UI_ and _PrivateData_. When you output $host into the console, all other properties will be converted into readable text – except for the properties UI and PrivateData:

    $Host
    Name             : ConsoleHost
    Version          : 1.0.0.0
    InstanceId       : e32debaf-3d10-4c4c-9bc6-ea58f8f17a8f
    UI               : System.Management.Automation.Internal.Host.InternalHostUserInterface
    CurrentCulture   : en-US
    CurrentUICulture : en-US
    PrivateData      : Microsoft.PowerShell.ConsoleHost+ConsoleColorProxy

This is because both these properties again contain an object. If you'd like to find out what is actually stored in the _UI_ property, you can read the property:

    $Host.UI
    RawUI
    -----
    System.Management.Automation.Internal.Host.InternalHostRawUserInterface

You see that the property _UI_ contains only a single property called _RawUI_, in which yet another object is stored. Let's see what sort of object is stored in the _RawUI_ property:

    $Host.ui.rawui
    ForegroundColor       : DarkYellow
    BackgroundColor       : DarkMagenta
    CursorPosition        : 0,136
    WindowPosition        : 0,87
    CursorSize            : 25
    BufferSize            : 120,3000
    WindowSize            : 120,50
    MaxWindowSize         : 120,62
    MaxPhysicalWindowSize : 140,62
    KeyAvailable          : False
    WindowTitle           : PowerShell

"RawUI" stands for "Raw User Interface" and exposes the raw user interface settings your PowerShell console uses. You can read all of these properties, but can you also change them?

### Read-Only and Read-Write Properties

Can you actually change properties, too? And if you can, what happens next?

Properties need to accurately describe an object. So, if you modify a property, the underlying object has to also be modified to reflect that change. If this is not possible, the property cannot be changed and is called "read-only."

Console background and foreground colors are a great example of properties you can easily change. If you do, the console will change colors accordingly. Your property changes are reflected by the object, and the changed properties still accurately describe the object.

    $Host.ui.rawui.BackgroundColor = "Green"
    $Host.ui.rawui.ForegroundColor = "White"

Type _cls_ so the entire console adopts this color scheme.

Other properties cannot be changed. If you try anyway, you'll get an error message:

    $Host.ui.rawui.keyavailable = $true
    "KeyAvailable" is a ReadOnly-property.
    At line:1 char:16
    + $Host.ui.rawui.k <<<< eyavailable = $true

Whether the console receives key press input or not, depends on whether you pressed a key or not. You cannot control that by changing a property, so this property refuses to be changed. You can only read it.

| ----- |
| Property |  Description |
| ForegroundColor |  Text color. Optional values are _Black_, _DarkBlue_, _DarkGreen_, _DarkCyan_, _DarkRed_, _DarkMagenta_, _DarkYellow_, _Gray_, _DarkGray_, _Blue_, _Green_, _Cyan_, _Red_, _Magenta_, _Yellow_, and _White_. |
| BackgroundColor |  Background color. Optional values are _Black_, _DarkBlue_, _DarkGreen_, _DarkCyan_, _DarkRed_, _DarkMagenta_, _DarkYellow_, _Gray_, _DarkGray_, _Blue_, _Green_, _Cyan_, _Red_, _Magenta_, _Yellow_, and _White_. |
| CursorPosition |  Current position of the cursor |
| WindowPosition |  Current position of the window |
| CursorSize |  Size of the cursor |
| BufferSize |  Size of the screen buffer |
| WindowSize |  Size of the visible window |
| MaxWindowSize |  Maximally permissible window size |
| MaxPhysicalWindowSize |  Maximum possible window size |
| KeyAvailable |  Makes key press input available |
| WindowTitle |  Text in the window title bar |

**Table 6.1:** Properties of the RawUI object

### Property Types

Some properties accept numeric values. For example, the size of a blinking cursor is specified as a number from _0_ to _100_ and corresponds to the fill percentage. The next line sets a cursor size of 75%. Values outside the 0-100 numeric range will generate an error:

    # A value from 0 to 100 is permitted:
    $Host.ui.rawui.cursorsize = 75

    # Values outside this range will generate an error:
    $Host.ui.rawui.cursorsize = 1000
    Exception setting "CursorSize": "Cannot process "CursorSize" because the cursor   
    size specified is invalid.
    Parameter name: value
    Actual value was 1000."
    At line:1 char:16
    + $Host.ui.rawui.c <<<< ursorsize = 1000

Other properties expect color settings. However, you cannot specify any color that comes to mind. Instead, PowerShell expects a "valid" color and if your color is unknown, you will receive an error message listing the colors you can use:

    # Colors are specified as text (in quotation marks):
    $Host.ui.rawui.ForegroundColor = "yellow"

    # Not all colors are allowed:
    $Host.ui.rawui.ForegroundColor = "pink"
    Exception setting "ForegroundColor": "Cannot convert value "pink" to type   
    "System.ConsoleColor" due to invalid enumeration values. Specify one of the   
    following enumeration values and try again. The possible enumeration values are   
    "Black, DarkBlue, DarkGreen, DarkCyan, DarkRed, DarkMagenta, DarkYellow, Gray,   
    DarkGray, Blue, Green, Cyan, Red, Magenta, Yellow, White"."
    At line:1 char:16
    + $Host.ui.rawui.F <<<< oregroundColor = "pink"

If you assign an invalid value to the property _ForegroundColor_, the error message will list the possible values. If you assign an invalid value to the property _CursorSize_, you get no hint. Why?

Every property expects a certain object type. Some object types are more specific than others. You can use _Get-Member_ to find out which object types a given property will expect:

    $Host.ui.RawUI | Get-Member -MemberType Property
       TypeName: System.Management.Automation.Internal.Host.InternalHostRawUserInterface

    Name                  MemberType Definition
    ----                  ---------- ----------
    BackgroundColor       Property   System.ConsoleColor BackgroundColor {get;set;}
    BufferSize            Property   System.Management.Automation.Host.Size BufferSize {get;set;}
    CursorPosition        Property   System.Management.Automation.Host.Coordinates CursorPosition {get;set;}
    CursorSize            Property   System.Int32 CursorSize {get;set;}
    ForegroundColor       Property   System.ConsoleColor ForegroundColor {get;set;}
    KeyAvailable          Property   System.Boolean KeyAvailable {get;}
    MaxPhysicalWindowSize Property   System.Management.Automation.Host.Size MaxPhysicalWindowSize {get;}
    MaxWindowSize         Property   System.Management.Automation.Host.Size MaxWindowSize {get;}
    WindowPosition        Property   System.Management.Automation.Host.Coordinates WindowPosition {get;set;}
    WindowSize            Property   System.Management.Automation.Host.Size WindowSize {get;set;}
    WindowTitle           Property   System.String WindowTitle {get;set;}

As you can see, _ForegroundColor_ expects a _System.ConsoleColor_ type. This type is a highly specialized type: a list of possible values, a so-called enumeration:

    [system.ConsoleColor].IsEnum
    True

Whenever a type is an enumeration, you can use a special .NET method called _GetNames()_ to list the possible values defined in that enumeration:

    [System.Enum]::GetNames([System.ConsoleColor])
    Black
    DarkBlue
    DarkGreen
    DarkCyan
    DarkRed
    DarkMagenta
    DarkYellow
    Gray
    DarkGray
    Blue
    Green
    Cyan
    Red
    Magenta
    Yellow
    White

If you do not specify anything contained in the enumeration, the error message will simply return the enumeration's contents.

_CursorSize_ stores its data in a _System.Int32_ object, which is simply a 32-bit number. So, if you try to set the cursor size to 1,000, you are actually not violating the object boundaries because the value of 1,000 can be stored in a _System.Int32_ object. You get an error message anyway because of the validation code that the _CursorSize_ property executes internally. So, whether you get detailed error information will really depend on the property's definition. In the case of _CursorSize_, you will receive only an indication that your value is invalid, but not why.

Sometimes, a property expects a value to be wrapped in a specific object. For example, if you'd like to change the PowerShell window size, you can use the _WindowSize_ property. As it turns out, the property expects a new window size wrapped in an object of type _System.Management.Automation.Host.Size_. Where can you get an object like that?

    $Host.ui.rawui.WindowSize = 100,100
    Exception setting "WindowSize": "Cannot convert "System.Object[]"   
    to "System.Management.Automation.Host.Size"."
    At line:1 char:16
    + $Host.ui.rawui.W <<<< indowSize = 100,100

There are a number of ways to provide specialized objects for properties. The easiest approach: read the existing value of a property (which will get you the object type you need), change the result, and then write back the changes. For example, here's how you would change the PowerShell window size to 80 x 30 characters:

    $value = $Host.ui.rawui.WindowSize
    $value
                                          Width                                      Height
                                          -----                                      ------
                                            110                                          64
    $value.Width = 80
    $value.Height = 30
    $Host.ui.rawui.WindowSize = $value

Or, you can freshly create the object you need by using New-Object:

    $value = New-Object System.Management.Automation.Host.Size(80,30)
    $Host.ui.rawui.WindowSize = $value

Or in a line:

    $host.ui.rawui.WindowSize = New-Object System.Management.Automation.Host.Size(80,30)

### Listing All Properties

_Get-Member_ will return detailed information about them because properties and methods are all _members_ of an object. Let's use _Get-Member_ to examine all properties defined in _$host_. To limit _Get-Member_ to only properties, you can use the _memberType_ parameter and specify "property":

    $Host | Get-Member -memberType property
    Name             MemberType Definition
    ----             ---------- ----------
    CurrentCulture   Property   System.Globalization.CultureInfo CurrentCulture {get;}
    CurrentUICulture Property   System.Globalization.CultureInfo CurrentUICulture {get;}
    InstanceId       Property   System.Guid InstanceId {get;}
    Name             Property   System.String Name {get;}
    PrivateData      Property   System.Management.Automation.PSObject PrivateData {get;}
    UI               Property   System.Management.Automation.Host.PSHostUserInterface UI {get;}
    Version          Property   System.Version Version {get;}

In the column _Name_, you will now see all supported properties in _$host_. In the column _Definition_, the property object type is listed first. For example, you can see that the _Name_ property stores a text as _System.String_ type. The _Version_ property uses the _System.Version_ type.

At the end of each definition, curly brackets will report whether the property is read-only ({get;}) or can also be modified ({get;set;}). You can see at a glance that all properties of the _$host_ object are only readable. Now, take a look at the _$host.ui.rawui_ object:

    $Host.ui.rawui | Get-Member -membertype property
    BackgroundColor       Property   System.ConsoleColor BackgroundColor {get;set;}
    BufferSize            Property   System.Management.Automation.Host.Size BufferSize {get;set;}
    CursorPosition        Property   System.Management.Automation.Host.Coordinates CursorPosition {get;set;}
    CursorSize            Property   System.Int32 CursorSize {get;set;}
    ForegroundColor       Property   System.ConsoleColor ForegroundColor {get;set;}
    KeyAvailable          Property   System.Boolean KeyAvailable {get;}
    MaxPhysicalWindowSize Property   System.Management.Automation.Host.Size MaxPhysicalWindowSize {get;}
    MaxWindowSize         Property   System.Management.Automation.Host.Size MaxWindowSize {get;}
    WindowPosition        Property   System.Management.Automation.Host.Coordinates WindowPosition {get;set;}
    WindowSize            Property   System.Management.Automation.Host.Size WindowSize {get;set;}
    WindowTitle           Property   System.String WindowTitle {get;set;}

This result is more differentiated. It shows you that some properties could be changed, while others could not.

There are different "sorts" of properties. Most properties are of the _Property_ type, but PowerShell can add additional properties like _ScriptProperty_. So if you really want to list all properties, you can use the -MemberType parameter and assign it a value of _*Property_. The wildcard in front of "property" will also select all specialized properties like "ScriptProperty."

## Methods: What an Object "Can Do"

Methods are things that an object _can do_.Only its properties are converted into readable text when you output an object to the console. Methods remain invisible. You can use _Get-Member_ and the parameter "memberType" with the value "method" to list the methods of an object:

    $Host | Get-Member -memberType Method

       TypeName: System.Management.Automation.Internal.Host.InternalHost

    Name                   MemberType Definition
    ----                   ---------- ----------
    EnterNestedPrompt      Method     System.Void EnterNestedPrompt()
    Equals                 Method     bool Equals(System.Object obj)
    ExitNestedPrompt       Method     System.Void ExitNestedPrompt()
    GetHashCode            Method     int GetHashCode()
    GetType                Method     type GetType()
    NotifyBeginApplication Method     System.Void NotifyBeginApplication()
    NotifyEndApplication   Method     System.Void NotifyEndApplication()
    PopRunspace            Method     System.Void PopRunspace()
    PushRunspace           Method     System.Void PushRunspace(runspace runspace)
    SetShouldExit          Method     System.Void SetShouldExit(int exitCode)
    ToString               Method     string ToString()

### Eliminating "Internal" Methods

_Get-Member_ does not list all methods defined by an object. It will skip methods that are used internally. You can force Get-Member to list all methods by adding the -Force parameter:

    PS > $Host | Get-Member -memberType Method -Force

       TypeName: System.Management.Automation.Internal.Host.InternalHost

    Name                   MemberType Definition
    ----                   ---------- ----------
    (...)
    get_CurrentCulture     Method     System.Globalization.CultureInfo get_Curre...
    get_CurrentUICulture   Method     System.Globalization.CultureInfo get_Curre...
    get_InstanceId         Method     System.Guid get_InstanceId()
    get_IsRunspacePushed   Method     bool get_IsRunspacePushed()
    get_Name               Method     string get_Name()
    get_PrivateData        Method     psobject get_PrivateData()
    get_Runspace           Method     runspace get_Runspace()
    get_UI                 Method     System.Management.Automation.Host.PSHostUs...
    get_Version            Method     System.Version get_Version()
    (...)

#### Get_ and Set_ Methods

Any method that starts with "get_" is really designed to retrieve a property value. So the method "get_someInfo()" will retrieve the very same information you could also have gotten with the "someInfo" property.

    # Query property:
    $Host.version

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    2      0      -1     -1

    # Query property value using getter method:
    $Host.get_Version()
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    2      0      -1     -1

The same is true for _Set_ methods:_ they change a property value and exist for properties that are read/writeable. Note in this example: all properties of the _$host_ object can only be read so there are no _Set__ methods. There can be more internal methods like this, such as _Add__ and _Remove__ methods. Generally speaking, when a method name contains an underscore, it is most likely an internal method.

#### Standard Methods

In addition, nearly every object contains a number of "inherited" methods that are also not specific to the object but perform general tasks for every object:

| ----- |
| Method |  Description |
| Equals |  Verifies whether the object is identical to a comparison object |
| GetHashCode |  Retrieves an object's digital "fingerprint"  |
| GetType |  Retrieves the underlying object type |
| ToString |  Converts the object into readable text |

**Table 6.2:** Standard methods of a .NET object

### Calling a Method

_Before_ you invoke a method: make sure you know what the method will do. Methods are commands that do something, which could be dangerous. You can add a dot to the object and then the method name to call a method. Add an opened and closed parenthesis, like this:

    $host.EnterNestedPrompt()

The PowerShell prompt changes to ">>" (unless you changed your default _prompt_ function). You have used _EnterNestedPrompt()_ to open a nested prompt. Nested prompts are not especially useful in a normal console, so be sure to exit it again using the _exit_ command or call _$host.ExitNestedPrompt()_.

Nested prompts can be useful in functions or scripts because they work like breakpoints. They can temporarily stop a function or script so you can verify variable contents or make code changes, after which you continue the code by entering _exit_. You'll learn more about this in [Chapter 11][1].

### Call Methods with Arguments

There are many useful methods in the _UI_ object. Here's how you get a good overview:

    $Host.ui | Get-Member -membertype Method
       TypeName: System.Management.Automation.Internal.Host.InternalHostUserInterface

    Name                   MemberType Definition
    ----                   ---------- ----------
    Equals                 Method     System.Boolean Equals(Object obj)
    GetHashCode            Method     System.Int32 GetHashCode()
    GetType                Method     System.Type GetType()
    get_RawUI              Method     System.Management.Automation.Host.PSHostRawUserInterface get_RawUI()
    Prompt                 Method     System.Collections.Generic.Dictionary`2[[System.String, mscorlib, Versio...
    PromptForChoice        Method     System.Int32 PromptForChoice(String caption, String message, Collection`...
    PromptForCredential    Method     System.Management.Automation.PSCredential PromptForCredential(String cap...
    ReadLine               Method     System.String ReadLine()
    ReadLineAsSecureString Method     System.Security.SecureString ReadLineAsSecureString()
    ToString               Method     System.String ToString()
    Write                  Method     System.Void Write(String value), System.Void Write(ConsoleColor foregrou...
    WriteDebugLine         Method     System.Void WriteDebugLine(String message)
    WriteErrorLine         Method     System.Void WriteErrorLine(String value)
    WriteLine              Method     System.Void WriteLine(), System.Void WriteLine(String value), System.Voi...
    WriteProgress          Method     System.Void WriteProgress(Int64 sourceId, ProgressRecord record)
    WriteVerboseLine       Method     System.Void WriteVerboseLine(String message)
    WriteWarningLine       Method     System.Void WriteWarningLine(String message)

Most methods require additional arguments from you, which are listed in the _Definition_ column.

#### Which Arguments are Required?

Pick out a method from the list, and then ask _Get-Member_ to get you more info. Let's pick _WriteDebugLine()_:

    # Ask for data on the WriteDebugLine method in $host.ui:
    $info = $Host.UI | Get-Member WriteDebugLine

    # $info contains all the data on this method:
    $info
       TypeName: System.Management.Automation.Internal.Host.InternalHostUserInterface
    Name           MemberType Definition
    ----           ---------- ----------
    WriteDebugLine Method     System.Void WriteDebugLine(String message)

    # Definition shows which arguments are required and which result will be returned:
    $info.Definition
    System.Void WriteDebugLine(String message)

The _Definition_ property tells you how to call the method. Every definition will begin with the object type that a method returns. In this example, it is _System.Void_, a special object type because it represents "nothing": the method doesn't return anything at all. A method "returning" _System.Void_ is really a procedure, not a function.

Next, a method's name follows, which is then followed by required arguments. _WriteDebugLine_ needs exactly one argument called _message_, which is of _String_ type. Here is how you call _WriteDebugLine()_:

    $Host.ui.WriteDebugLine("Hello!")
    Hello!

### Several Method "Signatures"

Some methods accept different argument types, or even different numbers of arguments. To find out which "signatures" a method supports, you can use _Get-Member_ again and look at the _Definition_ property:

    $info = $Host.UI | Get-Member WriteLine
    $info.Definition
    System.Void WriteLine(), System.Void WriteLine(String value), System.Void   
    WriteLine(ConsoleColor foregroundColor, ConsoleColor backgroundColor, String value)

The definition is hard to read at first. You can make it more readable by using _Replace()_ to add line breaks.

Remember the "backtick" character ("`"). It introduces special characters; "`n" stands for a line break.

    $info.Definition.Replace("), ", ")`n")
    System.Void WriteLine()
    System.Void WriteLine(String value)
    System.Void WriteLine(ConsoleColor foregroundColor, ConsoleColor backgroundColor, String value)

This definition tells you: You do not necessarily need to supply arguments:

    $host.ui.WriteLine()

The result is an empty line.

To output text, you can specify one argument only, the text itself:

    $Host.ui.WriteLine("Hello world!")
    Hello world!

The third variant adds support for foreground and background colors:

    $host.ui.WriteLine("Red", "White", "Alarm!")

_WriteLine()_ actually is the low-level function of the _Write-Host_ cmdlet:

    Write-Host
    Write-Host "Hello World!"
    Write-Host -ForegroundColor Red -BackgroundColor White Alarm!

#### Playing with PromptForChoice

So far, most methods you examined have turned out to be low-level commands for cmdlets. This is also true for the following methods: _Write()_ (corresponds to _Write-Host -nonewline_) or _ReadLine()/ReadLineAsSecureString()_ (_read-host -asSecureString_) or _PromptForCredential()_ (_get-credential_).

A new functionality is exposed by the method _PromptForChoice()_. Let's first examine which arguments this method expects:

    $info = $Host.UI | Get-Member PromptForChoice
    $info.Definition
    System.Int32 PromptForChoice(String caption, String message, Collection`1 choices,   
    Int32 defaultChoice)

You can get the same information if you call the method without parentheses:

    You can get the same information if you call the method without parentheses:
    $Host.ui.PromptForChoice
    MemberType          : Method
    OverloadDefinitions : {System.Int32 PromptForChoice(String caption, String message,   
    Collection`1 choices, Int 32 defaultChoice)}
    TypeNameOfValue     : System.Management.Automation.PSMethod
    Value               : System.Int32 PromptForChoice(String caption, String message,   
    Collection`1 choices, Int32 defaultChoice)
    Name                : PromptForChoice
    IsInstance          : True

The definition reveals that this method returns a numeric value (_System.Int32_). It requires a heading and a message respectively as text (_String_). The third argument is a bit strange: _Collection`1 choices_. The fourth argument is a number (_Int32_), the standard selection. You may have noticed by now the limitations of PowerShell's built-in description.

This is how you can use _PromptForChoice()_ to create a simple menu:

    $yes = ([System.Management.Automation.Host.ChoiceDescription]"&yes")
    $no = ([System.Management.Automation.Host.ChoiceDescription]"&no")
    $selection = [System.Management.Automation.Host.ChoiceDescription[]]($yes,$no)
    $answer = $Host.ui.PromptForChoice('Reboot', 'May the system now be rebooted?',$selection,1)
    $selection[$answer]
    if ($selection -eq 0) {
      "Reboot"
    } else {
      "OK, then not"
    }

## Working with Real-Life Objects

Every PowerShell command will return objects. However, it is not that easy to get your hands on objects because PowerShell converts them to text whenever you output them to the console.

### Storing Results in Variables

Save the result to variable to examine the object nature of results you receive from cmdlets.

    $listing = Dir $env:windir

When you dump the variable content to the console, the results stored inside of it will be converted to plain text, much like if you had output the information to the console in the first place:

    $listing
        Directory: Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltner
    Mode                LastWriteTime     Length Name
    ----                -------------     ------ ----
    d----        20.07.2007     11:37            Application data
    d----        26.07.2007     11:03            Backup
    d-r--        13.04.2007     15:05            Contacts
    d----        28.06.2007     18:33            Debug
    (...)

To get to the real objects, you can directly access them inside of a variable. _Dir_ has stored its result in $listing. It is wrapped in an array since the _listing_ consists of more than one entry. Access an array element to get your hands on a real object:

    # Access first element in listing
    $object = $listing[0]

    # Object is converted into text when you output it in the console
    $object
        Directory: Microsoft.PowerShell.CoreFileSystem::C:UsersTobias Weltner
    Mode                LastWriteTime     Length Name
    ----                -------------     ------ ----
    d----        20.07.2007     11:37            Application data

The object picked here happens to match the folder _Application Data;_ so it represents a directory. You can do this if you prefer to directly pick a particular directory or file:

    # Address a particular file:
    $object = Get-Item $env:windirexplorer.exe

    # Address a folder:
    $object = Get-Item $env:windir

#### Using Object Properties

You can use _Get-Member_ again to produce a list of all available properties:

    # $object is a fully functional object that describes the "Application Data" directory
    # First, list all object properties:
    $object | Get-Member -membertype *property
    Name              MemberType     Definition
    ----              ----------     ----------
    Mode              CodeProperty   System.String Mode{get=Mode;}
    PSChildName       NoteProperty   System.String PSChildName=Windows
    PSDrive           NoteProperty   System.Management.Automation.PSDriveInfo PS...
    PSIsContainer     NoteProperty   System.Boolean PSIsContainer=True
    PSParentPath      NoteProperty   System.String PSParentPath=Microsoft.PowerS...
    PSPath            NoteProperty   System.String PSPath=Microsoft.PowerShell.C...
    PSProvider        NoteProperty   System.Management.Automation.ProviderInfo P...
    Attributes        Property       System.IO.FileAttributes Attributes {get;set;}
    CreationTime      Property       System.DateTime CreationTime {get;set;}
    CreationTimeUtc   Property       System.DateTime CreationTimeUtc {get;set;}
    Exists            Property       System.Boolean Exists {get;}
    Extension         Property       System.String Extension {get;}
    FullName          Property       System.String FullName {get;}
    LastAccessTime    Property       System.DateTime LastAccessTime {get;set;}
    LastAccessTimeUtc Property       System.DateTime LastAccessTimeUtc {get;set;}
    LastWriteTime     Property       System.DateTime LastWriteTime {get;set;}
    LastWriteTimeUtc  Property       System.DateTime LastWriteTimeUtc {get;set;}
    Name              Property       System.String Name {get;}
    Parent            Property       System.IO.DirectoryInfo Parent {get;}
    Root              Property       System.IO.DirectoryInfo Root {get;}
    BaseName          ScriptProperty System.Object BaseName {get=$this.Name;}

Properties marked with _{get;set;}_ in the column _Definition_ are readable and writeable. You can actually change their value, too, by simply assigning a new value (provided you have sufficient privileges):

    # Determine last access date:
    $object.LastAccessTime
    Friday, July 20, 2007 11:37:39

    # Change Date:
    $object.LastAccessTime = Get-Date

    # Change was accepted:
    $object.LastAccessTime
    Monday, October 1, 2007 15:31:41

#### PowerShell-Specific Properties

PowerShell can add additional properties to an object. Whenever that occurs, _Get-Member_ will label the property accordingly in the _MemberType_ column. Native properties are just called "Property." Properties that are added by PowerShell use a prefix, such as "ScriptProperty" or "NoteProperty."

A _NoteProperty_ like _PSChildName_ contains static data. PowerShell will add it to tag additional information to an object. A _ScriptProperty_ like Mode executes PowerShell script code that calculates the property's value.

| ----- |
| MemberType |  Description |
| AliasProperty |  Alternative name for a property that already exists |
| CodeProperty |  Static .NET method returns property contents |
| Property |  Genuine property |
| NoteProperty |  Subsequently added property with set data value |
| ScriptProperty |  Subsequently added property whose value is calculated by a script |
| ParameterizedProperty |  Property requiring additional arguments |

**Table 6.3:** Different property types

#### Using Object Methods

Use _Get-Member_ to find out the methods that an object supports:

    # List all methods of the object:
    $object | Get-Member -membertype *method
       TypeName: System.IO.DirectoryInfo

    Name                      MemberType Definition
    ----                      ---------- ----------
    Create                    Method     System.Void Create(), System.Void Create(DirectorySecurity DirectoryS...
    CreateObjRef              Method     System.Runtime.Remoting.ObjRef CreateObjRef(Type requestedType)
    CreateSubDirectory        Method     System.IO.DirectoryInfo CreateSubDirectory(String path), System.IO.Di...
    Delete                    Method     System.Void Delete(), System.Void Delete(Boolean recursive)
    Equals                    Method     System.Boolean Equals(Object obj)
    GetAccessControl          Method     System.Security.AccessControl.DirectorySecurity GetAccessControl(), S...
    GetDirectories            Method     System.IO.DirectoryInfo[] GetDirectories(), System.IO.DirectoryInfo[]...
    GetFiles                  Method     System.IO.FileInfo[] GetFiles(String searchPattern), System.IO.FileIn...
    GetFileSystemInfos        Method     System.IO.FileSystemInfo[] GetFileSystemInfos(String searchPattern), ...
    GetHashCode               Method     System.Int32 GetHashCode()
    GetLifetimeService        Method     System.Object GetLifetimeService()
    GetObjectData             Method     System.Void GetObjectData(SerializationInfo info, StreamingContext co...
    GetType                   Method     System.Type GetType()
    get_Attributes            Method     System.IO.FileAttributes get_Attributes()
    get_CreationTime          Method     System.DateTime get_CreationTime()
    get_CreationTimeUtc       Method     System.DateTime get_CreationTimeUtc()
    get_Exists                Method     System.Boolean get_Exists()
    get_Extension             Method     System.String get_Extension()
    get_FullName              Method     System.String get_FullName()
    get_LastAccessTime        Method     System.DateTime get_LastAccessTime()
    get_LastAccessTimeUtc     Method     System.DateTime get_LastAccessTimeUtc()
    get_LastWriteTime         Method     System.DateTime get_LastWriteTime()
    get_LastWriteTimeUtc      Method     System.DateTime get_LastWriteTimeUtc()
    get_Name                  Method     System.String get_Name()
    get_Parent                Method     System.IO.DirectoryInfo get_Parent()
    get_Root                  Method     System.IO.DirectoryInfo get_Root()
    InitializeLifetimeService Method     System.Object InitializeLifetimeService()
    MoveTo                    Method     System.Void MoveTo(String destDirName)
    Refresh                   Method     System.Void Refresh()
    SetAccessControl          Method     System.Void SetAccessControl(DirectorySecurity DirectorySecurity)
    set_Attributes            Method     System.Void set_Attributes(FileAttributes value)
    set_CreationTime          Method     System.Void set_CreationTime(DateTime value)
    set_CreationTimeUtc       Method     System.Void set_CreationTimeUtc(DateTime value)
    set_LastAccessTime        Method     System.Void set_LastAccessTime(DateTime value)
    set_LastAccessTimeUtc     Method     System.Void set_LastAccessTimeUtc(DateTime value)
    set_LastWriteTime         Method     System.Void set_LastWriteTime(DateTime value)
    set_LastWriteTimeUtc      Method     System.Void set_LastWriteTimeUtc(DateTime value)
    ToString                  Method     System.String ToString()

You can apply methods just like you did in the previous examples. For example, you can use the _CreateSubDirectory_ method if you'd like to create a new sub-directory. First, you should find out which arguments this method requires and what it returns:

    $info = $object | Get-Member CreateSubDirectory
    $info.Definition.Replace("), ", ")`n")
    System.IO.DirectoryInfo CreateSubDirectory(String path)
    System.IO.DirectoryInfo CreateSubDirectory(String path, DirectorySecurity DirectorySecurity)

You can see that the method has two signatures. Try using the first to create a sub-directory and the second to add access permissions.

The next line creates a sub-directory called "My New Directory" without any special access privileges:

    $object.CreateSubDirectory("My New Directory")
    Mode                LastWriteTime     Length Name
    ----                -------------     ------ ----
    d----        01.10.2007     15:49           My New Directory

Because the method returns a _DirectoryInfo_ object as a result and you haven't caught and stored this object in a variable, the pipeline will convert it into text and output it. You could just as well have stored the result of the method in a variable:

    $subdirectory = $object.CreateSubDirectory("Another subdirectory")
    $subdirectory.CreationTime = "September 1, 1980"
    $subdirectory.CreationTime
    Monday, September 1, 1980 00:00:00

#### Different Method Types

Similarly to properties, PowerShell can also add additional methods to an object.

| ----- |
| MemberType |  Description |
| CodeMethod |  Method mapped to a static .NET method |
| Method |  Genuine method |
| ScriptMethod |  Method invokes PowerShell code |

**Table 6.4:** Different types of methods

## Using Static Methods

By now, you know that PowerShell stores information in objects, and objects always have a type. You know that simple text is stored in objects of type _System.String_ and that a date, for example, is stored in an object of type _System.DateTime_. You also know by now that each .NET object has a _GetType()_ method with a _Fullname_ property, which tells you the name of the type this object was derived from:

    $date = Get-Date
    $date.GetType().FullName
    System.DateTime

Every type can have its own set of private members called "static" members. You can simply specify a type in square brackets, pipe it to _Get-Member_, and then use the _-static_ parameter to see the static members of a type.

     [System.DateTime] | Get-Member -static -memberType *method
       TypeName: System.DateTime

    Name                  MemberType Definition
    ----                  ---------- ----------
    Compare               Method     static System.Int32 Compare(DateTime t1, DateTime t2)
    DaysInMonth           Method     static System.Int32 DaysInMonth(Int32 year, Int32 month)
    Equals                Method     static System.Boolean Equals(DateTime t1, DateTime t2), static Sys...
    FromBinary            Method     static System.DateTime FromBinary(Int64 dateData)
    FromFileTime          Method     static System.DateTime FromFileTime(Int64 fileTime)
    FromFileTimeUtc       Method     static System.DateTime FromFileTimeUtc(Int64 fileTime)
    FromOADate            Method     static System.DateTime FromOADate(Double d)
    get_Now               Method     static System.DateTime get_Now()
    get_Today             Method     static System.DateTime get_Today()
    get_UtcNow            Method     static System.DateTime get_UtcNow()
    IsLeapYear            Method     static System.Boolean IsLeapYear(Int32 year)
    op_Addition           Method     static System.DateTime op_Addition(DateTime d, TimeSpan t)
    op_Equality           Method     static System.Boolean op_Equality(DateTime d1, DateTime d2)
    op_GreaterThan        Method     static System.Boolean op_GreaterThan(DateTime t1, DateTime t2)
    op_GreaterThanOrEqual Method     static System.Boolean op_GreaterThanOrEqual(DateTime t1, DateTime t2)
    op_Inequality         Method     static System.Boolean op_Inequality(DateTime d1, DateTime d2)
    op_LessThan           Method     static System.Boolean op_LessThan(DateTime t1, DateTime t2)
    op_LessThanOrEqual    Method     static System.Boolean op_LessThanOrEqual(DateTime t1, DateTime t2)
    op_Subtraction        Method     static System.DateTime op_Subtraction(DateTime d, TimeSpan t), sta...
    Parse                 Method     static System.DateTime Parse(String s), static System.DateTime Par...
    ParseExact            Method     static System.DateTime ParseExact(String s, String format, IFormat...
    ReferenceEquals       Method     static System.Boolean ReferenceEquals(Object objA, Object objB)
    SpecifyKind           Method     static System.DateTime SpecifyKind(DateTime value, DateTimeKind kind)
    TryParse              Method     static System.Boolean TryParse(String s, DateTime& result), static...
    TryParseExact         Method     static System.Boolean TryParseExact(String s, String format, IForm...

There are a lot of method names starting with "op_," with "op" standing for "operator." These are methods that are called internally whenever you use this data type with an operator. _op_GreaterThanOrEqual_ is the method that does the internal work when you use the PowerShell comparison operator "_-ge_" with date values.

The _System.DateTime_ class supplies you with a bunch of important date and time methods. For example, you should use _Parse()_ to convert a date string into a real DateTime object and the current locale:

    [System.DateTime]::Parse("March 12, 1999")
    Friday, March 12, 1999 00:00:00

You could easily find out whether a certain year is a leap year:

    [System.DateTime]::isLeapYear(2010)
    False

    for ($x=2000; $x -lt 2010; $x++) { if( [System.DateTime]::isLeapYear($x) ) { "$x is a leap year!" } }
    2000 is a leap year!
    2004 is a leap year!
    2008 is a leap year!

Or you'd like to tell your children with absolute precision how much time will elapse before they get their Christmas gifts:

    [DateTime]"12/24/2007 18:00" - [DateTime]::now
    Days              : 74
    Hours             : 6
    Minutes           : 28
    Seconds           : 49
    Milliseconds      : 215
    Ticks             : 64169292156000
    TotalDays         : 74.2700140694444
    TotalHours        : 1782,48033766667
    TotalMinutes      : 106948,82026
    TotalSeconds      : 6416929,2156
    TotalMilliseconds : 6416929215,6

Two dates are being subtracted from each other here so you now know what happened during this operation:

* The first time indication is actually text. For it to become a _DateTime_ object, you must specify the desired object type in square brackets. _Important: Converting a String to a DateTime this way always uses the U.S. locale. To convert a String to a DateTime using your current locale, you can use the Parse() method as shown a couple of moments ago!_
* • The second time comes from the _Now_ static property, which returns the current time as _DateTime_ object. This is the same as calling the _Get-Date_ cmdlet (which you'd then need to put in parenthesis because you wouldn't want to subtract the _Get-Date_ cmdlet, but rather the result of the _Get-Date_ cmdlet).
* • The two timestamps are subtracted from each other using the subtraction operator ("-"). This was possible because the _DateTime_ class defined the _op_Subtraction()_ static method, which is needed for this operator.

Of course, you could have called the static method yourself and received the same result:

    [DateTime]::op_Subtraction("12/24/2007 18:00", [DateTime]::Now)

Now it's your turn. In the _System.Math_ class, you'll find a lot of useful mathematical methods. Try to put some of these methods to work.

| ----- |
| Function |  Description |  Example |
| Abs |  Returns the absolute value of a specified number (without signs). |  [Math]::Abs(-5) |
| Acos |  Returns the angle whose cosine is the specified number. |  [Math]::Acos(0.6) |
| Asin |  Returns the angle whose sine is the specified number. |  [Math]::Asin(0.6) |
| Atan |  Returns the angle whose tangent is the specified number. |  [Math]::Atan(90) |
| Atan2 |  Returns the angle whose tangent is the quotient of two specified numbers. |  [Math]::Atan2(90, 15) |
| BigMul |  Calculates the complete product of two 32-bit numbers. |  [Math]::BigMul(1gb, 6) |
| Ceiling |  Returns the smallest integer greater than or equal to the specified number. |  [Math]::Ceiling(5.7) |
| Cos |  Returns the cosine of the specified angle. |  [Math]::Cos(90) |
| Cosh |  Returns the hyperbolic cosine of the specified angle. |  [Math]::Cosh(90) |
| DivRem |  Calculates the quotient of two numbers and returns the remainder in an output parameter. |  $a = 0  
[Math]::DivRem(10,3,[ref]$a)  
$a |
| Exp |  Returns the specified power of e (2.7182818). |  [Math]::Exp(12) |
| Floor |  Returns the largest integer less than or equal to the specified number. |  [Math]::Floor(5.7) |
| IEEERemainder |  Returns the remainder of division of two specified numbers. |  [Math]::IEEERemainder(5,2) |
| Log |  Returns the natural logarithm of the specified number. |  [Math]::Log(1) |
| Log10 |  Returns the base 10 logarithm of the specified number. |  [Math]::Log10(6) |
| Max |  Returns the larger of two specified numbers. |  [Math]::Max(-5, 12) |
| Min |  Returns the smaller of two specified numbers. |  [Math]::Min(-5, 12) |
| Pow |  Returns a specified number raised to the specified power. |  [Math]::Pow(6,2) |
| Round |  Rounds a value to the nearest integer or to the specified number of decimal places. |  [Math]::Round(5.51) |
| Sign |  Returns a value indicating the sign of a number. |  [Math]::Sign(-12) |
| Sin |  Returns the sine of the specified angle. |  [Math]::Sin(90) |
| Sinh |  Returns the hyperbolic sine of the specified angle. |  [Math]::Sinh(90) |
| Sqrt |  Returns the square root of a specified number. |  [Math]::Sqrt(64) |
| Tan |  Returns the tangent of the specified angle. |  [Math]::Tan(45) |
| Tanh |  Returns the hyperbolic tangent of the specified angle. |  [Math]::Tanh(45) |
| Truncate |  Calculates the integral part of a number. |  [Math]::Truncate(5.67) |

**Table 6.5:** Mathematical functions from the [Math] library

### Finding Interesting .NET Types

The .NET framework consists of thousands of types, and maybe you are getting hungry for more. Are there other interesting types? There are actually plenty! Here are the three things you can do with .NET types:

#### Converting Object Types

For example, you can use System.Net.IPAddress to work with IP addresses. This is an example of a .NET type conversion where a string is converted into a _System.Net.IPAddress_ type:

    [system.Net.IPAddress]'127.0.0.1'

    IPAddressToString : 127.0.0.1
    Address           : 16777343
    AddressFamily     : InterNetwork
    ScopeId           :
    IsIPv6Multicast   : False
    IsIPv6LinkLocal   : False
    IsIPv6SiteLocal   : False

#### Using Static Type Members

Or you can use _System.Net.DNS_ to resolve host names. This is an example of accessing a static type method, such as _GetHostByAddress()_:

    [system.Net.Dns]::GetHostByAddress("127.0.0.1")

    HostName          Aliases           AddressList
    --------          -------           -----------
    PCNEU01           {}                {127.0.0.1}

#### Using Dynamic Object Instance Members

Or you can derive an instance of a type and use its dynamic members. For example, to download a file from the Internet, try this:

    # Download address of a file:
    $address = "http://www.powershell.com/downloads/powershellplus.zip"

    # Save the file to this location:
    $target = "$homepsplus.zip"

    # Carry out download:
    $object = New-Object Net.WebClient
    $object.DownloadFile($address, $target)
    "File was downloaded!"

## Creating New Objects

Most of the time, PowerShell cmdlets deliver objects. In addition, you can create new objects (instances) that are derived from a specific type. To get new instances, you can either convert an existing object to a new type or create a new instance using _New-Object_:

    $datetime = [System.DateTime] '1.1.2000'
    $datetime.GetType().Fullname
    System.DateTime
    $datetime = New-Object System.DateTime
    $datetime.GetType().Fullname
    System.DateTime
    $datetime = Get-Date
    $datetime.GetType().Fullname
    System.DateTime
    $datetime = [System.DateTime]::Parse('1.1.2000')
    $datetime.GetType().Fullname
    System.DateTime

### Creating New Objects with New-Object

You can create a .NET object with _New-Object,t_ which gives you full access to all type "constructors." These are invisible methods that create the new object. the type needs to have at least one constructor to create a new instance of a type. If it has none, you cannot create instances of this type.

The DateTime type has one constructor that takes no argument. If you create a new instance of a DateTime object, you will get back a date set to the very first date a DateTime type can represent, which happens to be January 1, 0001:

    New-Object System.DateTime
    Monday, January 01, 0001 12:00:00 AM

You can use a different constructor to create a specific date. There is one that takes three numbers for year, month, and day:

    New-Object System.DateTime(2000,5,1)
    Monday, May 01, 2000 12:00:00 AM

If you simply add a number, yet another constructor is used which interprets the number as ticks, the smallest time unit a computer can process:

    New-Object System.DateTime(568687676789080999)
    Monday, February 07, 1803 7:54:38 AM

#### Using Constructors

When you create a new object using New-Object, you can submit additional arguments by adding argument values as a comma separated list enclosed in parentheses. New-Object is in fact calling a method called ctor, which is the type constructor. Like any other method, it can support different argument signatures.

Let's check out how you can discover the different constructors, which a type will support. The next line creates a new instance of a System.String and uses a constructor that accepts a character and a number:

    New-Object System.String(".", 100)
    ....................................................................................................

To list the available constructors for a type, you can use the GetConstructors() method available in each type. For example, you can find out which constructors are offered by the _System.String_ type to produce _System.String_ objects:

    [System.String].GetConstructors() | ForEach-Object { $_.toString() }
    Void .ctor(Char*)
    Void .ctor(Char*, Int32, Int32)
    Void .ctor(SByte*)
    Void .ctor(SByte*, Int32, Int32)
    Void .ctor(SByte*, Int32, Int32, System.Text.Encoding)
    Void .ctor(Char[], Int32, Int32)
    Void .ctor(Char[])
    Void .ctor(Char, Int32)

In fact, there are eight different signatures to create a new object of the _System.String_ type. You just used the last variant: the first argument is the character, and the second a number that specifies how often the character will be repeated. PowerShell will use the next to last constructor so if you specify text in quotation marks, it will interpret text in quotation marks as a field with nothing but characters (_Char[]_).

### New Objects by Conversion

Objects can often be created without New-Object by using type casting instead. You've already seen how it's done for variables in [Chapter 3][2]:

    # PowerShell normally wraps text as a System.String:
    $date = "November 1, 2007"
    $date.GetType().FullName
    System.String
    $date
    November 1, 2007

    # Use strong typing to set the object type of $date:
    [System.DateTime]$date = "November 1, 2007"
    $date.GetType().FullName
    System.DateTime
    $date
    Thursday, November 1, 2007 00:00:00

So, if you enclose the desired .NET type in square brackets and put it in front of a variable name, PowerShell will require you to use precisely the specified object type for this variable. If you assign a value to the variable, PowerShell will automatically convert it to that type. That process is sometimes called "implicit type conversion." Explicit type conversion works a little different. Here, the desired type is put in square brackets again, but placed on the right side of the assignment operator:

    $value = [DateTime]"November 1, 2007"
    $value
    Thursday, November 1, 2007 00:00:00

PowerShell would first convert the text into a date because of the type specification and then assign it to the variable $value, which itself remains a regular variable without type specification. Because _$value_ is not limited to DateTime types, you can assign other data types to the variable later on.

    $value = "McGuffin"

Using the type casting, you can also create entirely new objects without _New-Object_. First, create an object using New-Object:

    New-Object system.diagnostics.eventlog("System")
      Max(K) Retain OverflowAction        Entries Name
      ------ ------ --------------        ------- ----
      20,480      0 OverwriteAsNeeded      64,230 System

You could have accomplished the same thing without _New-Object_:

    [System.Diagnostics.EventLog]"System"
      Max(K) Retain OverflowAction        Entries Name
      ------ ------ --------------        ------- ----
      20,480      0 OverwriteAsNeeded      64,230 System

In the second example, the string _System_ is converted into the _System.Diagnostics.Eventlog_ type: The result is an _EventLog_ object representing the _System_ event log.

So, when can you use New-Object and when type conversion? It is largely a matter of taste, but whenever a type has more than one constructor and you want to select the constructor, you should use New-Object and specify the arguments for the constructor of your choice. Type conversion will automatically choose one constructor, and you have no control over which constructor is picked.

    # Using New-Object, you can select the constructor you wish of the type yourself:
    New-Object System.String(".", 100)
    ....................................................................................................

    # When casting types, PowerShell selects the constructor automatically
    # For the System.String type, a constructor will be chosen that requires no arguments
    # Your arguments will then be interpreted as a PowerShell subexpression in which
    # a field will be created
    # PowerShell will change this field into a System.String type
    # PowerShell changes fields into text by separating elements from each other with whitespace:
    [system.string](".",100)
    . 100
    # If your arguments are not in round brackets, they will be interpreted as a Field
    # and the first field element # Cast in the System.String type:
    [system.string]".", 100
    .
    100

Type conversion can also include type arrays (identified by "[]") and can be a multi-step process where you convert from one type over another type to a final type. This is how you would convert string text into a character array:

    [char[]]"Hello!"
    H
    e
    l
    l
    o
    !

You could then convert each character into integers to get the character codes:

    [Int[]][Char[]]"Hello World!"
    72
    97
    108
    108
    111
    32
    87
    101
    108
    116
    33

Conversely, you could make a numeric list out of a numeric array and turn that into a string:

    [string][char[]](65..90)
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
    $OFS = ","
    [string][char[]](65..90)
    A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z

Just remember: if arrays are converted into a string, PowerShell uses the separator in the $ofs automatic variable as a separator between the array elements.

### Loading Additional Assemblies: Improved Internet Download

To get access to even more functionality, you can load additional assemblies with more types and members. If you have ever written VBScript scripts, you may want to get back some of your beloved VisualBasic methods, such as MsgBox() or InputBox(). Simply load the Microsoft.VisualBasic assembly, which is located in the global assembly cache:

    # Load required assembly:
    [void][reflection.assembly]::LoadWithPartialName("Microsoft.VisualBasic")

Once you do that, you have access to a whole bunch of new types:

    [Microsoft.VisualBasic.Interaction] | Get-Member -static

       TypeName: Microsoft.VisualBasic.Interaction

    Name            MemberType Definition
    ----            ---------- ----------
    AppActivate     Method     static System.Void AppActivate(Int32 Proces...
    Beep            Method     static System.Void Beep()
    CallByName      Method     static System.Object CallByName(Object Obje...
    Choose          Method     static System.Object Choose(Double Index, P...
    Command         Method     static System.String Command()
    CreateObject    Method     static System.Object CreateObject(String Pr...
    DeleteSetting   Method     static System.Void DeleteSetting(String App...
    Environ         Method     static System.String Environ(Int32 Expressi...
    Equals          Method     static System.Boolean Equals(Object objA, O...
    GetAllSettings  Method     static System.String[,] GetAllSettings(Stri...
    GetObject       Method     static System.Object GetObject(String PathN...
    GetSetting      Method     static System.String GetSetting(String AppN...
    IIf             Method     static System.Object IIf(Boolean Expression...
    InputBox        Method     static System.String InputBox(String Prompt...
    MsgBox          Method     static Microsoft.VisualBasic.MsgBoxResult M...
    Partition       Method     static System.String Partition(Int64 Number...
    ReferenceEquals Method     static System.Boolean ReferenceEquals(Objec...
    SaveSetting     Method     static System.Void SaveSetting(String AppNa...
    Shell           Method     static System.Int32 Shell(String PathName, ...
    switch          Method     static System.Object switch(Params Object[]...
    [microsoft.VisualBasic.Interaction]::InputBox("Enter Name", "Name", "$env:username")
    Tobias

Or, you can use a much-improved download method, which shows a progress bar while downloading files from the Internet:

    # Reload required assembly:
    [void][reflection.assembly]::LoadWithPartialName("Microsoft.VisualBasic")

    # Download address of a file:
    $address = "http://www.idera.com/powershellplus"

    # This is where the file should be saved:
    $target = "$homepsplus.zip"

    # Download will be carried out:
    $object = New-Object Microsoft.VisualBasic.Devices.Network
    $object.DownloadFile($address, $target, "", "", $true, 500, $true, "DoNothing")

### Using COM Objects

In addition to .NET, PowerShell can also load and access most COM objects, which work similar to .NET types and objects, but use an older technology.

#### Which COM Objects Are Available?

COM objects each have a unique name, known as _ProgID_ or _Programmatic Identifier_, which is stored in the registry. So, if you want to look up COM objects available on your computer, you can visit the registry:

    Dir REGISTRY::HKEY_CLASSES_ROOTCLSID -include PROGID -recurse | foreach {$_.GetValue("")}

#### How Do You Use COM Objects?

Once you know the _ProgID_ of a COM component, you can use _New-Object_ to put it to work in PowerShell. Just specify the additional parameter _-COMObject_:

    $object = New-Object -ComObject WScript.Shell

You'll get an object which behaves very similar to .NET objects. It will contain properties with data and methods that you can execute. And, as always, _Get-Member_ finds all object members for you. Let's look at its methods:

    # Make the methods of the COM objects visible:
    $object | Get-Member -memberType *method
       TypeName: System.__ComObject#{41904400-be18-11d3-a28b-00104bd35090}

    Name                     MemberType Definition
    ----                     ---------- ----------
    AppActivate              Method     bool AppActivate (Variant, Variant)
    CreateShortcut           Method     IDispatch CreateShortcut (string)
    Exec                     Method     IWshExec Exec (string)
    ExpandEnvironmentStrings Method     string ExpandEnvironmentStrings (string)
    LogEvent                 Method     bool LogEvent (Variant, string, string)
    Popup                    Method     int Popup (string, Variant, Variant, Variant)
    RegDelete                Method     void RegDelete (string)
    RegRead                  Method     Variant RegRead (string)
    RegWrite                 Method     void RegWrite (string, Variant, Variant)
    Run                      Method     int Run (string, Variant, Variant)
    SendKeys                 Method     void SendKeys (string, Variant)

The information required to understand how to use a method may be inadequate. Only the expected object types are given, but not why the arguments exist. The Internet can help you if you want to know more about a COM command. Go to a search site of your choice and enter two keywords: the _ProgID_ of the COM components (in this case, it will be _WScript.Shell_) and the name of the method that you want to use.

Some of the commonly used COM objects are WScript.Shell, WScript.Network, Scripting.FileSystemObject, InternetExplorer.Application, Word.Application, and Shell.Application. Let's create a shortcut to powershell.exe using WScript.Shell Com object and its method CreateShorcut():

    # Create an object:
    $wshell = New-Object -comObject WScript.Shell
    # Assign a path to Desktop to the variable $path
    $path = [system.Environment]::GetFolderPath('Desktop')

    # Create a link object $link = $wshell.CreateShortcut("$pathPowerShell.lnk")
    # $link is an object and has the properties and methods
    $link | Get-Member

       TypeName: System.__ComObject#{f935dc23-1cf0-11d0-adb9-00c04fd58a0b}

    Name             MemberType   Definition
    ----             ----------   ----------
    Load             Method       void Load (string)
    Save             Method       void Save ()
    Arguments        Property     string Arguments () {get} {set}
    Description      Property     string Description () {get} {set}
    FullName         Property     string FullName () {get}
    Hotkey           Property     string Hotkey () {get} {set}
    IconLocation     Property     string IconLocation () {get} {set}
    RelativePath     Property      {get} {set}
    TargetPath       Property     string TargetPath () {get} {set}
    WindowStyle      Property     int WindowStyle () {get} {set}
    WorkingDirectory Property     string WorkingDirectory () {get} {set}

    # We can populate some of the properties
    $link.TargetPath = 'powershell.exe'
    $link.Description = 'Launch Windows PowerShell console'   
    $link.WorkingDirectory = $profile   
    $link.IconLocation = 'powershell.exe'
    # And save the changes using Save() method
    $link.Save()

## Summary

Everything in PowerShell is represented by objects that have exactly two aspects: properties and methods, which both form the members of the object. While properties store data, methods are executable commands.

Objects are the result of all PowerShell commands and are not converted to readable text until you output the objects to the console. However, if you save a command's result in a variable, you will get a handle on the original objects and can evaluate their properties or call for their commands. If you would like to see all of an object's properties, then you can pass the object to Format-List and type an asterisk after it. This allows all—not only the most important—properties to be output as text.

The Get-Member cmdlet retrieves even more data, enabling you to output detailed information on the properties and methods of any object.

All the objects that you will work with in PowerShell originate from .NET framework, which PowerShell is layered. Aside from the objects that PowerShell commands provide to you as results, you can also invoke objects directly from the .NET framework and gain access to a powerful arsenal of new commands. Along with the dynamic methods furnished by objects, there are also static methods, which are provided directly by the class from which objects are also derived.

If you cannot perform a task with the cmdlets, regular console commands, or methods of the .NET framework, you can resort to the unmanaged world outside the .NET framework. You can directly access the low-level API functions, the foundation of the .NET framework, or use COM components.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-11-error-handling.aspx
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/02/chapter-3-variables.aspx
