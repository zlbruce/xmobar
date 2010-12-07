% xmobar - A Minimalistic Text Based Status Bar
% Andrea Rossato, Jose A. Ortega Ruiz

About
=====

xmobar is a minimalistic, text based, status bar. It was originally
designed and implemented by Andrea Rossato to work with [xmonad],
but it's actually window-manager-agnostic.

xmobar was inspired by the [Ion3] status bar, and supports similar
features, like dynamic color management, output templates, and
extensibility through plugins.

[This is a screen shot] of Andrea's desktop with [xmonad] and xmobar.

[xmonad]: http://xmonad.org
[Ion3]: http://tuomov.iki.fi/software/
[This is a screen shot]: http://haskell.org/sitewiki/images/a/ae/Arossato-config.png

Installation
============

## Using cabal-install

Xmobar is available from [Hackage], and you can install it using
`cabal-install`:

        cabal install xmobar

See below for a list of optional compilation flags that will enable
some optional plugins. For instance, to install xmobar with all the
bells and whistles, use:

        cabal install xmobar --flags="all_extensions"

## From source

If you don't have `cabal-install` installed, you can get its source
from [Github]:

        git clone git://github.com/jaor/xmobar

To install simply run:

        tar xvfz xmobar-0.9
        cd xmobar-0.9

Then run the configure script:

        runhaskell Setup.lhs configure

        # To enable UTF-8 support run:
        runhaskell Setup.lhs configure --flags="with_utf8"

        # To enable both XFT and UTF-8 support run:
        runhaskell Setup.lhs configure --flags="with_xft"

Now you can build the source:

        runhaskell Setup.lhs build
        runhaskell Setup.lhs install # possibly to be run as root


## Optional features

You can configure xmobar to include some optional plugins and
features, which are not compiled by default. To that end, you need to
add one or more flags to either the cabal install command or the
configure setup step, as shown in the examples above.

Extensions need additional libraries (listed below) that will be
automatically downloaded and installed if you're using cabal install.
Otherwise, you'll need to install them yourself.

`with_utf8`
:    UTF-8 support. Requires the [utf8-string] package.

`with_xft`
:    Antialiased fonts. Requires the [X11-xft] package. This option
     automatically enables UTF-8.

     To use XFT fonts you need to use the `xft:` prefix in the `font`
     configuration option. For instance:

        font = "xft:Times New Roman-10:italic"

`with_mpd`
:    Enables support for the [MPD] daemon. Requires the [libmpd] package.

`with_inotify`
:    Support for inotify in modern linux kernels. This option is needed
     for the MBox and Mail plugins to work.

`with_iwlib`
:    Support for wireless cards. Enables the Wireless plugin. No Haskell
     library is required, but you will need the [iwlib] C library and
     headers in your system (e.g., install `libiw-dev` in Debian-based
     systems).

`all_extensions`
:    Enables all the extensions above.

Running xmobar
==============

You can now run xmobar with:

        xmobar /path/to/config &

or

        xmobar &

if you have the default configuration file saved as `~/.xmobarrc`

Configuration
=============

## Quick Start

See [xmobar.config-sample] for an example.

For the output template:

- `%command%` will execute command and print the output. The output
  may contain markups to change the characters' color.

- `<fc=#FF0000>string</fc>` will print `string` with `#FF0000` color
  (red).

Other configuration options:

`font`
:    Name of the font to be used. Use the `xft:` prefix for XFT fonts.

`bgColor`
:    Background color.

`fgColor`
:    Default font color.

`position`
:     Top, TopW, TopSize, Bottom, BottomW, BottomSize or Static (with x, y,
      width and height).

:     TopW and BottomW take 2 arguments: an alignment parameter (L for
      left, C for centered, R for Right) and an integer for the
      percentage width xmobar window will have in respect to the
      screen width.

:     TopSize and BottomSize take 3 arguments: an alignment parameter, an
      integer for the percentage width, and an integer for the minimum pixel
      height that the xmobar window will have.

:     For example:

:          position = BottomW C 75

:     to place xmobar at the bottom, centered with the 75% of the screen width.

:     Or

:          position = Static { xpos = 0 , ypos = 0, width = 1024, height = 15 }

:     or

