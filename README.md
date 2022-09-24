# recent_files

## Overview

*recent_files* displays a list of recently-modified files. By default, files modified in the past seven days are shown. By default, it uses Git ignore files to restrict the files shown.

Output may be text or JSON. JSON output was intended for use by an Alfred workflow. See https://www.alfredapp.com/

## Prerequisites

Both Python 3 and *fd(1)* are required to run this application. Both can be installed from a variety of sources, including Brew, MacPorts, and direct downloads of the executables for your platform.

## Installation

Copy *recent_files* to a directory in your $PATH, such as ~/bin or wherever user scripts are located.

## Usage

```sh
usage: recent_files [-h] [-c CHANGED_WITHIN] [-d DIR] [-i IGNORE_FILE] [-l LIMIT]
                    [-o {json,text}]

Finds recent files

optional arguments:

  -h, --help            show this help message and exit
  -c CHANGED_WITHIN, --changed-within CHANGED_WITHIN
                        Changed within 1h, 2d, 5min, etc., default is 7d
  -d DIR, --dir DIR     Directory to start search from, default is the current directory
  -i IGNORE_FILE, --ignore-file IGNORE_FILE
                        Path to Git-format ignore file for search exclusions, optional
  -l LIMIT, --limit LIMIT
                        Limit number of results
  -o {json,text}, --output-format {json,text}
                        Output format, default is text
```

## Examples

List files modified within the past day:

	recent_files --changed_within 1d

List files modified in the past week, in the Documents folder:

	recent_files -c 1w --dir ~/Documents

Use a custom ignore file, return JSON output:

	recent_files --ignore-file ~/my_ignore_file -o json

## Ignore File

*recent_files*, through *fd(1)*, will respect Git ignore files.

Here's an example ignore file:

```sh
Library
~*
*.jpg
*.mov
```

This ignore file will cause *fd(1)* to ignore files in the Library folder, files beginning with the '~' character (often used for temporary files), .jpg image files, and .mov movie files. Files matched in an ignore file will not be returned as results.

If this file was named ```my_ignore_file``` and put in the $HOME directory, the following command line would allow *recent_files* to reference it:

```recent_files --ignore-file $HOME/my_ignore_file```

## Background

Hans Raaf, inspired by the Trickster app (https://www.apparentsoft.com/trickster), created an Alfred (https://www.alfredapp.com/) workflow called Last changed files (https://github.com/oderwat/alfredworkflows) to display recently modified files using Alfred.

I liked the idea, but the workflow was a little slow. I looked at it, and discovered that it was a PHP application that ran the MacOS *mdfind(1)* command.

I used the fantastic *fd(1)* command for finding files from the command-line, and I wondered if it would be any faster than *mdfind(1)* in this case. A side benefit of using *fd(1)* is that it supports the use of Git-style ignore files, so that one could ignore files and directories easily. (The *mdfind(1)* command in the workflow could also ignore files, but it required hard-coding values in the workflow itself.)

Not knowing PHP, I wrote recent_files, a quick Python wrapper around *fd(1)*. It works great as a component of an Alfred workflow, but it can also be used from the command line. It appears to be considerably faster than the PHP wrapper around *mdfind(1)*, although this is likely mostly due to *fd(1)*'s support for Git-style ignore files.
