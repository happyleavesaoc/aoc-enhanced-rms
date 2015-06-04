# aoc-enhanced-rms

Enhanced RMS (.erms) is an alternative syntax for AoC random map scripts. Enhanced RMS scripts compile to standard RMS.

### Supported features
- Variable substitution
- Boolean expressions
- Special constructs

### Usage
Compile mymap.erms to mymap.rms:
```
$ erms mymap.erms mymap.rms
```
### Syntax
Whitespace is ignored (spaces, tabs, and newlines all qualify as whitespace).
#### Labels
A label is specified in all capitals and may include underscores.
#### Numbers
Numbers must be integers.
#### Statements
All statements must be specified in lowercase.
#### Comments
```
// a comment
```
```
/* a longer
  comment
*/
```
### Components
#### Defines
Defining a label makes it evaluate to True when used in an `if` statement.
```
def(EXAMPLE)
```
#### Blocks
A block is a list of statements grouped together. Blocks can be standalone, or used in conjunction with other components, like `if`, `map`, etc.
```
{
  define(LABEL)
  set_something
  ...
}
```
#### Contexts
A context is a block with a specific functionality. Contexts may be any of the following:
- map
- player_lands
- info

Prefix the block with the name of the context. Contexts can be repeated.
```
map {
  ...
}
```
#### Functions
A function is a context that accepts an argument. Functions may be any of the following:
- section
- object
- land
- terrain

The argument is placed after the function name, in parentheses. Functions can be repeated.
```
object(SHEEP) {
  ...
}
```

#### Chance
Statements inside a `chance` function run according to the given percent. Chance functions can be chained or standalone.
```
chance(20%) {
  set_something
}
```

#### Conditionals

Conditionals use the standard `if`, `elseif`, and `else` from RMS. Instead of just evaluating labels, both `if` and `elseif` also support boolean expressions.

##### Operators

and
```
if(P and Q) {
  ...
}
```
or
```
if(P or Q) {
  ...
}
```
not
```
if(not P) {
  ...
}
```
##### Chaining
Operators can be chained to create more complex expressions.
```
if(P and (Q or (not Z))) {
  ...
)

if(P or (Q or (Z or X))) {
  ...
}

if((P or Q) and (Z or X)) {
  ...
}

```

#### Variables
Enhanced RMS variables are placeholders for substitutions. No operations besides assignment can be performed on a variable. Assignments can take the following forms:

Assignment of an integer
```
$example = 1
```
Assignment of a label
```
$example = SOMETHING
```
Assignment of a block
```
$example = {
  set_something
  some_attribute 1
  ...
}
```
Assigment of a context
```
$example = map {
...
}
```
Assignment of a function
```
$example = object(EXAMPLE) {
  ...
}
```
Substitutions can occur as a statement, or paired with an attribute.
```
  $num = 1
  num_items $num // output: num_items 1
  $something = SOMETHING
  attribute $something // output: attribute SOMETHING
```
```
$example = {
  set_something
}

$example // output: set_something
```
### Constructs

#### Import
`import "path/to/file.erms"`
Importing an .erms file will merge the file at the specified path into the current file. There must not be any conflicting variable names.

#### Info
```
info {
  name "Example Map"
  author "HappyLeaves"
  version "1.0"
  created "2015-06-03"
  modified "2015-06-04"
  description "..."
}
```
The `info` construct is structured way to provide metadata about a map or pack. It is represented as a comment in the compiled ouput.

#### Map
```
map {
  ...
}
```
The `map` construct encapsulates a single map, and automatically handles required statements (like ).

#### Pack
```
pack {
  $map1
  $map2
  ...
}
```
The `pack` construct makes it easy to pack multiple maps together. It will automatically split component maps and add the appropriate `if` statements.

### Example

```
$grass = GRASS

$gaia = {
  set_gaia_object_only
  set_place_for_every_player
  set_loose_grouping
}

$arabia = map {
  section(TERRAIN) {
    chance(20%) {
      set(TEMPERATE)
    } chance(40%) {
      set(DESERT)
    } chance() {
      set(jungle)
    }
    if(TEMPERATE or JUNGLE) {
      terrain($grass) {
        base_terrain $grass
        land_percent 50
      }
    } else {
      terrain(DIRT) {
        base_terrain DIRT1
        land_percent 50
      }
    }
  }
  section(OBJECTS) {
    object(SHEEP) {
      $gaia
      number_of_objects 4
    }
  }
}

$baltic = map {
  ...
}

pack {
  $arabia
  $baltic
}

```

### Mapping

ERMS | RMS
--- | --- 
def(X) | #define X
chance(X%) | start_random / percent_chance X / end_random
if(X) | if X
elseif(X) | elseif X
else | else
 | endif
section(X) | \<X_GENERATION\>
X { } | create_X { }
X(Y) { } | create_X Y { }
map |
info |
pack |
import |
// |
/* */ | /* */
$x | 
