
# Chapter 1. The PowerShell Console - Master-PowerShell | With Dr. Tobias Weltner

Welcome to PowerShell! This chapter will introduce you to the PowerShell console and show you how to configure it, including font colors and sizes, editing and display options.

**Topics Covered:**

## Starting PowerShell

On Windows 7 and Server 2008 R2, Windows PowerShell is installed by default. To use PowerShell on older systems, you need to download and install it. The update is free. The simplest way to find the appropriate download is to visit an Internet search engine and search for "KB968930 Windows XP" (replace the operating system with the one you use). Make sure you pick the correct update. It needs to match your operating system language and architecture (32-bit vs. 64-bit).

After you installed PowerShell, you'll find PowerShell in the Accessories program group. Open this program group, click on Windows PowerShell and then launch the PowerShell executable. On 64-bit systems, you will also find a version marked as (x86) so you can run PowerShell both in the default 64-bit environment and in an extra 32-bit environment for backwards compatibility.

You can also start PowerShell directly. Just press (Windows)+(R) to open the _Run_ window and then enter _powershell_ (Enter). If you use PowerShell often, you should open the program folder for _Windows PowerShell_ and right-click on _Windows PowerShell_. That will give you several options:

* **Add to the start menu:** On the context menu, click on _Pin to Start Menu_ so that PowerShell will be displayed directly on your start menu from now on and you won't need to open its program folder first.
* **Quick Launch toolbar:** Click _Add to Quick Launch toolbar_ if you use Windows Vista and would like to see PowerShell right on the Quick Launch toolbar inside your taskbar. Windows XP lacks this command so XP users will have to add PowerShell to the Quick Launch toolbar manually.
* **Jump List:** On Windows 7, after launching PowerShell, you can right-click the PowerShell icon in your taskbar and choose _Pin to Taskbar_. This will not only keep the PowerShell icon in your taskbar so you can later easily launch PowerShell. It also gives access to its new "Jump List": right-click the icon (or pull it upwards with your mouse). The jump list contains a number of useful PowerShell functions: you can launch PowerShell with full administrator privileges, run the PowerShell ISE, or open the PowerShell help file. By the way: drag the pinned icon all to the left in your taskbar. Now, pressing WIN+1 will always launch PowerShell. And here are two more tips: hold SHIFT while clicking the PowerShell icon in your taskbar will open a new instance, so you can open more than one PowerShell console. Holding SHIFT+CTRL while clicking the PowerShell icon opens the PowerShell console with full Administrator privileges (provided _User Account Control_ is enabled on your system).
* **Keyboard shortcuts:** Administrators particularly prefer using a keyboard instead of a mouse. If you select _Properties_ on the context menu, you can specify a key combination in the _hot-key_ field. Just click on this field and press the key combination intended to start PowerShell, such as (Alt)+(P). In the properties window, you also have the option of setting the default window size to start PowerShell in a normal, minimized, or maximized window.

![][1]

**Figure 1.1:** How to always open PowerShell with administrator rights

_(Run without administrative privileges whenever possible)_

## First Steps with the Console

After PowerShell starts, its console window opens, and you see a blinking text prompt, asking for your input with no icons or menus. PowerShell is a command console and almost entirely operated via keyboard input. The prompt begins with "PS" and after it is the path name of the directory where you are located. Start by trying out a few commands. For example, type:

hello (Enter)

As soon as you press (Enter), your entry will be sent to PowerShell. Because PowerShell has never heard of the command "hello" you will be confronted with an error message highlighted in red.

![][2]

**Figure 1.2:** First commands in the PowerShell console

For example, if you'd like to see which files and folders are in your current directory, then type _dir_ (Enter). You'll get a text listing of all the files in the directory. PowerShell's communication with you is always text-based. PowerShell can do much more than display simple directory lists. You can just as easily list all running processes or all installed hotfixes: Just pick a different command as the next one provides a list of all running processes:

_Get-Process_ (Enter)

_Get-Hotfix_ (Enter)

