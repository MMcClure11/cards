3/26/21
To Create a Project:
$ mix new name-of-project

In lib cards, automatically generated defMod Cards do end
most of elixir is based on Modules with collections of methods or functions

iex : works just like irb for ruby, runs the shell
-S mix compiles the project and we can reference it

Elixir has an implicit return, the last value in a function will automatically be returned.

To run your file in the elixir interactive shell: 
$ iex -S mix

The elixir shell does not automatically recompile:
$ recompile

To close the shell, ctrl + C twice

Design Pattern:
Elixir is a Functional Programming language
a module has no idea about what a class or an instance variable is, they do not exist
Modules are collections of methods which you can call on primitive data types to return some kind of result
---------------------------
Shuffle: error troubleshooting - If you call Cards.shuffle() without an argument passed in you get the following error:
function Cards.shuffle/0 is undefined or private. Did you mean one of:

      * shuffle/1

which means that it expected one argument but got none, this is because in Elixir you 
can have methods by the same name that take in different numbers of arguments

arity - the number of arguments a method accepts, our shuffle argument has an arity of 1

The Enum Module: https://hexdocs.pm/elixir/Enum.html
used for working with any kind of list
https://hexdocs.pm/elixir/Enum.html#shuffle/1 

iex(4)> recompile
Compiling 1 file (.ex)
:ok
iex(5)> deck = Cards.create_deck
["Ace", "Two", "Three"]
iex(6)> deck
["Ace", "Two", "Three"]
iex(7)> Cards.shuffle(deck)
["Two", "Three", "Ace"]
iex(8)> Cards.shuffle(deck)
["Ace", "Three", "Two"]