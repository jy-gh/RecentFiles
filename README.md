# **Recent Files**

## Overview

**Recent Files** is both an [Alfred](https://www.alfredapp.com) workflow and a standalone command-line utility that displays a list of recently added/modified files, newest first. When used as an Alfred workflow, the list of files is displayed in Alfred's file browser. When used as a command-line utility (**recent_files**), the list of files is sent to standard output.

![demo][recent_files_alfred_demo]

## Prerequisites

### Alfred Powerpack

You must have [Alfred's Powerpack](https://www.alfredapp.com/powerpack/) installed in order to use **Recent Files** as an Alfred workflow.

### fd

In addition, the `fd` command is required to run this application. `fd` can be installed from a variety of sources, including [MacPorts](https://www.macports.org/) and [Brew](https://brew.sh/).

It's possible to confirm that `fd` is installed by entering the following command in a Terminal window:

`which fd`

If the message *fd not found* is displayed, that means that the `fd` command has not been installed (or is not somewhere in the user's `$PATH`). If `fd` is installed, the command will display the correct path to it. Use this path in the Workflow Configuration (or with the `--fd-command` argument to **recent_files**) if it differs from the default location, `/opt/local/bin/fd`.

`fd` is worth having if you don't have it installed. It can replace most uses of `find`, with both an easier to remember syntax and improved performance.

### Python 3

**Recent Files** and **recent_files** require Python 3.8; earlier 3.x version of Python may work, but have not been tested.

It's possible to confirm that `python3` is installed by entering the following commands in a Terminal window:

`which python3`

If the message *python3 not found* is displayed it will be necessary to install Python in order to use **Recent Files**/**recent_files**.

Python 3.x may be installed using any of the following:

* [MacPorts](https://www.macports.org/)
* [Brew](https://brew.sh/)
* [Direct download from Python.org](https://www.python.org/downloads/)

## **Recent Files**, the Alfred workflow

### Installation

Download the workflow and double-click on it to install it. During installation Alfred will display the [Workflow Configuration](#Workflow-Configuration) values.

### Usage

`rf` — Invoke **Recent Files**

After invocation, **Recent Files** will display the list of results in Alfred where any allowed action (open/move/delete/preview, etc.) may be performed. By default, this search starts at the user's `$HOME` directory—although this can be changed, see [Workflow Configuration](#Workflow-Configuration)—and includes files in subdirectories.

### Workflow Configuration

The behavior of **Recent Files** may be configured by selecting the workflow and clicking on the `Configure Workflow` button in Alfred. Here are the values that can changed from that screen:

|Variable|Default value|Description|
|---------|--------|--------|
|`keyword`|rf| Keyword to invoke **Recent Files** from Alfred.|
|`FD_COMMAND`|`/opt/local/bin/fd`| Path to the `fd` command; Brew users may need to change this to `/usr/local/bin/fd`.|
|`TOP_LEVEL_DIRECTORY`|| Directory to search other than `$HOME` (an empty value causes the workflow to default to `$HOME`). Custom values must have an absolute path. Don't use `$HOME` or `~` in the configuration screen, as those values will not work.|
|`MAX_RESULTS`|20|Maximum number of results to display in Alfred.|
|`IGNORE_FILE`|`example_ignore_file.txt`|File to use as an ignore file; see [Ignore Files](#Ignore-Files).|
|`CHANGED_WITHIN`|7d|Show items added/changed within a time period. The default is 7d (seven days); other units may be used, such as min (minutes), h (hours), and w (weeks). A value of 12h, for example, would only return files created or modified the previous 12 hours.|
|`FILETYPE`|f|File types to display; valid types include f for files, d for directories. These arguments can be combined, so df will show both directories and files.|

## **recent_files**, the command-line utility

The **Recent Files** Alfred workflow uses a Python 3 script, **recent_files**, to perform the search. **recent_files** can be used as a standalone command-line utility to display a list of recently-modified files to standard output (stdout).

### Installation

To use **recent_files** as a standalone utility, copy it to a directory in your `$PATH`, such as `~/bin` or wherever user scripts are located.

### Command-line usage

When invoked with `-h`, the following usage message is displayed:

```
usage: recent_files [-h] [-c CHANGED_WITHIN] [-d DIR] [--fd-command FD_COMMAND] [-H]
                    [-i IGNORE_FILE] [-l LIMIT] [-o {text,json}] [--reverse] [-t FILETYPES]

Finds recent files

optional arguments:
  -h, --help            show this help message and exit
  -c CHANGED_WITHIN, --changed-within CHANGED_WITHIN
                        Changed within 1h, 2d, 5min, etc.; the default is 7d
  -d DIR, --dir DIR     Directory to start search from; the default is the current directory
  --fd-command FD_COMMAND
                        Path to the fd(1) command; the default is /opt/local/bin/fd
  -H, --hidden          Show hidden files; the default is not to show hidden files
  -i IGNORE_FILE, --ignore-file IGNORE_FILE
                        Path to the Git-format ignore file for search exclusions, optional
  -l LIMIT, --limit LIMIT
                        Limit number of results to LIMIT; the default is to return all results
  -o {text,json}, --output-format {text,json}
                        Output format, default is text; json is for use by Alfred
  --reverse             Reverse the sorting order; default is newest files first
  -t FILETYPES, --filetype FILETYPES
                        Filetype, as supported by fd; the default is "f"; the argument may be
                        repeated or combined, so both (1) and (2) are allowed: (1) --filetype
                        fd (2) --filetype f --filetype d
```

### Command-line examples

List all files modified within the past day:

`recent_files --changed-within 1d`

List files and directories modified within the past three days:

`recent_files --changed-within 3d --filetype fd`

List files modified in the past week, in the Documents directory and subdirectories:

`recent_files -c 1w --dir ~/Documents`

List files modified in the past hour, including hidden files:

`recent_files -c 1h -H`

Use a custom ignore file, display results in reverse order (oldest item first):

`recent_files --ignore-file ~/my_ignore_file --reverse`

Specify a different location for the fd command:

`recent_files --fd-command /some/path/to/fd/command`

## Ignore Files

Both the **Recent Files** workflow and the **recent_files** command-line utility can use a Git-style ignore file.

This is what the provided ignore file contains:

```
Library
Icon?
~*
*.history
*.indexes
*.photoslibrary
*.musiclibrary
*.tvlibrary
```

This ignore file will cause `fd` to ignore files in the Library folder, Icon files with embedded carriage returns, files beginning with the '~' character (often used for temporary files), .photoslibrary, .musiclibrary, and .tvlibrary files. Files matched in an ignore file will not be returned as results.

If this file was named `my_ignore_file` and put in the `$HOME` directory, the following command line would allow **recent_files** to use it:

`recent_files --ignore-file $HOME/my_ignore_file`

Note that an empty, 0-length file is a valid ignore file. While this may not be useful for the **recent_files** command-line (since one could simply omit the `-i/--ignore-file` argument) this might be desirable when using the **Recent Files** workflow.

## JSON output for Alfred

**recent_files** produces JSON output format as specified by Alfred. It's unlikely to be useful when using the command-line interface, but this capability is necessary for the **Recent Files** workflow to function correctly.

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

Note that **recent_files** does not populate the **uid** and **autocomplete** properties used by Alfred.

## Tips on changing search results and speeding up the search

If **Recent Files**/**recent_files** isn't doing what you need, consider some of the following options:

### Change the default changed within time period

An increase in the time period from the default of 7 days will increase the number of results returned, while a decrease in the time period will increase performance and reduce the number of results returned.

See the [Workflow Configuration](#Workflow-Configuration) section for information on changing this value in the **Recent Files** workflow.

For the **recent_files** command-line utility, do the following:

Add a `--changed-within` argument to the command line to increase or decrease the default time period to search.

### Add a custom ignore file

A custom ignore file is one of the best ways to filter out unwanted results.

See the [Workflow Configuration](#Workflow-Configuration) section for information on changing this value in the **Recent Files** workflow.

For the **recent_files** command-line utility, do the following:

Add the `--ignore-file` argument to the command. See [Ignore Files](#Ignore-Files).

### Limit the number of returned results

See the [Workflow Configuration](#Workflow-Configuration) section for information on changing this value in the **Recent Files** workflow.

For the **recent_files** command-line utility, do the following:

Add the `--limit` argument. While this won't increase the performance of the search itself, it will reduce the number of files displayed, which may prevent the list of files from being overly large.

### Change the top-level directory

Changing the default top-level directory from `$HOME` to another directory, such as `$HOME/Documents`, is another way to return results faster, as it will eliminate many files from consideration.

See the [Workflow Configuration](#Workflow-Configuration) section for information on changing this value in the **Recent Files** workflow, keeping in mind that the path to the directory must be absolute.

For the **recent_files** command-line utility, do the following:

Add the `--directory` argument to the command.

## Acknowledgements

This workflow was inspired by Hans Raaf's Alfred workflow, [*Last changed files*](https://github.com/oderwat/alfredworkflows).

## Copyright

All code/media is released under the [MIT License](https://opensource.org/licenses/MIT)