PowerShell's advantage is its tremendous flexibility since it allows you to control and display nearly all the information and operations on your computer. The command _cls_ deletes the contents of the console window and the _exit_ command ends PowerShell.

### Incomplete and Multi-line Entries

Whenever you enter something PowerShell cannot understand, you get a red error message, explaining what went wrong. However, if you enter something that isn't wrong but incomplete (like a string with one missing closing quote), PowerShell gives you a chance to complete your input. You then see a double-prompt (">>"), and once you completed the line and pressed ENTER twice, PowerShell executes the command. You can also bail out at any time and cancel the current command or input by pressing: (Ctrl)+(C).

The "incomplete input" prompt will also appear when you enter an incomplete arithmetic problem like this one:

2 + (Enter)  
>> 6 (Enter)  
>> (Enter)

8

This feature enables you to make multi-line PowerShell entries:

"This is my little multiline entry.(Enter)  
>> I'm now writing a text of several lines. (Enter)  
>> And I'll keep on writing until it's no longer fun."(Enter)  
>>(Enter)

This is my little multiline entry.  
I'm now writing a text of several lines.  
And I'll keep on writing until it's no longer fun.

The continuation prompt generally takes its cue from initial and terminal characters like open and closed brackets or quotation marks at both ends of a string. As long as the symmetry of these characters is incorrect, you'll continue to see the prompt. However, you can activate it even in other cases:

dir `(Enter)  
>> -recurse(Enter)  
>>(Enter)

So, if the last character of a line is what is called a "back-tick" character, the line will be continued. You can retrieve that special character by pressing (`).

### Important Keyboard Shortcuts

Shortcuts are important since almost everything in PowerShell is keyboard-based. For example, by pressing the keys (Arrow left) and (Arrow right), you can move the blinking cursor to the left or right. Use it to go back and correct a typo. If you want to move the cursor word by word, hold down (Ctrl) while pressing the arrow keys. To place the cursor at the beginning of a line, hit (Home). Pressing (End) will send the cursor to the end of a line.

If you haven't entered anything, then the cursor won't move since it will only move within entered text. There's one exception: if you've already entered a line and pressed (Enter) to execute the line, you can make this line appear again character-by-character by pressing (Arrow right).

### Deleting Incorrect Entries

If you've mistyped something, press (Backspace) to delete the character to the left of the blinking cursor. (Del) erases the character to the right of the cursor. And you can use (Esc) to delete your entire current line.

The hotkey (Ctrl)+(Home) works more selectively: it deletes all the characters at the current position up to the beginning of the line. Characters to the right of the current position (if there are any) remain intact. (Ctrl)+(End) does it the other way around and deletes everything from the current position up to the end of the line. Both combinations are useful only after you've pressed (Arrow left) to move the cursor to the middle of a line, specifically when text is both to the left and to the right of the cursor.

### Overtype Mode

If you enter new characters and they overwrite existing characters, then you know you are in type-over mode. By pressing (Insert) you can switch between insert and type-over modes. The default input mode depends on the console settings you select. You'll learn more about console settings soon.

### Command History: Reusing Entered Commands

The most awesome feature is a built-in search through all of the commands you used in your current session: simply type "#" and then some search text that you know exists in one or more of your previous commands. Next, type TAB one or more times to see all the commands that contained your keyword. Press ENTER to execute the command once you found it, or edit the command line to your liking.

If you just wanted to polish or correct one of your most recent commands, press (Arrow up) to re-display the command that you entered. Press (Arrow up) and (Arrow down) to scroll up and down your command history. Using (F5) and (F8) do the same as the up and down arrow keys.

This command history feature is extremely useful. Later, you'll learn how to configure the number of commands the console "remembers". The default setting is the last 50 commands. You can display all the commands in your history by pressing (F7) and then scrolling up and down the list to select commands using (Arrow up) and (Arrow down) and (Enter).

The numbers before the commands in the Command History list only denote the sequence number. You cannot enter a number to select the associated command. What you can do is move up and down the list by hitting the arrow keys.

Simply press (F9) to 'activate' the numbers so that you can select a command by its number. This opens a menu that accepts the numbers and returns the desired command.

