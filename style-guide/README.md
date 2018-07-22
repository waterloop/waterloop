[C++ Style Guide]: c++.md
[Wio Style Guide]: wio.md

# General Style Guide.

## Style Terminology Table:

| Style Name            | Single Word Example | Multi Word Example |
| :-------------------: | :-----------------: | :----------------: |
| `camelCase`           | waterloop           | waterloopIsCool    |
| `PascalCase`          | Waterloop           | WaterloopIsCool    |
| `snake_case`          | waterloop           | waterloop_is_cool  |
| `Pascal_Snake_Case`   | Waterloop           | Waterloop_Is_Cool  |
| `CAPITAL_SNAKE_CASE`  | WATERLOOP           | WATERLOOP_IS_COOL  |
| `hyphen-case`         | waterloop           | waterloop-is-cool  |

## Use 90 characters per-line:
This is based on the fact that back in the day 80 characters used to be standard, but now we have bigger screens so we should use them. 100 characters with a medium size font tends to go past more than half the screen for most computers (even a 17" screen). Hence we have 90 that is greater than 80 and less than 100 (perfectly balanced).

## Folder names:
Keep all folder names meaningful and hyphen-case but can be overidden by different guides.
For example:
    - Use [Wio Style Guide] rules for wio target folders.

## File names:
This should be depend on the type of file it is. For example:
    - Use [C++ Style Guide] rules for C/C++ files.

## Use Waterloop copyright notice.
Find a way to put this on top of any file `Copyright 2018 Waterloop` rather than the author names.
For example first line in a c++ file according to [C++ Style Guide] should be:
```cpp
// Copyright 2018 Waterloop.
```

