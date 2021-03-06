# Basic commands

Most command names in radare are derived from action names. They should be easy to remember as well as short. Therefore command names are all single letters. Subcommands or related commands are described using a second character. For example `/ foo` for searching plain strings or `/x 90 90` to look for hexpair strings.

The format of valid command (as explained in 'Command format' chapter) looks something like this:

    [[.][times][cmd][~grep][@[@iter]addr!size][|>pipe] ; ...

    > 3s +1024    ; seeks three times 1024 from the current seek
    
If the command starts with `!` the string is passed to the currently loaded IO plugin (the debugger for example). If no plugin handles the command, posix_system() is called to hand the command to your shell. You can also force this by prefixing your command with two exlamation marks `!!` to make sure your command is directly handed to the shell.

    > !help       ; handled by the debugger or shell
    > !!ls        ; runs ls in the shell

The [arg]-part depends on the specific command. As a rule of thumb, most commands take a number as an argument to specify the number of bytes to work on instead of block size. Other commands accept math expressions, or strings.

    > px 0x17     ; show 0x17 bytes in hexa at current seek
    > s base+0x33 ; seeks to flag 'base' plus 0x33
    > / lib       ; search for 'lib' string.
The `@` command is used to specify a temporal offset location / seek at which the command is executed. This is quite useful so you don't have to seek around all the time.

    > p8 10 @ 0x4010  ; show 10 bytes at offset 0x4010
    > f patata @ 0x10 ; set 'patata' flag at offset 0x10
Using the `@@` command you can execute a single command on a list of flags matching the glob. You can think of it as a foreach operation:

    > s 0
    > / lib             ; search 'lib' string
    > p8 20 @@ hit0_*   ; show 20 hexpairs at each search hit
    
The `>` operation is used to redirect the output of a command into a file (truncating it to 0 if it already exist)

    > pr > dump.bin   ; dump 'raw' bytes of current block to 'dump.bin' file
    > f  > flags.txt  ; dump flag list to 'flags.txt'
    
The `|` operation (pipe) behaves similar to what you're used from the *NIX shell: use the output of one command as the input to another.

    [0x4A13B8C0]> f | grep section | grep text
    0x0805f3b0 512 section._text
    0x080d24b0 512 section._text_end
    
Using the `;` char you can concatenate multiple commands on a single line:

    > px ; dr
