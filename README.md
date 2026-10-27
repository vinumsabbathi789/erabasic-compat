# eraBasic compatibility suite

This is a small game that tests how an eraBasic interpreter behaves:
Emuera, Emuera-EM/EE, emuera-tty, emuera.ts, uEmuera and others. It
checks the language and the built-in commands and functions, and shows
any result that differs from what Emuera-EM/EE does. The expected values
are Emuera-EM/EE's, with the default configuration. Where its behaviour
is surprising, a comment in the test says so (for example, `&`, `|` and
`^` share one precedence level and group left to right).

## Where it lives

- https://gitgud.io/era-games/erabasic-compat: the main repository, for
  issues and merge requests (gitgud.io needs an account to read).
- https://github.com/vinumsabbathi789/erabasic-compat: a mirror anyone
  can clone, e.g. as a git submodule of an interpreter's repository.

If a check fails in Emuera-EM/EE, the check is wrong: please report it.

## Running it

Run the folder like any game, with no `emuera.config`, or one with the
default settings.

- **Emuera / Emuera-EM / EE:** copy `Emuera.exe` into this folder and
  run it (it isn't included).
- **emuera-tty:** `emuera-tty compat` (or `emuera-tty compat --headless`
  for plain text).
- **Others:** start the game in this folder the way that interpreter
  starts games.

It needs no input until the end. Each check prints one line:

    OK  core.flow: for leaves the counter at the end
    NG  core.header: define without parentheses is textual  got [10] want [8]

At the end it prints the totals and every failed check again, writes the
screen to `emuera.log` (OUTPUTLOG), and waits for a key.

## Topics

| File | Topic | What it covers |
|---|---|---|
| `CORE_HEADER.ERB` | core.header | `#DEFINE`, global `#DIM` (CONST, initial values, 2-D/3-D) from `COMPAT.ERH` |
| `CORE_SYNTAX.ERB` | core.syntax | comments, `{ }` line joining, `[SKIPSTART]`, `[IF_DEBUG]`, case-insensitive names, full-width spaces, number literals, assignment forms |
| `CORE_OPERATORS.ERB` | core.operators | 64-bit wrapping, `/` and `%` signs, shifts, operator precedence, short-circuit evaluation, string operators |
| `CORE_FLOW.ERB` | core.flow | IF, SELECTCASE, FOR/WHILE/DO/REPEAT, BREAK/CONTINUE, GOTO, TRY…, CALLFORM, JUMP, RESTART |
| `CORE_FUNCTIONS.ERB` | core.functions | arguments and defaults, RETURN/RESULT, `#FUNCTION(S)`, static and DYNAMIC locals, REF, recursion |
| `CORE_VARIABLES.ERB` | core.variables | default array sizes, GAMEBASE, CSV names and indices, initial values (TARGET 1, ASSI -1, EJAC…), RAND bounds |
| `CORE_ARRAYS.ERB` | core.arrays | VARSET, ARRAYSORT/SHIFT/REMOVE/COPY, SUMARRAY, FINDELEMENT, MATCH… |
| `CORE_STRINGS.ERB` | core.strings | Shift_JIS byte lengths vs character lengths, SUBSTRING, STRFIND, regex functions, conversions |
| `CORE_FORM.ERB` | core.form | `%…%`, `{…}`, widths and alignment, `\@ ? # \@`, escapes |
| `CORE_MATH.ERB` | core.math | ABS…LIMIT, POWER, SQRT/LOG/EXPONENT rounding, bit statements, TIMES |
| `CORE_CHARA.ERB` | core.chara | CHARA*.CSV templates, ADDCHARA…PICKUPCHARA, FINDCHARA, SORTCHARA (MASTER fixed, TARGET follows) |
| `CORE_CONSOLE.ERB` | core.console | colours, font styles, alignment, REDRAW, LINEISEMPTY, SKIPDISP |
| `EM_FUNCTIONS.ERB` | em.functions | GETVAR/SETVAR, EXISTFUNCTION, GETMETH, TRYCALLF, ARRAYMSORTEX, VARSETEX… |
| `EM_MAPS.ERB` | em.maps | MAP_* |
| `EM_XML.ERB` | em.xml | XML_* |
| `EM_DATATABLE.ERB` | em.datatable | DT_* |

The `core.*` topics use only what vanilla Emuera (1821 and later) has.
The `em.*` topics use Emuera-EM/EE extensions and are called with
TRYCALL, so an interpreter without them can delete the `EM_*.ERB` files.
The run then reports them as `SKIP`.

## What it can't test

- Errors. A script error stops an Emuera game, so the suite only checks
  things that work. "Division by zero is an error" and "reading past the
  end of an array is an error" have to be checked separately.
- Input, waits, layout and pictures: anything that needs a person, a
  window size or a font. (HTML_STRINGLEN, for example, depends on the
  font size.)
- Saves. Whether saves are byte-identical to Emuera's has to be checked
  against files Emuera wrote.

## Adding a check

Put it in the topic file it belongs to:

    CALL CHECK, "what", <condition>
    CALL CHECKN, "what", <got>, <want>      ; integers: shows both on failure
    CALL CHECKS, "what", <got>, <want>      ; strings

Don't name a variable or argument with a single letter: A to Z are
Emuera's own variables, and a `#DIM` of one is an error. Nor use a
`#DEFINE` in `COMPAT.ERH`: Emuera expands macros only in ERB files.

Remember that the CHECK calls are CALLs too: each one sets RESULT to 0.
So read RESULT into the first CHECK straight after the statement that
set it.

Check every expected value against Emuera-EM/EE (the source at
https://gitlab.com/EvilMask/emuera.em, or running it), not against the
interpreter you are working on. Files are UTF-8 with a BOM, which every
Emuera reads.
