# todostack.el

## General use

Todostack is a trivial (and experimental -- use at your own risk) to-do
list manager for a particular way of doing things that may or may not
appeal to you.  It uses a stack as a to-do list system.  The idea is to
reduce the effect of distractions.  When you have to switch tasks,
push what you are currently doing onto the stack with the command
`todostack-push`, then when you return, inspect the stack with
`todostack`.  When you have finished the task currently on the top of
the stack, pop it off with `todostack-pop`.

Command `todostack-list` will list the whole current stack in order
from top to bottom.  It uses the value of `todostack-list-function` to
decide how to do it, so you can easily write your own list function
which should be a function of zero arguments.  Three such functions
are pre-defined: First, `todostack-list-buffer` and
`todostack-list-echo-area` -- the first creates a buffer and lists the
stack there; the second does what it sounds like.  The default,
however, is `todostack-list-dwim` which tries to decide if the list
will fit in the echo area and if so, displays it there using the first
of those functions, and if not displays it in a buffer using the
second of those functions.

You can also use the command `todostack-procrastinate` to push back the
top item on the list by one space (or farther with a prefix arg) and
you can use `todostack-rotate` to send the top item all the way to the
back.  You can use `todostack-queue` to add something to the bottom of
the stack instead of the top, which lets you treat the stack more like
a queue.

Commands `todostack-save` and `todostack-load` will save and load the
todostack to a file specified in the variable `todostack-save-file`.
The default is `~/.emacs.d/todostacksave.el`.  Also,
`todostack-backup` will make a backup copy of the current stack in a
file in the same directory and same name with an extention named by
variable `todostack-backup-extension`.  This is a good function
to add to `todostack-before-save-hook` -- so good, in fact, that I
have made it the default and set the extension to ".bak".  See also
`todostack-rescue`.  Finally, if you want to hack on it, see
`todostack-test`.

## Loading

You can use the customize interface if you like it. I use the
following in my .emacs.  The hooks allow my stack to persist across
Emacs sessions and in case of a crash, by aggressively saving the
stack whenever I modify it.  I strongly recommend their use:

     (load-file "/PATH/TO/todostack.el")
     (load-file todostack-save-file)
     (add-hook 'kill-emacs-hook 'todostack-save)
     (add-hook 'emacs-startup-hook 'todostack-load)
     (add-hook 'todostack-post-op-hook 'todostack-save)

I also bind some of the most-used functions to keys of my choice.

## Other functions

You can have multiple todo stacks, but can operate on only one at a
time -- whichever is named by the variable `todostack-current-stack`.
The default is the aptly-named `todostack-default-stack`.  A stack is
simply a Lisp list, so you need only initialize a symbol to nil and
then set `todostack-current-stack` to point to it.  But REMEMBER TO
ALSO CHANGE `todostack-save-file`, or else your old saved todostack
will get clobbered the first time you try to save the new stack.  For
example:

     (defvar mystack '())
     (setq todostack-current-stack 'mystack)
     (setq todostack-save-file "~/mystacksave.el")

## Org-mode integration

Org-mode users may find it useful to use command `todostack-org-snarf`
to convert a buffer with org TODO items into a stack.  See the doc
string for `todostack-org-snarf' for more information.  You will
probably want to set or customize the following:

     (setq todostack-list-function 'todostack-list-buffer)
     (setq todostack-list-line-prefix "* TODO ")
     (add-hook 'todostack-post-list-buffer-hook 'org-mode)


