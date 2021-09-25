## 静态链接规则：从左到右

[Library order in static linking](https://eli.thegreenplace.net/2013/07/09/library-order-in-static-linking)

- linker maintains a symbol table:
    - A list of symbols exported by all the objects and libraries encountered so far.
    - A list of undefined symbols that the encountered objects and libraries requested to import and were not found yet.
- new object file:
    - the symbols it exports: 1) add to the list of exported symbols 2) if any symbol is in the undefined list, remove it. if symbol is in the exported list already, `multiple definition error`
    - the symbol it imports: if not found in the exported list, add to the undefined list
- new library: go over all objects in the library, for_each:
    - If any symbol it exports are on the undefined list, the object is added to the link and next step is executed, otherwise skipped. (well.. important rule)
    - if the object is added to the link, it executed like a object file described above
    - finally, if any of the objects in the library has been included in the link, the library is rescanned again - it's possible that symbols imported by the included objects within the same library.

Points:

1. after the linker has looked at a library, it won't look at it again. 
2. when a library is examined, an object file within it can be left out of the link if it does not provide symbols that the symbol table needs.

> Traditionally, static libraries are compiled without -fPIC on Linux because it usually doesn't have much use and on some architectures there is a small performance penalty for using it. You only need it if you're compiling a shared library using it, whereas I expect most people want to compile their entire executable statically (which does not need the flag).
