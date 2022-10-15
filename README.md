# recent_files

## Overview

*recent_files* displays a list of recently-modified files. By default, files modified in the past seven days are shown. By default, it uses Git ignore files to restrict the files shown.

Output may be text or JSON. JSON output was intended for use by an [Alfred](https://www.alfredapp.com/) workflow.

## Prerequisites

Both Python 3 and *fd(1)* are required to run this application. Both can be installed from a variety of sources, including [Brew](https://brew.sh/), [MacPorts](https://www.macports.org/), and direct downloads of the executables for your platform.

The default location specified in the script for fd(1) is `/opt/local/bin/fd`. This can be overridden with the command line argument `--fd-command`. See below.

## Installation

Copy *recent_files* to a directory in your $PATH, such as `~/bin` or wherever user scripts are located.

## Command-line usage

```
usage: recent_files [-h] [-c CHANGED_WITHIN] [-d DIR] [-H] [-i IGNORE_FILE]
                    [--fd-command FD_COMMAND] [-l LIMIT] [-o {text,json}] [-t {f,d,l,s,p,x,e}]

Finds recent files

optional arguments:
  -h, --help            show this help message and exit
  -c CHANGED_WITHIN, --changed-within CHANGED_WITHIN
                        Changed within 1h, 2d, 5min, etc.; the default is 7d
  -d DIR, --dir DIR     Directory to start search from, the default is the current directory
  -H, --hidden          Show hidden files; the default is not to show hidden files
  -i IGNORE_FILE, --ignore-file IGNORE_FILE
                        Path to the Git-format ignore file for search exclusions, optional
  --fd-command FD_COMMAND
                        Path to the fd(1) command; the default is /opt/local/bin/fd
  -l LIMIT, --limit LIMIT
                        Limit number of results; must be greater than 1
  -o {text,json}, --output-format {text,json}
                        Output format, default is text
  -t {f,d,l,s,p,x,e}, --filetype {f,d,l,s,p,x,e}
                        Filetype, as supported by fd(1), default is "f"
```

### Command-line examples

List files modified within the past day:

`recent_files --changed_within 1d`

List files modified in the past week, in the Documents folder:

`recent_files -c 1w --dir ~/Documents`

List files modified in the past hour, including hidden files:

`recent_files -c 1h -H`

Use a custom ignore file, return JSON output:

`recent_files --ignore-file ~/my_ignore_file -o json`

Specify a different location for the fd(1) command:

`recent_files --fd-command /some/path/to/fd/command`

## Ignore Files using a Git-style ignore file

*recent_files*, through *fd(1)*, will respect a Git-style ignore file.

Here's an example ignore file:

```
Library
~*
*.jpg
*.mov
```

This ignore file will cause *fd(1)* to ignore files in the Library folder, files beginning with the '~' character (often used for temporary files), .jpg image files, and .mov movie files. Files matched in an ignore file will not be returned as results.

If this file was named `my_ignore_file` and put in the $HOME directory, the following command line would allow *recent_files* to use it:

`recent_files --ignore-file $HOME/my_ignore_file`

## Alfred usage

Although *recent_files* can be used from the command-line, it was initially intended to be used with the [Alfred application launcher](https://www.alfredapp.com).

### Example usage within Alfred

A minimal command line for an Alfred workflow would look like this:

`/Users/j/bin/recent_files --dir $HOME --output-format json`

### JSON output

*recent_files* uses the JSON output format defined by Alfred.

See [Script Filter JSON Format](https://www.alfredapp.com/help/workflows/inputs/script-filter/json/) for more, but here's the basic structure:

```
{
  "items": [
    {
      "type": "file",
      "title": "/Users/j/Documents/foo.txt",
      "subtitle": "/Users/j/Documents/foo.txt",
      "arg": "/Users/j/Documents/foo.txt",
      "icon": {
        "type": "fileicon",
        "path": "/Users/j/Documents/foo.txt"
      }
    },
    {
      "type": "file",
      "title": "/Users/j/Documents/bar.txt",
      "arg": "/Users/j/Documents/bar.txt"
      "subtitle": "/Users/j/Documents/bar.txt",
      "icon": {
        "type": "fileicon",
        "path": "/Users/j/Documents/bar.txt"
      }
    }
  ]
}
```

Note that *recent_files* does not populate the **uid** and **autocomplete** properties used by Alfred.

### Tips on changing search results and speeding up the search

If *recent_files* isn't finding what you need, or alternately if *recent_files* is slow, consider some of the following options:

1. Add a `--changed-within` argument to the command line to increase or decrease the default time period to search. An increase in the time period from the default of 7 days will increase the number of results, while a decrease in the time period will increase performance.

2. Add a `--ignore-file` argument to the command. This may drastically increase performance. See *Ignore Files using a Git-style ignore file*.

3. Add a `--limit n` argument. While this won't increase the performance of the search itself, it will reduce the number of files displayed, which may