The keyboard sequence (Alt)+(F7) will clear the command history and start you off with a new list.

(F8) provides more functionality than (Arrow up) as it doesn't just show the last command you entered, but keeps a record of the characters you've already typed in. If, for example, you'd like to see all the commands you've entered that begin with "d", type:

d(F8)

Press (F8) several times. Every time you press a key another command will be displayed from the command history provided that you've already typed in commands with an initial "d."

### Automatically Completing Input

An especially important key is (Tab). It will save you a great deal of typing (and typing errors). When you press this key, PowerShell will attempt to complete your input automatically. For example, type:

cd (Tab)

The command _cd_ changes the directory in which you are currently working. Put at least one space behind the command and then press (Tab). PowerShell suggests a sub-directory. Press (Tab) again to see other suggestions. If (Tab) doesn't come up with any suggestions, then there probably aren't any sub-directories available.

This feature is called Tab-completion, which works in many places. For example, you just learned how to use the command _Get-Process_, which lists all running processes. If you want to know what other commands there are that begin with "Get-", then type:

Get-(Tab)

Just make sure that there's no space before the cursor when you press (Tab). Keep hitting (Tab) to see all the commands that begin with "Get-".

A more complete review of the Tab-completion feature is available in [Chapter 9][3].

Tab-completion works really well with long path names that require a lot of typing. For example:

c:p(Tab)

Every time you press (Tab), PowerShell will prompt you with a new directory or a new file that begins with "c:p." So, the more characters you type, the fewer options there will be. In practice, you should type in at least four or five characters to reduce the number of suggestions.

When the list of suggestions is long, it can take a second or two until PowerShell has compiled all the possible suggestions and displays the first one.

Wildcards are allowed in path names. For example, if you enter _c:pr*e_ (Tab) in a typical Windows system, PowerShell will respond with "c:Program Files".

PowerShell will automatically put the entire response inside double quotation marks if the response contains whitespace characters.

### Scrolling Console Contents

The visible part of your console depends on the size of your console window, which you can change with your mouse. Drag the window border while holding down your left mouse button until the window is the size you want. Note that the actual contents of the console, the "screen buffer," don't change. So, if the window is too small to show everything, you should use the scroll bars.

### Selecting and Inserting Text

Use your mouse if you'd like to select text inside the PowerShell window and copy it onto the clipboard. Move the mouse pointer to the beginning of the selected text, hold down the left mouse button and drag it over the text area that you want to select.

### QuickEdit Mode

QuickEdit is the default mode for selecting and copying text in PowerShell. Select the text using your mouse and PowerShell will highlight it. After you've selected the text, press (Enter) or right-click on the marked area. This will copy the selected text to the clipboard which you can now paste into other applications. To unselect press (Esc).

You can also insert the text in your console at the blinking command line by right-clicking your mouse.

![][4]

**Figure 1.3:** Marking and copying text areas in QuickEdit mode

### Standard Mode

If QuickEdit is turned off and you are in Standard mode, the simplest way to mark and copy text is to right-click in the console window. If QuickEdit is turned off, a context menu will open.

