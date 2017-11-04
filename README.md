Lua Cuckoo Filter Library
-------------------------

## Overview
A bloom filter alternative for approximate set membership tests with deletion support.

## Installation

### Prerequisites
* C compiler (GCC 4.7+, Visual Studio 2013, MinGW (Lua 5.1))
* Lua 5.1, Lua 5.2, or LuaJIT
* [CMake (2.8.7+)](http://cmake.org/cmake/resources/software.html)

### CMake Build Instructions

    git clone https://github.com/mozilla-services/lua_cuckoo_filter.git
    cd lua_cuckoo_filter 
    mkdir release
    cd release
    
    # UNIX
    cmake -DCMAKE_BUILD_TYPE=release ..
    make

    # Windows Visual Studio 2013
    cmake -DCMAKE_BUILD_TYPE=release -G "NMake Makefiles" ..
    nmake

    ctest
    cpack

## Module

### Example Usage
```lua
require "cuckoo_filter"

local cf = cuckoo_filter.new(1000) -- this will be rounded up to 1024
local found = cf:query("test")
-- found == false
cf:add("test")
found = cf:query("test")
-- found == true
```

### API Functions

#### new
```lua
require "cuckoo_filter"
local cf = cuckoo_filter.new(1024)
```

Import the Lua _cuckoo_filter_ via the Lua 'require' function. The module is
globally registered and returned by the require function.

*Arguments*
- items (unsigned) The maximum number of items to be inserted into the filter
  (must be >= 4). Items are grouped into buckets of four and the number of
  buckets will be rounded up to the closest power of two if necessary. The
  fingerprint size is currently fixed at sixteen bits so the false positive rate
  is 0.00012.

*Return*
- cuckoo_filter userdata object.

#### version
```lua
require "cuckoo_filter"
local v = cuckoo_filter.version()
-- v == "0.1.0"
```

Returns a string with the running version of cuckoo_filter.

*Arguments*
- none

*Return*
- Semantic version string

### API Methods

#### add
```lua
local added = cf:add(key)
```

Adds an item to the cuckoo filter. This method works a little differently than
the standard algorithm; a lookup is performed first. Since we must handle
duplicates we consider any collision within the bucket to be a duplicate.

*Arguments*
- key (string/number) The key to add in the cuckoo filter.

*Return*
- True if the key was added, false if it already existed.

#### delete
```lua
local deleted = cf:delete(key)
```

Deletes an item to the cuckoo filter.

*Arguments*
- key (string/number) The key to delete in the cuckoo filter.

*Return*
- True if the key was deleted, false if it didn't exist.

#### query
```lua
local found = cf:query(key)
```

Checks for the existence of the key in the cuckoo filter.

*Arguments*
- key (string/number) The key to lookup in the cuckoo filter.

*Return*
- True if the key exists, false if it doesn't.

#### count
```lua
local total = cf:count()
```

Returns the number of items in the cuckoo filter.

*Arguments*
- none

*Return*
- Returns the number of distinct items currently in the set.

#### clear
```lua
cf:clear()
```

Resets the cuckoo filter to an empty set.

*Arguments*
- none

*Return*
- none
