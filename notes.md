3/26/21 - Approx 2 hours, sections 1-18
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
iex(9)> deck
["Ace", "Two", "Three"]
iex(10)> 

Immutability in Elixir:
we never modify an existing data structure in place, we always make a NEW data structure

------------
In Elixir it is convention to include a question mark at the end of a method name if it is expected to return a boolean, it doesn't not actually affect behavior

https://hexdocs.pm/elixir/Enum.html#member?/2
iex(10)> recompile  
Compiling 1 file (.ex)
:ok
iex(11)> deck = Cards.create_deck
["Ace", "Two", "Three"]
iex(12)> Cards.contains?(deck, "Ace")
true
iex(13)> Cards.contains?(deck, "King")
false

-----------
List Comprehension - how we run a for loop in Elixir
For every element in the list, do this thing 
The syntax for a comprehension is a mapping function
For every element in the suits array, we run the do block, and whatever is returned from that do block gets put into a brand new array

    for value <- values do 
      for suit <- suits do 
        "#{value} of #{suit}"
      end
    end
Nesting comprehensions means that each time the inner comprehension is run, it returns an array of its data, so we get back an array of arrays for each pass of the inner comprehesion, which is not what we want. Note we are not actually working with arrays, we are ACTUALLY working with linked Lists.

  def create_deck do 
    values = ["Ace", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten"]
    suits = ["Spades", "Clubs", "Hearts", "Diamonds"]

    cards = for value <- values do 
      for suit <- suits do 
        "#{value} of #{suit}"
      end
    end
    List.flatten(cards)
  end
First solve is to use the built in flatten method.

  for suit <- suits, value <- values do 
    "#{value} of #{suit}"
  end

Second solve, 'double upped' the comprehension

--------------
A tuple is like an array, but each index has a very special meaning, to the developers that wrote it
For example, in our deal method 
Enum.split(deck, hand_size) -> { [My hand], [The rest of the cards] }
My hand will always be at index 0, and the rest of the cards will always be at index 1

3/27/21 - 1 hour, Sections 18-24
Pattern Matching: Elixir's replacement for variable assignment
{ hand, rest_of_deck } = Cards.deal(deck, 5)  
  Elixir notices that we have a tuple on the left and right, because the data structure and number of elements exactly match for both sides, it knows to assign that variable name
  We create a mirror structure and number of elements for Elixir to pattern match against
Pattern Matching is used anytime the = sign is used
[ color1, color2 ] = ["red", "blue"]
color1 => "red"
colors = ["red", "blue"]
colors => ["red", "blue"]
[ color1, color2, color3 ] = ["red", "blue"]
color3 => throws error, color3 is undefined

Elixir's Relationship with Erlang
Code we write is fed into Elixir, which is transpiled into Erlang, then its compiled and executed as BEAM (Bogdan Erlang Abstract Machine)
Elixir is a dialect of Erlang, to get away from the annoying syntax of Erlang, makes it easier for programmers
BEAM is the virtual machine that executes the Erlang code

when working with file system we actually have to use Erlang as opposed to Elixir
invoke the Erlang library with :erlang
we are able to save a deck as a binary object to the file system
In Elixir when we want to make checks we try to avoid if statements and more often rely on case statements which are a combo of checks and pattern matching

In Elixir :ok, :error are examples of primitive data types called atoms, used for handling control flows, errors, messages, you can think of them as strings

More on Pattern Matching: if on the left hand side you have a hard coded value, Elixir requires that the right hand side have an identical value
["red", color] = ["green", "blue"] //=> returns an error
["red", color] = ["red", "blue"] //=> success

  def load(filename) do 
    case File.read(filename) do 
      {:ok, binary} -> :erlang.binary_to_term binary
      {:error, _reason} -> "That file does not exist"
    end
  end

Note that here with the tuple, of :ok, binary we are actually performing two operations, it is using the atom :ok to find which tuple, and assigning the second part of the tuple to binary due to pattern matching
For :error, _reason, the variable is not used so if we have 'reason' we get a warning of an unused variable, but we can't remove it because then the pattern match fails, so in Elixir we can add an _ to indicate that we know there will be a variable here, but we're not going to use it.

The Pipe Operator
Requires that you use methods that use the same first argument throughout
Whatever is applied from the previous function, will be used as the first argument of the next function
  def create_hand(hand_size) do 
    #deck = Cards.create_deck
    #deck = Cards.shuffle(deck)
    #hand = Cards.deal(deck, hand_size)

    Cards.create_deck
      |> Cards.shuffle
      |> Cards.deal(hand_size)
  end
  ----------------
  Adding dependencies:
  mix.exs, 
    defp deps do
    [
      {:ex_doc, "~> 0.12"} *this is what we added
      # {:dep_from_hexpm, "~> 0.3.0"},
      # {:dep_from_git, git: "https://github.com/elixir-lang/my_dep.git", tag: "0.1.0"}
    ]
  end
then to add it, $ mix deps.get
Had to install Hex, selected Y

DOCUMENTATION
$ mix docs 
generates:
Compiling 1 file (.ex)
Generating docs...
View "html" docs at "doc/index.html"
View "epub" docs at "doc/cards.epub"
$ cd doc
$ open index.html

In browser creates beautiful documentation that looks very similar to Elixir's official documentation
Adding documentation for an Example requires very specific spacing, see deal example.

TESTING
Building the project comes with a robust testing suite
$ mix test

Two types of tests
1. testing a singular and particular fact using an assertion
2. Doc Testing
  - any examples you have in your module or function docs, the Elixir test will run the code in the example as if it were real, and check that the outcome matches
  - doc testing is amazingly productive, get great testing and documentation in one fell swoop