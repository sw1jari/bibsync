# bibsync

Keeps the `.bib` file of an Overleaf project in sync with a Zotero collection.

It exports the collection with Better BibTeX, writes it into your local git clone of the Overleaf project, and commits and pushes it, so Overleaf has your latest references a few seconds later. It's faster than Overleaf's own Zotero integration, and the citation keys are the ones you see in Zotero.

## Use

```sh
bibsync           # sync the project you're in, or pick one from a menu
bibsync all       # sync every linked project
bibsync link      # link a project to a collection, or change its link
bibsync list      # show the links
```

The first time you sync a project, it asks for three things: the Overleaf project, the `.bib` file in it, and the Zotero collection. After that, syncing is one command.

It works from a terminal and from dmenu. In a terminal the menus open in fzf and messages are printed. From dmenu_run or a keybinding they open in dmenu and messages come back as notifications.

From dmenu, the first sync goes like this:

- Run `bibsync`, pick `link a new project`
- Pick the Overleaf project, then the `.bib` file, then the Zotero collection
- It syncs right away and tells you how many references it pushed

Later runs show the linked projects in the menu, and picking one syncs it.

## Setup

Zotero has to be running with the [Better BibTeX](https://retorque.re/zotero-better-bibtex/) plugin. Better BibTeX decides the citation keys; you can set the format in its preferences.

You need a local git clone of each Overleaf project, which requires an Overleaf plan with git access. Set up a git credential helper so pushes don't ask for the token each time. Any helper works; libsecret keeps it in the system keyring:

```sh
git config --global credential.https://git.overleaf.com.helper libsecret
git clone https://git@git.overleaf.com/<project id>
```

Linking looks for Overleaf clones in your home folder. Searching the whole home folder can take a few seconds, so you can list the folders to search, one per line, in `~/.config/bibsync/roots`:

```
~/uni
~/Documents
```

A project somewhere else can always be linked by running `bibsync link` inside it.

## How it works

Better BibTeX serves exports on Zotero's local port (23119). bibsync asks it for the linked collection as BibTeX. If the result differs from the `.bib` in the project, it pulls, replaces the file, commits it as "Update references" and pushes.

The list of collections comes from a read-only snapshot of `~/Zotero/zotero.sqlite`, since Zotero locks the database while it runs. Set `ZOTERO_DB` if yours is elsewhere, and `ZOTERO_PORT` if Zotero listens on another port.

Whether items in subcollections are included follows the "Show Items from Subcollections" option in Zotero's View menu.

The `.bib` file is overwritten on each sync, so keep hand-written entries in a separate file and list both in `\bibliography{}`.

## Install

Needs bash, git, curl and sqlite3, with dmenu or fzf for the menus. dunst or notify-send is optional, for notifications.

```sh
cp bibsync ~/.local/bin/
```

## License

MIT
