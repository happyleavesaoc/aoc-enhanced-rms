# aoc-enhanced-rms

Enhanced RMS (.erms) is an alternative syntax for AoC random map scripts. Enhanced RMS scripts compile to standard RMS.

### Supported features
- Variable substitution
- Boolean expressions
- Special contexts

### Usage
Compile mymap.erms to mymap.rms:
```
$ erms mymap.erms > mymap.rms
```

### Benefits

- Modularity: No need to copy-and-paste with ERMS. Assign shared statements to variables and use them where necessary. Keep shared code in import files to reduce the length of complex maps. Make useful ERMS imports available to others.
- Syntax: ERMS provides a more familiar, modern syntax.

### Syntax
Whitespace is ignored (spaces, tabs, and newlines all qualify as whitespace).
#### Labels
A label is specified in all capitals and may include underscores.
#### Numbers
Numbers must be positive integers.
#### Statements
All statements must be specified in lowercase.
#### Comments
Comments may be added before, after, or between statements.

Single line comment
```
// a comment
```
Multi line comment
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
#### Constants
Setting a constant maps a terrain or object numeric identifier to a label.
```
const(EXAMPLE, 23)
```
#### Blocks
A block is a list of statements grouped together. Blocks can be standalone, or used in conjunction with other components, like `if`, `map`, etc.
```
{
  def(LABEL)
  set_something
  ...
}
```
#### Contexts
A context is a block with a specific functionality. Contexts may be any of the following:
- map
- player_lands
- connect_all_lands
- connect_all_player_lands
- connect_teams_lands
- connect_same_land_zones
- info
- pack

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
- elevation

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
```
if(P) {
  ...
} elseif(Q) {
  ...
} else {
  ...
}
```
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
Operators can be chained to create more complex expressions. The left hand side must be a label. This could be improved in the future.
```
if(P and (Q or Z)) {
  ...
)

if(P or (Q or (Z or X))) {
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
Substitution paired with an attribute
```
  $num = 1
  num_items $num // output: num_items 1
  $something = SOMETHING
  attribute $something // output: attribute SOMETHING
```
Substitution in a def()
```
$label = EXAMPLE
def($label) // output: #define EXAMPLE
```
Substitution as a statement
```
$example = {
  set_something
}

$example // output: set_something
```

#### Import
```
import "path/to/file.erms"
```
Importing an .erms file will merge variables in the file at the specified path into the current file. There must not be any conflicting variable names. An imported file will not affect the compiler output unless the variables it contains are referenced.

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
The `info` context is a structured way to provide metadata about a map or pack. It is represented as a comment in the compiled ouput.

#### Map
```
map {
  ...
}
```
The `map` context encapsulates a single map. It exists for readability and to facilitate packing.

#### Pack
```
pack {
  $map1
  $map2
  ...
}
```
The `pack` context makes it easy to pack multiple maps together. It will automatically compute equal chance per included map, and wrap each map in an appropriate `if` statement. Other statements can also be used in `pack`, but only `map` contexts will be manipulated.

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
    } chance(10%) {
      set(JUNGLE)
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
const(X, N) | #const X N
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
