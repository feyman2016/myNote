·         **C-x C-f**: Open a file (whether or not it already exists)

·         **C-x C-s**: Save a file

·         **C-x C-w**: Write a file (probably more familiar to you as *Save as...*)

Cut, copy, paste:

·         Highlight text with the mouse or by hitting **C-Space** to *set a mark* and then moving the cursor to highlight a region.

·         **C-w**: Cut a highlighted region

·         **M-w**: Copy a highlighted region

·         **C-k**: Cut (*k*ill) from the cursor to the end of the line

·         **C-y**: Paste (*y*ank)

Some other useful commands:

·         **C-x 2**: Split the window into 2 buffers, one above the other (Use the mouse or **C-x o** to switch between them)

·         **C-x 0**: Undo window-splitting so there is only 1 buffer

·         **C-x b**: Switch to another buffer by entering its name

·         **C-x C-b**: See a list of all current buffers

Getting help within Emacs: In addition to the help button/menu on the right...

·         **C-h**: Hitting this will display a short message in the minibuffer: C-h (Type ? for further options).

·         **C-h b**: Key *b*indings. This lists all key bindings that are valid for the current mode. Note that key bindings change from mode to mode.

·         **C-h a**: Command *a*propos. After typing C-h a you can type a symbol and a buffer will appear that lists all symbols and functions that match that phrase.

More advanced Emacs hacks (optional):

If you are curious, try some of these once you have finished the rest of the setup instructions. They are unnecessary for any of the work we will do, but may be convenient.

·         Change the colors of your syntax highlighting. In version 24 of Emacs, M-x customize-themes is a good place to start.

·         General customization interface: Open the Options menu and choose the first item under Customize Emacs. This will let you customize Emacs through a sort-of-graphical interface. It saves all your settings in a file in your "home" directory, ~/.emacs.

·         Much, much, much more: Emacs calls itself an extensible editor for a reason.

##  Using the SML/NJ REPL (Read-Eval-Print Loop) in Emacs

At this point, we are done installing! This section shows you how to run SML programs from within Emacs. It assumes you already have an SML file or can write your own SML program in a new one.

·         Edit a file with extension .sml. You should be in SML-mode, using *Tab* to indent your code well.

·         To create the *sml* buffer (which holds the REPL), type C-c C-s (and then *Return/Enter*) in the buffer with the .sml file. (Note: This will not work in the *scratch* buffer that Emacs starts in because this buffer is not in SML Mode.)

·         Keep the .sml file(s) you are working with for a particular assignment in the same folder. When you type C-c C-s to start the REPL from a buffer for foo.sml, the REPL will look in the right folder for foo.sml when you type use "foo.sml"; and will look in the same folder for any other file you use such as foo_tests.sml. This is less confusing than trying to keep track of different folders and paths while using the REPL although that is possible.

·         To end and restart a REPL session, type C-d (to end it) and C-c C-s (and then *Return/Enter*) (to restart it). You must type C-d while in the *sml* buffer; you can type C-c C-s from the *sml* buffer or a buffer with a .sml file.

·         By ending and restarting a session, the new session has an empty environment. Your earlier interactions are still in the *sml* buffer, so you can save them, cut-paste them, etc., but they have no effect on the evaluation in the restarted REPL session.

·         Evaluation can go into an infinite loop.

1.     This has likely occurred if you are not getting the "-" prompt back and nothing appears to be happening.

2.     C-c C-c will interrupt evaluation and get you your prompt back.

·         If you forget to end your binding with a ";" character, the REPL will print an "=" character on the next line, which is just its way of saying, "you are not done -- continue your binding," so type a ";" and hit *Return/Enter*. This is not an infinite loop (nothing is being evaluated; the REPL is waiting for you) so C-c C-c does not do anything.

·         If the printed result looks "pretty good," but part of what you expected to see has been replaced by a "#" or " ..." this is normal. The REPL has a limit on how many characters it prints, which is good since you might make a large value, such as a list with tens of thousands of elements. You can adjust the limit if you want.

·         Two keyboard commands are particularly useful in the REPL: M-p will print the previous line you used in the REPL, which you can then run again or edit before running. Repeating M-p will cycle through previous REPL lines, allowing you to bring back up any of your previous REPL expressions. The p stands for *previous*. M-n(repeatedly) does the same thing in the opposite direction, with the n standing for *next*.

### **Advice You Will Wish You Followed!**

In each REPL session, follow this pattern:

·         First type use "foo.sml"; for any SML files you want to use.

·         Then use the REPL manually as long as you wish.

·         After using the REPL to test something, do *not* use use to load (or reload) any more files.

·         When tempted to violate the previous point, end and restart your REPL session before continuing.

*Why:* use "foo.sml"; has a very simple semantics: it adds the ML bindings in the file to the environment in order. These may or may not shadow ML bindings from the last time you typed use "foo.sml";, depending on how foo.sml changed. This confuses even expert programmers until they train themselves to follow the pattern above.

If you find yourself typing the same non-trivial things repeatedly in the REPL, stop wasting your time.

·         Move the repeated parts to a second file, e.g., test.sml.

·         Then, when you restart your session, begin with use "foo.sml"; use "test.sml";.

·         In fact, there is an even faster way:

1.     Begin test.sml with the expression use "foo.sml";

2.     Then begin your session with use "test.sml";

*Note:* Do *not* put use "foo.sml"; in test.sml *and* begin your session with use "foo.sml"; use "test.sml";. That will evaluate the ML bindings in foo.sml twice, which is confusing.

If you develop some emotional attachment to the transcript of your *sml* buffer, you can save it to a file just like any other buffer. But after you do, it is not an *sml* buffer anymore, so you will have to create a new *sml*buffer from a buffer in SML Mode via C-c C-s.

Acknowledgments: These instructions were prepared starting with material created by Ben Wood, adapted from prior materials by Dan Grossman and Hal Perkins. Stefan Monnier provided fantastic feedback on this document and even created SML Mode version 6 to simplify SML Mode installation substantially. John Reppy provided excellent improvements to the SML/NJ installation instructions.



## Little tips:

you can set the color by the menu bar:

```
"Options" -- "Customize Emacs"  -- "Custom Themes"
```

and also the font:

```
"Options" -- "Set Default Font"
```

finally, save the changes:

```
"Options" -- "Set Default Font"
```