:         position = Top

`border`
:     TopB, TopBM, BottomB, BottomBM, FullB, FullBM or NoBorder (default).

:     TopB, BottomB, FullB take no arguments, and request drawing a
      border at the top, bottom or around xmobar's window,
      respectively.

:     TopBM, BottomBM, FullBM take an integer argument, which is the
      margin, in pixels, between the border of the window and the
      drawn border.

`borderColor`
:     Border color.

`commands`
:    For setting the options of the programs to run (optional).

`sepChar`
:    The character to be used for indicating commands in the output
     template (default '%').

`alignSep`
:    a 2 character string for aligning text in the output template. The
     text before the first character will be align to left, the text in
     between the 2 characters will be centered, and the text after the
     second character will be align to the right.

`template`
:    The output template.

## Running xmobar with i3status

xmobar can be used to display information gathered by [i3status], a
small program that gathers information and formats it suitable for
being displayed by the dzen2 status bar, wmii's status bar or xmobar's
StdinReader.

Since xmobar support has been added only recently you need to get the
git repository, and build it with the appropriate flags:

    git clone git://code.stapelberg.de/i3status

and then build it:

    cd i3status
    make EXTRA_CFLAGS="-DXMOBAR

Then you can run it with:

    ./i3status -c i3status.conf | xmobar -o -t "%StdinReader%" -c "[Run StdinReader]"

## Command Line Options

xmobar can be either configured with a configuration file or with
command line options. In the second case, the command line options
will overwrite the corresponding options set in the configuration
file.

Example:

    xmobar -B white -a right -F blue -t '%LIPB%' -c '[Run Weather "LIPB" [] 36000]'

This is the list of command line options (the output of
xmobar --help):

    Usage: xmobar [OPTION...] [FILE]
    Options:
      -h, -?        --help               This help
      -V            --version            Show version information
      -f font name  --font=font name     The font name
      -B bg color   --bgcolor=bg color   The background color. Default black
      -F fg color   --fgcolor=fg color   The foreground color. Default grey
      -o            --top                Place xmobar at the top of the screen
      -b            --bottom             Place xmobar at the bottom of the screen
      -a alignsep   --alignsep=alignsep  Separators for left, center and right text
                                         alignment. Default: '}{'
      -s char       --sepchar=char       The character used to separate commands in
                                         the output template. Default '%'
      -t template   --template=template  The output template
      -c commands   --commands=commands  The list of commands to be executed
	  -x screen     --screen=screen      On which X screen number to start
    Mail bug reports and suggestions to <andrea.rossato@unibz.it>

## The Output Template

The output template must contain at least one command. xmobar will
parse the template and will search for the command to be executed in
the `commands` configuration option. First an `alias` will be searched
(plugins such as Weather or Network have default aliases, see below).
After that, the command name will be tried. If a command is found, the
arguments specified in the `commands` list will be used.

If no command is found in the `commands` list, xmobar will ask the
operating system to execute a program with the name found in the
template. If the execution is not successful an error will be
reported.

## The `commands` Configuration Option

The `commands` configuration option is a list of commands information
and arguments to be used by xmobar when parsing the output template.
Each member of the list consists in a command prefixed by the `Run`
keyword. Each command has arguments to control the way xmobar is going
to execute it.

The option consists in a list of commands separated by a comma and
enclosed by square parenthesis.

Example:

    [Run Memory ["-t","Mem: <usedratio>%"] 10, Run Swap [] 10]

to run the Memory monitor plugin with the specified template, and the
swap monitor plugin, with default options, every second.

The only internal available command is `Com` (see below Executing
External Commands). All other commands are provided by plugins.
xmobar comes with some plugins, providing a set of system monitors,
a standard input reader, an Unix named pipe reader, and a configurable
date plugin. These plugins install the following internal commands:
`Weather`, `Network`, `Memory`, `Swap`, `Cpu`, `MultiCpu`, `Battery`,
`Thermal`, `CpuFreq`, `CoreTemp`, `Date`, `StdinReader`,
`CommandReader`, and `PipeReader`.

To remove them see below Installing/Removing a Plugin

Other commands can be created as plugins with the Plugin
infrastructure. See below Writing a Plugin

