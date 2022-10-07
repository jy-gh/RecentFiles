# recent_files

## Overview

*recent_files* displays a list of recently-modified files. By default, files modified in the past seven days are shown. By default, it uses Git ignore files to restrict the files shown.

Output may be text or JSON. JSON output was intended for use by an Alfred workflow. See [Alfred App](https://www.alfredapp.com/).

## Prerequisites

Both Python 3 and *fd(1)* are required to run this application. Both can be installed from a variety of sources, including Brew, MacPorts, and direct downloads of the executables for your platform.

The default location specified in the script for fd(1) is `/opt/local/bin/fd`. This can be overridden with the command line argument `--fd-command`. See below.

## Installation

Copy *recent_files* to a directory in your $PATH, such as `~/bin` or wherever user scripts are located.

## Usage

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
                        Limit number of results
  -o {text,json}, --output-format {text,json}
                        Output format, default is text
  -t {f,d,l,s,p,x,e}, --filetype {f,d,l,s,p,x,e}
                        Filetype, as supported by fd(1), default is "f"
```

## Examples

List files modified within the past day:

```recent_files --changed_within 1d```

List files modified in the past week, in the Documents folder:

```recent_files -c 1w --dir ~/Documents```

List files modified in the past hour, including hidden files:

```recent_files -c 1h -H```

Use a custom ignore file, return JSON output:

```recent_files --ignore-file ~/my_ignore_file -o json```

Specify a different location for the fd(1) command:

```recent_files --fd-command /some/path/to/fd/command```

## Ignore File

*recent_files*, through *fd(1)*, will respect Git ignore files.

Here's an example ignore file:

```
Library
~*
*.jpg
*.mov
```

This ignore file will cause *fd(1)* to ignore files in the Library folder, files beginning with the '~' character (often used for temporary files), .jpg image files, and .mov movie files. Files matched in an ignore file will not be returned as results.

If this file was named `my_ignore_file` and put in the $HOME directory, the following command line would allow *recent_files* to reference it:

```recent_files --ignore-file $HOME/my_ignore_file```

## JSON output

*recent_files* uses the JSON output format defined by Alfred.

See [Script Filter JSON Format](https://www.alfredapp.com/help/workflows/inputs/script-filter/json/) for more, but here's the basic structure:

```
{
  "items": [
    {
      "type": "file",
      "title": "/Users/j/Documents/foo.txt",
      "arg": "/Users/j/Documents/foo.txt"
      "subtitle": "/Users/j/Documents/foo.txt",
    },
    {
      "type": "file",
      "title": "/Users/j/Documents/bar.txt",
      "subtitle": "/Users/j/Documents/bar.txt",
      "arg": "/Users/j/Documents/bar.txt"
    }
  ]
}
```

Note that *recent_files* does not populate the following properties, as they are extraneous: **uid**, **autocomplete**, and **icon**.

## Background

Hans Raaf, inspired by the [Trickster app](https://www.apparentsoft.com/trickster), created an [Alfred workflow](https://www.alfredapp.com/workflows) called [Last changed files](https://github.com/oderwat/alfredworkflows) to display recently modified files using Alfred.

This was a great idea, but the workflow was a little slow. Some inspection revealed that it was a PHP application that ran the MacOS *mdfind(1)* command.

It seemed likely that the fantastic *fd(1)* might be faster than *mdfind(1)* in this case. A side benefit of using *fd(1)* is that it supports the use of Git-style ignore files, so that one could ignore files and directories easily. (The *mdfind(1)* command in the workflow could also ignore files, but it required hard-coding values in the workflow itself.)

*recent_files*, a Python script, is the result. It works great as a component of an Alfred workflow, but it can also be used from the command line. It appears to be considerably faster than the PHP wrapper around *mdfind(1)*, although this is likely mostly due to *fd(1)*'s support for Git-style ignore files.
