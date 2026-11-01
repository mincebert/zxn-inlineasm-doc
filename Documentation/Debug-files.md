# Debug files

Use `SLD "program.sld"` in the `.asm` dot command to create optional
source-level debugging information. The file uses
[SjASMPlus SLD version 1 format](https://z00m128.github.io/sjasmplus/documentation.html#c_sld_data)
and can be used by tools that support that format. It includes labels and
equates, along with traces for assembled instructions. Instructions before
the `SLD` directive are included too.

Source locations refer to the source file and its physical line number,
including for nested includes. BASIC input is identified as `BASIC` and uses
BASIC line numbers. Instructions expanded from a macro refer to the macro's
invocation location, not its definition; macro definition locations and
statement columns are not included. Instructions separated by `:` have
separate traces but refer to the same source line.

SLD does not include mutable `VAR` symbols or traces for data directives such
as `DB`, `DW`, `DS`, and `INCBIN`. `BRK` and `NBRK` do have instruction traces.
For banked code, traces identify the physical 8K memory page: `BANK n` selects
pages `2*n` and `2*n+1`, and the bank offset determines the page. `ORG` changes
the execution address but not the page selected by `BANK`. Without `BANK`, the
entry MMU mapping is used.
