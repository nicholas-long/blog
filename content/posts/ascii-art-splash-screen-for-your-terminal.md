+++
title = 'Ascii Art Splash Screen for Your Terminal'
date = 2023-11-26T10:54:54-06:00
draft = false
+++

I like having a [splash screen with ASCII art](https://github.com/nicholas-long/environment/blob/main/zet/20230906032330).
A lot of people like using neofetch, but I like putting my own art there.
Either way, it is very simple to configure.

- My ascii art
```
&&&&&&&&&&&&
&          &
&          &
&          &
&          &
&coyote0x90&
&&&&&&&&&&&&
&@&&&&=====&
&&&&&&&&&&&&
 &&&&&&&&&& 
 &&&&&&&&&& 
```

Bash and Zsh both load RC files from your home directory when they launch, `.bashrc` and `.zshrc` respectively.
These are just scripts that get loaded full of setup commands to run before running your commands in the interactive terminal.
These files are responsible for user terminal customizations and for configuring the `PATH` environment variable to point to any extra user scripts.
This is where you would put an ASCII art splash screen.
My versions of these files that I use for my environment are located [here](https://github.com/nicholas-long/environment/blob/main/zet/20230905015120).

I created a [script to center text in the terminal](https://github.com/nicholas-long/environment/blob/main/zet/20230906050031/README.md) which I use to center my art.
It uses the tty size from stty in order to figure out how big the terminal is and prints adequate spaces before each line to center text.
As long as my ascii art is rectangular, it will appear centered.
If it is not rectangular, and there are some lines where it does not line up then it will not display correctly and can appear chopped or glitched.

- How to center text in the terminal with AWK
```awk
BEGIN {
  command = "stty size | awk '{print $2}'"
  command | getline cols
  close(command)
}
{
  spacing = ( cols - length() ) / 2
  for (i = 0; i < spacing; i++) printf " "
  print
}
```
This is not strictly necessary in order to implement this, but I thought it is nice to see the ASCII art line up.
In [my implementation in my environment](https://github.com/nicholas-long/environment/blob/main/zet/20230906032330), I even have a backup ASCII art picture to show if the terminal is too narrow to display the full-sized one properly.

- Adjust ASCII art if the screen is too narrow
```bash
splashscreen="$ENVIRON_BASEPATH/zet/20230906032330/splash"
cols=$(stty size | awk '{print $2}')

if [ $cols -lt 70 ]; then
  splashscreen="$ENVIRON_BASEPATH/zet/20230906032330/splash-narrow"
fi
```
These details are just a tiny little preview of the kind of ricing you can do to your terminal.

If you want to set it up yourself, just add some lines to the bottom of your `.zshrc` or `.bashrc` files.
First let's install neofetch.
```bash
sudo apt install neofetch # run this line on debian system
brew install neofetch # run this line on macbooks
```

These bash commands will enable neofetch as an ascii art splash screen on a Debian type Linux system.
```bash
echo neofetch >> ~/.bashrc
echo neofetch >> ~/.zshrc
```

