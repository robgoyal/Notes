# TMUX

Terminal multiplexer for Unix-like OS' which allow multiple terminal sessions to be accessed simulatenously in a single window. 

Tmux is broken into a client-server model which is what allows you to reconnect to a session after you have detached from it.

Tmux sessions are tied to a *separate process*, not to your shell session. If you log out of the system, disconnected, timed out, etc, you will be able to attach to that session if it exists. 

**Note**: Tmux sessions do not surive reboots. 

## Tmux configuration options

Set history scrollback limit to 10000
`set -g history-limit 10000`

Prevent windows from being renamed with the current process
`set -g allow-rename off`

Set search mode in Window to vi
`set-window-option -g mode-keys vi`

## Command Cheat Sheet
By default, the prefix key is `ctrl + b`

### Sessions

Create a named tmux session

```
$ tmux new -s <session name>
```

List sessions

```
$ tmux ls
```

Attach to a session

```
$ tmux attach -t <session name or id>
```

Detach from a session

`<prefix> d`

Kill a session

```
$ tmux kill-session -t <session name>
```

Kill all sessions

```
$ tmux kill-session -a
```

### Windows

| Functionality        | Command         |
|----------------------|-----------------|
| Create window        | `<prefix> c`    |
| Rename window        | `<prefix> ,`    |
| Close current window | `<prefix> &`    |
| Previous window      | `<prefix> p`    |
| Next window          | `<prefix> n`    |
| Switch window        | `<prefix> 0..9` |

### Panes

| Functionality      | Command                  |
|--------------------|--------------------------|
| Horizontal Split   | `<prefix> %`             |
| Vertical Split     | `<prefix> "`             |
| Move Pane Left     | `<prefix> {`             |
| Move Pane Right    | `<prefix> }`             |
| Switch Pane        | `<prefix> <arrow-key`    |
| Show Pane Numbers  | `<prefix> q`             |
| Switch Pane Number | `<prefix> q 0..9`        |
| Resize Pane        | `<prefix> + <arrow-key>` OR `<prefix> <option> + <arrow key>`|
| Close Pane         | `<prefix> x`             |

### Copy Mode
**Note**: To use vi keys in buffer, refer to [[#Tmux configuration options]]

| Functionality            | Command      |
|--------------------------|--------------|
| Enter Copy Mode          | `<prefix> [` |
| Search forward           | `/ <search>` |
| Search backward          | `? <search>` |
| Next occurrence          | `n`          |
| Previous occurrence      | `N`          |
| Start Selection          | `spacebar`   |
| Clear Selection          | `esc`        |
| Copy Selection           | `enter`      |
| Paste contents of buffer | `<prefix ]`  |

# References
[ippsec](https://www.youtube.com/watch?v=Lqehvpe_djs)
[tmux cheat sheet](https://tmuxcheatsheet.com/)