## System Monitor Plugins

This is the description of the system monitor plugins that are
installed by default.

Each monitor has an `alias` to be used in the output template.
Monitors have default aliases.

`Weather StationID Args RefreshRate`

- aliases to the Station ID: so `Weather "LIPB" []` can be used in template as `%LIPB%`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `station`, `stationState`, `year`, `month`, `day`, `hour`,
	    `wind`, `visibility`, `skyCondition`, `tempC`, `tempF`,
	    `dewPoint`, `rh`, `pressure`
- Default template: `<station>: <tempC>C, rh <rh>% (<hour>)`
- Requires `curl` in the `$PATH` to retrieve weather information from
  `http://weather.noaa.gov`

`Network Interface Args RefreshRate`

- aliases to the interface name: so `Network "eth0" []` can be used as `%eth0%`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `dev`, `rx`, `tx`, `rxbar`, `txbar`
- Default template: `<dev>: <rx>|<tx>`

`Wireless Interface Args RefreshRate`

- aliases to the interface name with the suffix "wi": thus, `Wirelss
  "wlan0" []` can be used as `%wlan0wi%`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
            `essid`, `quality`, `qualitybar`
- Default template: `<essid> <quality>`
- Requires the C library libiw (part of the wireless tools suite)
  installed in your system. In addition, to activate this plugin you
  must pass --flags="with_iwlib" to "runhaskell Setup configure"
  or to "cabal install".

`Memory Args RefreshRate`

- aliases to `memory`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
             `total`, `free`, `buffer`, `cache`, `rest`, `used`,
             `usedratio`, `usedbar`, `freebar`
- Default template: `Mem: <usedratio>% (<cache>M)`

`Swap Args RefreshRate`

- aliases to `swap`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `total`, `used`, `free`, `usedratio`
- Default template: `Swap: <usedratio>%`

`Cpu Args RefreshRate`

- aliases to `cpu`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `total`, `bar`, `user`, `nice`, `system`, `idle`
- Default template: `Cpu: <total>`

`MultiCpu Args RefreshRate`

- aliases to `multicpu`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `total`, `bar`, `user`, `nice`, `system`, `idle`,
	    `total0`, `bar0`, `user0`, `nice0`, `system0`, `idle0`, ...
- Default template: `Cpu: <total>`

`Battery Args RefreshRate`

- aliases to `battery`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `left`, `leftbar`, `status`
- Default template: `Batt: <left>`

`BatteryP Dirs Args RefreshRate`

- aliases to `battery`
- Dirs: list of directories in /proc/acpi/battery/ directory where to
  look for the `state` and `info` files. Example:
  `["BAT0","BAT1","BAT2"]`. Only the first 3 directories will be
  searched.
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `left`, `leftbar`, `status`
- Default template: `Batt: <left>`

`TopProc Args RefreshRate`

- aliases to `top`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `no`, `name1`, `cpu1`, `both1`, `mname1`, `mem1`, `mboth1`,
            `name2`, `cpu2`, `both2`, `mname2`, `mem2`, `mboth2`, ...
