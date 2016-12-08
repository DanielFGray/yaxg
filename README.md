# yaxg

Yet Another X Grabber script

## Dependencies

* [slop](https://github.com/naelstrof/slop) - for selecting regions and windows
* [maim](https://github.com/naelstrof/maim) - for saving screenshots
* [ffmpeg](http://ffmpeg.org/) - for recording videos
* [byzanz](http://git.gnome.org/browse/byzanz) - for recording gifs (soon to be replaced with ffmpeg)

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
-g  default to saving as gif (will be overridden by file extension)
-p  default to saving as png (will be overridden by file extension)
-w  default to saving as webm (will be overridden by file extension)
		this is the default behavior when -g or -p is not present and the filename doesn't end in png or gif
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

## See also

* [tekup](/DanielFGray/tekup) - upload files to https://teknik.io via `yaxg -e 'tekup $f'`
