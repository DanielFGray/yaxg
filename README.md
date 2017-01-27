# yaxg

Yet Another X Grabber script

## Dependencies

* *[slop](https://github.com/naelstrof/slop)* - for selecting regions and windows
* *[maim](https://github.com/naelstrof/maim)* - for saving screenshots
* *[ffmpeg](http://ffmpeg.org/)* - for recording videos
* *[byzanz](http://git.gnome.org/browse/byzanz)* - for recording gifs (soon to be replaced with ffmpeg)
* *libnotify* / *notify-send* - for notifications

## Usage
yaxg [OPTIONS] [FILE]  

### Options

```
-d  specify duration to record for (only works for webms)
-D  specify delay
-F  overwrite file if it exists
-s  select a region or window
-S  pass a arguments directly to slop
-e  execute a script in the config dir or a callback string with the current $SHELL
-g  save as gif (will be overridden by file extension)
-p  save as png (will be overridden by file extension)
-w  save as webm (will be overridden by file extension)
    this is the default behavior when -g or -p is not present and the filename doesn't end in png or gif
    press Ctrl+C to stop recording or run `yaxg stop`
-v  increase verbosity (can be stacked)
    first level will show errors parsing config file, consecutive levels will be passed to ffmpeg
-h  print this help
```

### Special Strings

Similar to `scrot`, the callback and filename parameters can take format specifiers that are expanded when encountered. Characters preceded by
`%` are interpreted directly by the `date` commmand. See `man strftime` for examples. Characters preceded by `$` are parsed by yaxg and expanded as such:

```
$f  quoted image path/filename (only available in callback)
$w  image width
$h  image height
```

### Configuration

A configuration file can saved at `$XDG_CONFIG_DIR/yaxg/conf` (defaults to `$HOME/.config/yaxg/conf` if `XDG_CONFIG_DIR` is not set).  
If a line begins with '#' it is treated as a comment and ignored.  
The config file accepts the following values:

```
callback
  a callback string to execute
  if callback is a script in the config dir, the the quoted filename will automatically be passed to the script
filename
  a default filename to use when none is provided (must not include extension)
format
  default format to use, must be either 'png', 'webm', or 'gif'
slop
  arguments to be passed to slop
```

### Examples

* Example CLI usage:

``` bash
yaxg '%s-$w-$h'
yaxg '%s-$w-$h' -e 'mv $f ~/images/$f'
yaxg -s -S '-l -c 0.3,0.4,0.6,0.4' -e 'mv $f ~/images/$f && firefox ~/images/$f'
printf '#!/usr/bin/env bash\n\n[[ "$1" =~ png$ ]] && optipng "$1"\n' > ~/.config/yaxg/myScript && chmod +x !#:3 && yaxg -e 'myScript'
```

* Example config file:

```
filename  %c - $wx$h
format    png
slop      -l -c 0.3,0.4,0.6,0.4
callback  myScript
```

The wiki has some [example callback scripts](https://github.com/DanielFGray/yaxg/wiki/Example-callback-scripts).

## Installation

Arch Linux users can install from the AUR as `yaxg-git` via their favorite AUR helper.

If you're on another distro, I would suggest cloning the repo and symlink'ing the script into `~/.local/bin` and adding that to your `PATH`.

## See also

* [tekup](https://github.com/DanielFGray/tekup) - upload files to https://teknik.io via `yaxg -e 'tekup $f'`

## Legal
Copyright (C) 2016 Daniel F Gray <DanielFGray@gmail.com>

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.  If not, see <http://www.gnu.org/licenses/>.