Select _Mark_ to mark text and Paste if you want to insert the marked text (or other text contents that you've copied to the clipboard) in the console.

It's usually more practical to activate QuickEdit mode so that you won't have to use the context menu.

## Customizing the Console

You can customize a variety of settings in the console including edit mode, screen buffer size, font colors, font sizes etc.

### Opening Console Properties

The basic settings of your PowerShell console are configured in a special _Properties_ dialog box. Click on the PowerShell icon on the far left of the title bar of the console window to open it.

![][5]

**Figure 1.4:** Opening console properties

That will open a context menu. You should select Properties and a dialog box will open.

To get help, click on the question mark button on the title bar of the window. A question mark is then pinned to your mouse pointer. Next, click on the option you need help for. The help appears as a ScreenTip window.

### Defining Options

Under the heading _Options_ are four panels of options:

![][6]

**Figure 1.5:** Defining the QuickEdit and Insert modes
* **Edit options**: You should select the QuickEdit mode as well as the Insert mode. We've already discussed the advantages of the _QuickEdit__ mode_: it makes it much easier to select, copy, and insert text. The _Insert mode_ makes sure that new characters don't overwrite existing input so new characters will be added without erasing text you've already typed in when you're editing command lines.
* **Cursor size**: Here is where you specify the size of the blinking cursor.
* **Display options**: Determine whether the console should be displayed as a window or full screen. The "window" option is best so that you can switch to other windows when you're working. The full screen display option is not available on all operating systems.
* **Command history**: Here you can choose how many command inputs the console "remembers". This allows you to select a command from the list by pressing (Arrow up) or (F7). The option _Discard Old Duplicates_ ensures that the list doesn't have any duplicate entries. So, if you enter one command twice, it will appear only once in the history list.

### Specifying Fonts and Font Sizes

On the _Font_ tab, you can choose both the font and the font size displayed in the console.

The console often uses the raster font as its default. This font is available in a specific range of sizes with available sizes shown in the "Size" list. Scalable TrueType fonts are much more flexible. They're marked in the list by a "TT" symbol. When you select a TrueType font, you can choose any size in the size list or enter them as text in the text box. TrueType fonts can be dynamically scaled.

![][7]

**Figure 1.6:** Specifying new fonts and font sizes

You should also try experimenting with TrueType fonts by using the "bold fonts" option. TrueType fonts are often more readable if they're displayed in bold.

Your choice of fonts may at first seem a bit limited. To get more font choices, you can add them to the console font list. The limited default font list is supposed to prevent you from choosing unsuitable fonts for your console.

One reason for this is that the console always uses the same width for each character (fixed width fonts). This restricts the use of most Windows fonts because they're proportional typefaces: every character has its own width. For example, an "i" is narrower than an "m". If you're sure that a certain font will work in the console, then here's how to add the font to the console font list.

Open your registry editor. In the key _HKEY_LOCAL_MACHINESOFTWAREMicrosoftWindows NT CurrentVersionConsoleTrueTypeFont_ insert a new "string value" and give this entry the name "00" (numbers, not letters).

If there's already an entry that has this name, then call the new entry "000" or add as many zeroes as required to avoid conflicts with existing entries. You should then double-click your new entry to open it and enter the name of the font. The name must be exactly the same as the official font name, just the way it's stated under the key _HKEY_LOCAL_MACHINESOFTWAREMicrosoftWindows NTCurrentVersionFonts_.

The newly added font will now turn up in the console's option field. However, the new font will work only after you either log off at least once or restart your computer. If you fail to do so, the console will ignore your new font when you select it in the dialog box.

### Setting Window and Buffer Size

On the _Layout_ tab, you can specify how large the screen buffer should be, meaning how much information the console should "remember" and how far back you can scroll with the scroll bars.

You should select a width of at least 120 characters in the window buffer size area with the height should be at least 1,000 lines or larger. This gives you the opportunity to use the scroll bars to scroll the window contents back up so that you can look at all the results of your previous commands.

![][8]

**Figure 1.7:** Specifying the size of the window buffer

You can also set the window size and position on this tab if you'd like your console to open at a certain size and screen position on your display. Choose the option _Let system position window_ and Windows will automatically determine at what location the console window will open.

### Selecting Colors

On the _Colors_ tab, you can select your own colors for four areas:

* **Screen text****:** Console font
* **Screen background****:** Console background color
* **Popup text****:** Popup window font, such as command history's (F7)
* **Popup background****:** Popup window background color

You have a palette of 16 colors for these four areas. So, if you want to specify a new font color, you should first select the option _Screen Text_ and click on one of the 16 colors. If you don't like any of the 16 colors, then you can mix your own special shade of color. Just click on a palette color and choose your desired color value at the upper right from the primary colors red, green, and blue.

![][9]

**Figure 1.8:** Select better colors for your console

### Directly Assigning Modifications in PowerShell

Some of the console configuration can also be done from within PowerShell code. You'll hear more about this later. To give you a quick impression, take a look at this:

$host.ui.rawui (Enter)  
$host.ui.rawui.ForegroundColor = "Yellow" (Enter)  
$host.ui.rawui.WindowTitle = "My Console" (Enter)

These changes will only be temporary. Once you close and re-open PowerShell, the changes are gone. You would have to include these lines into one of your "profile scripts" which run every time you launch PowerShell to make them permanent. You can read more about this in [Chapter 10][10].

### Saving Changes

Once you've successfully specified all your settings in the dialog box, you can close the dialog box. If you're using Windows Vista or above, all changes will be saved immediately, and when you start PowerShell the next time, your new settings will already be in effect. You may need Admin rights to save settings if you launched PowerShell with a link in your start menu that applies for all users.

If you're using Windows XP, you'll see an additional window and a message asking you whether you want to save changes temporarily (Apply properties to current window only) or permanently (Modify shortcut that started this window).

## Piping and Routing

You may want to view the information page by page or save it in a file since some commands output a lot of information.

### Piping: Outputting Information Page by Page

The pipe command _more_ outputs information screen page by screen page. You will need to press a button (like Space) to continue to the next page.

Piping uses the vertical bar (|). The results of the command to the left of the pipe symbol are then fed into the command on the right side of the pipe symbol. This kind of piping is also known in PowerShell as the "pipeline":

Get-Process | more (Enter)

You can press (Ctrl)+(C) to stop output. Piping also works with other commands, not just _more_. For example, if you'd like to get a sorted directory listing, pipe the result to Sort-Object and specify the columns you would like to sort:

dir | Sort-Object -Property Length, Name (Enter)

You'll find more background information on piping as well as many useful examples in [Chapter 5][11].

### Redirecting: Storing Information in Files

If you'd like to redirect the result of a command to a file, you can use the redirection symbol ">":

Help > help.txt (Enter)

The information won't appear in the console but will instead be redirected to the specified file. You can then open the file.

However, opening a file in PowerShell is different from opening a file in the classic console:

help.txt (Enter)

The term "help.txt" is not recognized as a cmdlet, function,   
operable program, or script file. Verify the term and try again.  
At line:1 character:8  
\+ help.txt <<<<

If you only specify the file name, PowerShell will look for it in all folders listed in the PATH environment variable. So to open a file, you will have to specify its absolute or relative path name. For example:

.help.txt (Enter)

Or, to make it even simpler, you can use Tab-completion and hit (Tab) after the file name:

help.txt(Tab)

The file name will automatically be completed with the absolute path name, and then you can open it by pressing (Enter):

& "C:UsersUserAhelp.txt" (Enter)

You can also append data to an existing file. For example, if you'd like to supplement the help information in the file with help on native commands, you can attach this information to the existing file with the redirection symbol ">>":

Cmd /c help >> help.txt (Enter)

If you'd like to directly process the result of a command, you won't need traditional redirection at all because PowerShell can also store the result of any command to a variable:

$result = Ping 10.10.10.10  
$result

Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Reply from 10.10.10.10: bytes=32 time<1ms TTL=128  
Ping statistics for 10.10.10.10:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms

Variables are universal data storage and variable names always start with a "$". You'll find out more about variables in [Chapter 3][12].

## Summary

PowerShell is part of the operating system starting with Windows 7 and Server 2008 R2. On older operating systems such as Windows XP or Server 2003, it is an optional component. You will have to download and install PowerShell before using it.

The current version is 2.0, and the easiest way to find out whether you are using the most current PowerShell version is to launch the console and check the copyright statement. If it reads "2006", then you are still using the old and outdated PowerShell 1.0. If it reads "2009", you are using the correct version. There is no reason why you should continue to use PowerShell 1.0, so if you find it on your system, update to 2.0 as soon as possible. If you wanted to find out your current PowerShell version programmatically, output the automatic variable $psversiontable (simply by entering it). It not only tells you the current PowerShell version but also the versions of the core dependencies. This variable was introduced in PowerShell version 2.0, so on version 1.0 it does not exist.

The PowerShell console resembles the interactive part of PowerShell where you can enter commands and immediately get back results. The console relies heavily on text input. There are plenty of special keys listed in **Table 1.1.**

| ----- |
| **Key** |  **Meaning** |
| (Alt)+(F7) |  Deletes the current command history |
| (PgUp), (PgDn) |  Display the first (PgUp) or last (PgDn) command you used in current session |
| (Enter) |  Send the entered lines to PowerShell for execution |
| (End) |  Moves the editing cursor to the end of the command line |
| (Del) |  Deletes the character to the right of the insertion point |
| (Esc) |  Deletes current command line |
| (F2) |  Moves in current command line to the next character corresponding to specified character |
| (F4) |  Deletes all characters to the right of the insertion point up to specified character |
| (F7) |  Displays last entered commands in a dialog box |
| (F8) |  Displays commands from command history beginning with the character that you already entered in the command line |
| (F9) |  Opens a dialog box in which you can enter the number of a command from your command history to return the command. (F7) displays numbers of commands in command history |
| (Left arrow), (Right arrow) |  Move one character to the left or right respectively |
| (Arrow up), (Arrow down), (F5), (F8) |  Repeat the last previously entered command |
| (Home) |  Moves editing cursor to beginning of command line |
| (Backspace) |  Deletes character to the left of the insertion point |
| (Ctrl)+(C) |  Cancels command execution |
| (Ctrl)+(End) |  Deletes all characters from current position to end of command line |
| (Ctrl)+(Arrow left), (Ctrl)+(Arrow right) |  Move insertion point one word to the left or right respectively |
| (Ctrl)+(Home) |  Deletes all characters of current position up to beginning of command line |
| (Tab) |  Automatically completes current entry, if possible |

**Table 1.1:** Important keys and their meaning in the PowerShell console

You will find that the keys (Arrow up), which repeats the last command, and (Tab), which completes the current entry, are particularly useful. By hitting (Enter), you complete an entry and send it to PowerShell. If PowerShell can't understand a command, an error message appears highlighted in red stating the possible reasons for the error. Two special commands are _cls_ (deletes the contents of the console) and _exit_ (ends PowerShell).

You can use your mouse to select information in the console and copy it to the Clipboard by pressing (Enter) or by right-clicking when you have the QuickEdit mode turned on. With QuickEdit mode turned off, you will have to right-click inside the console and then select _Mark_ in a context menu.

The basic settings of the console—QuickEdit mode as well as colors, fonts, and font sizes—can be customized in the properties window of the console. This can be accessed by right-clicking the icon to the far left in the title bar of the console window. In the dialog box, select _Properties_.

Along with the commands, a number of characters in the console have special meanings and you have already become acquainted with three of them:

* **Piping:** The vertical bar "|" symbol pipes the results of a command to the next. When you pipe the results to the command more, the screen output will be paused once the screen is full, and continued when you press a key.
* **Redirection:** The symbol ">" redirects the results of a command to a file. You can then open and view the file contents. The symbol ">>" appends information to an existing file.

PowerShell 2.0 also comes with a simple script editing tool called "ISE" (Integrated Script Environment). You find it in PowerShell's jump list (if you are using Windows 7), and you can also launch it directly from PowerShell by entering ise ENTER. ISE requires .NET Framework 3.5.1. On Windows Server 2008 R2, it is an optional feature that needs to be enabled first in your system control panel. You can do that from PowerShell as well:

Import-Module ServerManager Add-WindowsFeature ISE -IncludeAll

* * *

[1]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_1.png
[2]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_2.png
[3]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-9-functions.aspx
[4]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_3.png
[5]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_4.png
[6]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_5.png
[7]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_6.png
[8]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_7.png
[9]: http://powershell.com/cs/cfs-file.ashx/__key/CommunityServer.Blogs.Components.WeblogFiles/ebook.images/Ch1_2D00_8.png
[10]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-10-scripts.aspx
[11]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/05/chapter-5-the-powershell-pipeline.aspx
[12]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/02/chapter-3-variables.aspx
