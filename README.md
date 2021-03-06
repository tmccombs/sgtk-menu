# sgtk-menu
This project is an attempt to create a simple menu, that behaves decently on sway, but also on i3 window manager. 
It uses `pygobject` to create a themeable, searchable gtk3-based system menu w/ some optional features.

## Features

- `.desktop` entries-based system menu;
- search box to find what you need quickly;
- favourites (most frequently used entries) menu above (optional `[-f | -fn FN]` argument);
- user-defined menu below (optional `[-a | -af AF]` argument).

As the script searches `.desktop` files only, it may be used as a replacement to wofi/rofi `--drun`, but not for 
`--dmenu` mode.

![screenshot](http://nwg.pl/Lychee/uploads/big/fbe90b3f5c8d192657a7e9ad98310d84.png)
*The menu in Clearlooks GTK theme w/ Aqatix icons*

## Background

Well, I didn't even think that sway needed a menu, being happy with [wofi](https://hg.sr.ht/~scoopta/wofi) and 
[dmenu-wayland](https://github.com/nyyManni/dmenu-wayland). I started coding just to find out what the freedesktop 
[Desktop Menu Specification](https://specifications.freedesktop.org/menu-spec/latest) looks like, and also to learn some 
more [pygobject](https://pygobject.readthedocs.io/en/latest). [The best menu I know](https://github.com/johanmalm/jgmenu), 
however, does not (yet?) behave well on sway. So, I thought to share the code, which has already taken me more time 
that I had ever expected.

[This code](https://github.com/johanmalm/jgmenu/blob/master/contrib/pmenu/jgmenu-pmenu.py) by 
[Johan Malm](https://github.com/johanmalm) helped me understand how to make use of `.desktop` entries. Many thanks!

## Usage

```text
$ sgtk-menu -h
usage: menu.py [-h] [-b] [-f | -fn FN] [-a | -af AF] [-l L] [-s S] [-w W] [-d D] [-o O] [-t T]

GTK menu for sway and i3

optional arguments:
  -h, --help        show this help message and exit
  -b, --bottom      display menu at the bottom
  -f, --favourites  prepend 5 most used items
  -fn FN            prepend <FN> most used items
  -a, --append      append custom menu from /home/piotr/.config/sgtk-menu/appendix
  -af AF            append custom menu from /home/piotr/.config/sgtk-menu/<AF>
  -l L              force language (e.g. "de" for German)
  -s S              menu icon size (min: 16, max: 48, default: 20)
  -w W              menu width in px (integer, default: screen width / 8)
  -d D              menu delay in milliseconds (default: 100)
  -o O              overlay opacity (min: 0.0, max: 1.0, default: 0.3)
  -t T              sway submenu lines limit (default: 30)
```

Sample sway key binding:

`bindsym mod1+F1 exec sgtk-menu -f -a`

or sample i3 key binding:

`bindsym Mod1+F1 exec --no-startup-id sgtk-menu -f -a`

displays menu prepended with the default number of favourites, appended with the default `~/.config/sgtk-menu/appendix`
file. Use `sgtk-menu -f -af <custom_menu_file>` to append your custom menu. Copy and edit the default `appendix` file 
(in the same location).

## Installation

For now the only available package is [sgtk-menu](https://aur.archlinux.org/packages/sgtk-menu) [AUR] for Arch linux.
However, you may simply clone the repository and launch the `menu.py` file, instead of `sgtk-menu` command.

## Dependencies

- `sway` or `i3`
- `gtk3`
- `python` (python3)
- `python-gobject`
- `python-i3ipc` (optionally, to avoid deprecation warnings)

## How it works?

The problem to resolve was, that the Gtk.Menu class behaves differently / unexpectedly when open over Wayland and X11 windows. 
To work it around, the script opens the menu over a (semi-)transparent, floating window, that covers all the screen.

## Troubleshooting

### Menu does not position properly in the screen corner

Try increasing the delay length. The default value is 100 milliseconds, and on my laptop it works well down to 30. 
Slower machines, however, may require higher values. E.g. try using `-d 200` argument.

### Overlay behind the menu is not (semi)transparent on i3

You need [compton](https://github.com/chjj/compton) or equivalent X11 compositor.

## Overlay first displays as a tiled window on i3

Add this to your `~/.config/i3/config` file:

```text
for_window [title="sgtk-menu"] border none, floating enable
```

## TODO
- On next sway / GTK release, check if the overflowed menus issue on sway is fixed; remove 50 SLOC long workaround if so;
- testing;
- more testing.
