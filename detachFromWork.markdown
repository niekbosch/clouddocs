# Use `screen` to detach from work

Within an SSH connection, if you start an interactive program it is likely to stop working when you close your terminal. You can set up your VM so that the program will keep working after you close your connection. You can use a program called `screen` for that.

## Set-up

- Create a new VM (you may be able to reuse the existing `images` and `templates` that you have created so far; but you can always import a new appliance from the AppMarket).
- Log into your VM using SSH.
- Let us now create a simple script file called `example.sh`. You can use your favourite editor. In our example we use nano, like this:

```bash
cd
nano example.sh
```

- Write (or copy-and-paste) these lines into the file:

```bash
#!/bin/bash
while sleep 2
do
  date
done
```

- Save the file and exit the editor. For example, in nano, you press `control-x` and then you press `Y` to confirm that you want to save your changes. Then you are back in the terminal.

- Make the script executable:

```bash
chmod u+x example.sh
```

- Run the script:

```bash
./example.sh
```

- After a while, terminate the script by typing `control-c`

## First detach

- Log in to your VM,
- run the example script:

```bash
./example.sh
```

- observe the output's progress
- log in with a second ssh session
- check with `ps` or `pstree` that your script runs,

```bash
pstree
# look for a line like this:
#  ... -example.sh---sleep
```

> **Food for brain:**
>
> **Is `pstree` command installed?** If not, are you able to figure out how to install it? After all, you are the sysadmin!
>
> Suggestion just in case:
> * Find the [Linux distribution (aka distro) name and version you are running](https://www.cyberciti.biz/faq/find-linux-distribution-name-version-number/).
> * [Find the package](https://packages.ubuntu.com/) for the distro you are running that provides `pstree`.
> * Install the package using the [package manager](https://help.ubuntu.com/lts/serverguide/package-management.html) for your linux distro. Tip, try the `apt` command-line tool.
<!--Answer: psmisc-->

- disconnect the terminal with the running example by closing its window
- in the other session, use `ps` or `pstree` again to verify the the execution has stopped.

## Using `nohup`

- Log in to your VM,
- use `nohup` to run the example script in the background:

```bash
nohup ./example.sh &
```

The output will be:

```
nohup: ignoring input and appending output to ‘nohup.out’
```

- as before, check the process with `ps` or `pstree` and have a look at the progress:

```bash
tail -f nohup.out
```

- log out and log back in
- use `ps` or `pstree` to verify that your script is still running
- look at the output file(s) to see that output is still continuing

```bash
tail -f nohup.out
```

- kill the running script (as there is no natural end to it)

```bash
killall example.sh
```

- check the output file again to verify the script is no longer running
- remove the output file.

For more information read the manual, it is small and simple but effective.

```
man nohup
```

## Using `screen`

You will use a program called `screen`. Similar programs are:  `tmux`, `dtach`+`dtvm`.

- start screen for the first time

```bash
screen
```

- after reading an initial message, you press the `Enter` key
- you see the normal shell prompt
- use `pstree` to see that you are actually in a screen session: look for a line similar to

```
├─sshd───sshd───sshd───bash───screen───screen───bash───pstree

```

- start your example script

```bash
./example.sh
```

- wait a few seconds and observe the output, as expected
- detach from the screen session by typing `control-a`, then `d`
- the view is restored to the situation before you started `screen` and line says something similar to:

```
[detached from 14691.pts-0.145]
```

- wait several seconds and reconnect using the `-r` flag:

```
screen -r
```

- you see that the output has accumulated while you were detached, the script continued to run.
- detach again (`control-a` plus `d`)
- log out and back in
- reconnect to the screen session
- find the script still running

- terminate the script (`control-c`)
- terminate the shell inside screen (`control-d`)
- you are back with the extra line `[screen is terminating]`
- try `screen -r`

## Multiple `screen` sessions

`screen` command has a lot of options. Reading the manual page will provide you with a wealth of information.

Examples:

### Start additional session

- in `screen`, type a command, so that you can recognize the session
- type `control-a` `c` (lowercase C)
- you see a fresh screen with a new shell
- type a different command in the new shell
- type `control-a` `control-a` (yes, twice)
- you see the previous session
- type `control-a` `1` (digit one)
- you see the second session
- type `control-a` `0` (digit zero)
- you see the first session again, sessions are numbered from zero
- add another session (`control-a` `c`, as before)

Play with these switching commands between the three sessions using `control-a` and a digit or the `control-a` `control-a` sequences

- type `control-a` `w` (lowercase W)
- during several seconds, a black bar at the bottom shows all sessions (windows) with their number.

### Split screen

- still in `screen`, type `control-a` `S` (capital S)
- the screen is now split into two panes with one the old session and the other blank
- switching session still works within the one pane, try it
- type `control-a` `TAB`
- the cursor moved to the blank pane
- switch one of your sessions
- experiment with switching between panes (`control-a` `TAB`) and between sessions in that pane
- combine with detaching from screen and re-attaching