- Default template: `<both1>`
- Displays the name and cpu/mem usage of running processes (`bothn`
  and `mboth` display both, and is useful to specify an overall
  maximum and/or minimum width, using the `-m`/`-M` arguments. `no` gives
  the total number of processes.

`TopMem Args RefreshRate`

- aliases to `topmem`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `name1`, `mem1`, `both1`, `name2`, `mem2`, `both2`, ...
- Default template: `<both1>`
- Displays the name and RSS (resident memory size) of running
  processes (`bothn` displays both, and is useful to specify an
  overall maximum and/or minimum width, using the `-m`/`-M` arguments.

`DiskU Disks Args RefreshRate`

- aliases to `disku`
- Disks: list of pairs of the form (device or mount point, template),
  where the template can contain <size>, <free>, <used>, <freep> or
  <usedp>, <freebar> or <usedbar> for total, free, used, free
  percentage and used percentage of the given file system capacity. Example:
  `[("/", "<used>/<size>"), ("sdb1", "<usedbar>")]`
- Args: the argument list (see above). `-t`/`--template` is ignored.
- Default template: none (you must specify a template for each file system).

`DiskIO Disks Args RefreshRate`

- aliases to `diskio`
- Disks: list of pairs of the form (device or mount point, template),
  where the template can contain <total>, <read>, <write> for total,
  read and write speed, respectively. Example:
  `[("/", "<read> <write>"), ("sdb1", "<total>")]`
- Args: the argument list (see above). `-t`/`--template` is ignored.
- Default template: none (you must specify a template for each file system).

`Thermal Zone Args RefreshRate`

- aliases to the Zone: so `Zone "THRM" []` can be used in template as `%THRM%`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `temp`
- Default template: `Thm: <temp>C`
- This plugin works only on sytems with devices having thermal zone.
  Check directories in /proc/acpi/thermal_zone for possible values.
- Example: `Run Thermal "THRM" ["-t","iwl4965-temp: <temp>C"]`

`CpuFreq Args RefreshRate`

- aliases to `cpufreq`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `cpu0`, `cpu1`, ..,  `cpuN`
- Default template: `Freq: <cpu0>GHz`
- This monitor requires acpi_cpufreq module to be loaded in kernel
- Example: `Run CpuFreq ["-t","Freq:<cpu0>|<cpu1>GHz","-L","0","-H","2","-l","lightblue","-n","white","-h","red"] 50`

`CoreTemp Args RefreshRate`

- aliases to `coretemp`
- Args: the argument list (see below)
- Variables that can be used with the `-t`/`--template` argument:
	    `core0`, `core1`, ..,  `coreN`
- Default template: `Temp: <core0>C`
- This monitor requires coretemp module to be loaded in kernel
- Example: `Run CoreTemp ["-t","Temp:<core0>|<core1>C","-L","40","-H","60","-l","lightblue","-n","gray90","-h","red"] 50`

`MPD Args RefreshRate`

- aliases to `mpd`
- Args: the argument list (see below). In addition you can provide
  `-P`, `-S` and `-Z`, with an string argument, to represent the
  playing, stopped and paused states in the `statei` template field,
  and `-h`, `-p` and `-x` for the host, port and password (default
  host is "localhost", port 6600 and empty password).
- Variables that can be used with the `-t`/`--template` argument:
             `bar`, `state`, `statei`, `volume`, `length`
             `lapsed`, `remaining`,
             `plength` (playlist length), `ppos` (playlist position)
             `name`, `artist`, `composer`, `performer`
             `album`, `title`, `track`, `file`, `genre`
- Default template: `MPD: <state>`
- Example:
    `Run MPD ["-t",
              "<composer> <title> (<album>) <track>/<plength> <statei> ",
              "--", "-P", ">>", "-Z", "|", "-S", "><"] 10`
   Note that you need "--" to separate regular monitor options from
   MPD's specific ones.
- This monitor will only be compiled if you ask for it using the
  `with_mpd` flag. It needs libmpd 4.1 or later (available on Hackage).

`Mail Args`

- aliases to `Mail`
- Args: list of maildirs in form [("name1","path1"),("name2","path2")]
- This plugin requires INOTIFY support in Linux kernel and hinotify library.
  To activate, pass --flags="with_inotify" to "runhaskell Setup configure"
  or to "cabal install".

`MBox Mboxes Opts Alias`

- Mboxes a list of mbox files of the form [("name", "path", "color")],
  where name is the displayed name, path the absolute or relative (to
  BaseDir) path of the mbox file, and color the color to use to display
  the mail count (use an empty string for the default).
- Opts is a possibly empty list of options, as flags. Possible values:
   -a  --all (no arg)  Show all mailboxes, even if empty.
   -d dir  --dir dir a string giving the base directory where mbox files with
                     a relative path live.
   -p prefix --prefix prefix  a string giving a prefix for the list
                      of displayed mail coints
   -s suffix --suffix suffix  a string giving a suffix for the list
                      of displayed mail coints
- This plugin requires INOTIFY support in Linux kernel and hinotify library.
  To activate, pass --flags="with_inotify" to "runhaskell Setup
  configure" or to "cabal install".
- Example:
   `Run MBox [("I ", "inbox", "red"), ("O ", "/foo/mbox", "")]
             ["-d", "/var/mail/", "-p", " "] "mbox"`
  will look for mails in `/var/mail/inbox` and `/foo/mbox`, and will put
  a space in front of the printed string (when it's not empty); it
  can be used in the template with the alias `mbox`.

## Monitor Plugins Commands Arguments

These are the arguments that can be used for internal commands in the
`commands` configuration option:

    -H number           --High=number               The high threshold
    -L number           --Low=number                The low threshold
    -h color number     --high=color number         Color for the high threshold: es "#FF0000"
    -n color number     --normal=color number       Color for the normal threshold: es "#00FF00"
    -l color number     --low=color number          Color for the low threshold: es "#0000FF"
    -p number           --ppad=number               Pad percentages to given width
    -m number           --minwidth=number           Minimum field width
    -M number           --maxwidth=number           Maximum field width
    -w number           --width=number              Fixed field width
    -c chars            --padchars=chars            Chars used (cyclically) for padding
    -a ["r" or "l"]     --align=["r" or "l"]        Pad alignment (right/left)
    -b chars            --bback=chars               Chars used to draw bar backgrounds (default ":")
    -f chars            --bfore=chars               Chars used to draw bar foregrounds (default "#")
    -W number           --bwidth=number             Bar width (default 10)
    -t output template  --template=output template  Output template of the command.

Commands' arguments must be set as a list. E.g.:

    Run Weather "EGPF" ["-t","<station>: <tempC>C"] 36000

In this case xmobar will run the weather monitor, getting information
for the weather station ID EGPF (Glasgow Airport, as a homage to GHC)
every hour (36000 tenth of seconds), with a template that will output
something like:

    Glasgow Airport: 16.0C

## Executing External Commands

In order to execute an external command you can either write the
command name in the template, in this case it will be executed without
arguments, or you can configure it in the "commands" configuration
option list with the Com template command:

`Com ProgramName Args Alias RefreshRate`

- ProgramName: the name of the program
- Args: the arguments to be passed to the program at execution time
- Alias: a name to be used in the template. If the alias is en empty
  string the program name can be used in the template.

Es:

        Run Com "uname" ["-s","-r"] "" 36000

can be used in the output template as `%uname%`

        Run Com "date" ["+\"%a %b %_d %H:%M\""] "mydate" 600

can be used in the output template as `%mydate%`

## Other Plugins

`Date Args Alias RefreshRate`

`StdinReader`

`CommandReader "/path/to/program" Alias`

`PipeReader "/path/to/pipe" Alias`

Plugins
=======

## Writing a Plugin

Writing a plugin for xmobar should be very simple. You need to create
a data type with at least one constructor.

Next you must declare this data type an instance of the `Exec` class, by
defining the 1 needed method (alternatively `start` or `run`) and 2
optional ones (alias and rate):

        start :: e -> (String -> IO ()) -> IO ()
        run   :: e -> IO String
        rate  :: e -> Int
        alias :: e -> String

`start` must receive a callback to be used to display the `String`
produced by the plugin. This method can be used for plugins that need
to perform asynchronous actions. See `Plugins/PipeReader.hs` for an
example.

`run` can be used for simpler plugins. If you define only `run` the
plugin will be run every second. To overwrite this default you just
need to implement `rate`, which must return the number of tenth of
seconds between every successive runs. See `Plugins/HelloWorld.hs` for
an example of a plugin that runs just once, and `Plugins/Date.hs` for
one that implements `rate`.

Notice that Date could be implemented as:

        instance Exec Date where
            alias (Date _ a _) = a
            start (Date f _ r) = date f r

        date :: String -> Int -> (String -> IO ()) -> IO ()
        date format r callback = do go
            where go = do
                    t <- toCalendarTime =<< getClockTime
                    callback $ formatCalendarTime defaultTimeLocale format t
                    tenthSeconds r >> go

This implementation is equivalent to the one you can read in
`Plugins/Date.hs`.

`alias` is the name to be used in the output template. Default alias
will be the data type constructor.

Implementing a plugin requires importing the plugin API (the `Exec`
class definition), that is exported by `Plugins.hs`. So you just need
to import it in your module with:

        import Plugins

After that your type constructor can be used as an argument for the
Runnable type constructor `Run` in the `commands` list of the
configuration options.

This requires importing your plugin into `Config.hs` and adding your
type to the type list in the type signature of `Config.runnableTypes`.

For a very basic example see `Plugins/HelloWorld.hs` or the other
plugins that are distributed with xmobar.

## Installing/Removing a Plugin

Installing a plugin should require 3 steps. Here we are going to
install the HelloWorld plugin that comes with xmobar:

1. import the plugin module in `Config.hs`, by adding:

        import Plugins.HelloWorld

2. add the plugin data type to the list of data types in the type
   signature of `runnableTypes` in `Config.hs`. For instance, for the
   HelloWorld plugin, change `runnableTypes` into:

        runnableTypes :: Command :*: Monitors :*: HelloWorld :*: ()
        runnableTypes = undefined

3. Rebuild and reinstall xmobar. Now test it with:

        xmobar Plugins/helloworld.config

As you may see in the example configuration file, the plugin can be
used by adding, in the `commands` list:

        Run HelloWorld

and, in the output template, the alias of the plugin:

        %helloWorld%

That's it.

To remove a plugin, just remove its type from the type signature of
runnableTypes and remove the imported modules.

To remove the system monitor plugin:

1. remove, from `Config.hs`, the line

        import Plugins.Monitors

2. in `Config.hs` change

         runnableTypes :: Command :*: Monitors :*: ()
         runnableTypes = undefined

    to

         runnableTypes :: Command :*: ()
         runnableTypes = undefined

3. rebuild xmobar.

Bug Reports
===========

To submit bug reports you can use the Google code bug tracking system
available at the following address:

<http://code.google.com/p/xmobar/issues>

Credits
=======

__Andrea Rossato__:

Thanks to Robert Manea and Spencer Janssen for their help in
understanding how X works. They gave me suggestions on how to solve
many problems with xmobar.

Thanks to Claus Reinke for make me understand existential types (or at
least for letting me think I grasp existential types...;-).

xmobar incorporates patches from: Krzysztof Kosciuszkiewicz, Spencer
Janssen, Jens Petersen, Dmitry Kurochkin, Lennart Kolmodin, and
Norbert Zeh.

__jao__:

Thanks to Andrea for creating xmobar in the first place, and for
giving the chance to contribute.

Useful links
============

- [Github page](http://github.com/jaor/xmobar).
- [Mailing list].
- Andrea's original [xmobar] home page, and [xmobar darcs repository].

- To understand the internal mysteries of xmobar you may try reading
  [this tutorial] on X Window Programming in Haskell.

- My [sawflibs] project includes a module to automate running xmobar
  in [sawfish].

[Mailing list]: http://projects.haskell.org/cgi-bin/mailman/listinfo/xmobar
[xmobar]: http://code.haskell.org/~arossato/xmobar/
[xmobar darcs repository]: http://code.haskell.org/xmobar
[this tutorial]: http://www.haskell.org/haskellwiki/X_window_programming_in_Haskell
[sawflibs]: http://github.com/jaor/sawflibs
[sawfish]: http://sawfish.wikia.com/

Author
======

Andrea Rossato originally designed and implemented xmobar up to
version 0.11.1. Since then, it is maintained by [Jose Antonio Ortega
Ruiz](http://hacks-galore.org/jao/).

Legal
=====

This software is released under a BSD-style license. See [LICENSE] for
more details.

Copyright &copy; 2007-2010 Andrea Rossato

Copyright &copy; 2010 Jose Antonio Ortega Ruiz

[Github]: http://github.com/jaor/xmobar/
[Hackage]: http://hackage.haskell.org/package/xmobar/
[LICENSE]: https://github.com/jaor/xmobar/raw/master/LICENSE
[MPD]: http://mpd.wikia.com/
[X11-xft]: http://hackage.haskell.org/package/X11-xft/
[i3status]: http://i3.zekjur.net/i3status/
[iwlib]: http://www.hpl.hp.com/personal/Jean_Tourrilhes/Linux/Tools.html
[libmpd]: http://hackage.haskell.org/package/libmpd/
[utf8-string]: http://hackage.haskell.org/package/utf8-string/
[xmobar.config-sample]: https://github.com/jaor/xmobar/raw/master/xmobar.config-sample
