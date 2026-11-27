# StuLang 26.40.0 language reference

Examples below use the format **feature = code**. All `stu` blocks demonstrate syntax accepted by the 26.40.0 lexer and parser. The [README](../README.md) distinguishes parser support from browser-preview support.

StuLang source files use the `.stu` extension. The parser accepts smart double quotes (`“…”`) and single quotes in addition to normal double quotes.

## Basic syntax

Names are case-sensitive. Statements end with `:` (or `;`). Arguments and array entries use `;`. Function and object blocks end with `):`; views end with `)::`. Actions use `>` and `<`. Hyphens are allowed in names, so put spaces around subtraction (`a - b`). Parenthesize comparisons using `<` or `>` inside actions.

Print func =

```stu
Print("Hello, StuLang!"):
```

`Print` parses as a function call but has no built-in implementation in the preview.

Comments =

```stu
# A single-line comment
#* A multiline
comment *#
```

Variables and declaration kinds =

```stu
Var Int score = 10:
Var Flo price = 2.5:
Var Str message = "Hello":
Var Bool ready = True:
Con limit = 100:
Set other = score:
Var empty = Null:
```

The parser records `Var`, `Con`, and `Set`; it does not enforce their semantics or types. Type labels are `Int`, `Flo`, `Str`, `Chr`, `Bool`, `Ary`, `Map`, and `Auto`. Omitting the type records `Auto`. The browser preview currently treats `Con` as a JavaScript constant and `Var`/`Set` as mutable variables; it does not implement all declaration-kind or type semantics.

Strings and interpolation =

```stu
Var name = "StuLang":
Var greeting = "Hello, **name**!":
Var escaped = "First line\nSecond line":
```

The parser preserves interpolation text; the preview interpolates named variables.

Arrays and maps =

```stu
Var items = [1; 2; 3]:
Var settings = Map("theme" = "dark"; "size" = 12):
Var first = items[0]:
```

Arrays and array literals run in the preview. Map literals and indexed member access parse, but do not execute in the preview.

Operators =

```stu
Var total = (2 + 3) * 4:
Var power = 2 ** 3:
Var remainder = 10 % 3:
Var allowed = True && Not False:
Var either = True Or False:
score += 1:
score++:
```

Supported parser operators include `+ - * / % **`, `== != < > <= >=`, `&& || Or Not`, assignment `= += -= *= /=`, and postfix `++ --`. Exponentiation and assignment associate right to left. The browser preview supports `+ - * / % **`, comparisons, `&& ||`, assignment `=`, and the small expression set used for variables and events. Compound assignments and postfix updates are parser features, not fully implemented preview behavior.

## Functions

Function with return =

```stu
Func Add(a; b): (
    Return(a + b):
):
Var result = Add(10; 20):
```

Default parameter =

```stu
Func AddOne(value = 0): (
    Return(value + 1):
):
Var result = AddOne():
```

Import =

```stu
Imp(Random):
```

Imports are parsed but not resolved. The preview's `Random.Choice` special case needs no import.

Async function =

```stu
Async Func Load(): (
    Return(Await Fetch()):
):
```

## Conditions and loops (parser only)

If / Elif / Else =

```stu
If(score > 10) >
    Print("High"):
<
Elif(score == 10) >
    Print("Equal"):
<
Else >
    Print("Low"):
<
```

Repeat =

```stu
Repeat(3) >
    Print("Hello"):
<
```

While =

```stu
While(score < 10) >
    score += 1:
<
```

For =

```stu
For(i; 0; 10; 1) >
    Print(i):
<
```

The parser accepts three or four arguments and requires an identifier first; runtime range behavior is not implemented.

ForEach =

```stu
ForEach(item; items) >
    Print(item):
<
```

Break and Continue =

```stu
Repeat(10) >
    If(score == 5) >
        Break:
    <
    Continue:
<
```

Try / Catch / Finally / Throw =

```stu
Try >
    Throw("Something went wrong"):
<
Catch(error) >
    Print(error):
<
Finally >
    Print("Finished"):
<
```

## Views, objects, and events

Root view (preview supported) =

```stu
View; FSA; Home: (
)::
```

A root view header has `View`, the `FSA` full-screen layout, and a unique object ID. An object header has `Object`, an object type, optional layout and preset modifiers, and a unique object ID.

The preview supports these object types:

| Object type         | Browser element or behavior                       |
| ------------------- | ------------------------------------------------- |
| `Text-Box`          | Text container                                    |
| `Button`            | Button                                            |
| `Editable-Text-Box` | Text input                                        |
| `SideBar`           | Aside/container                                   |
| `Container`         | Generic container                                 |
| `NavBar`            | Navigation container                              |
| `CheckBox`          | Checkbox                                          |
| `Table`             | Table                                             |
| `Table-Row`         | Table row                                         |
| `Table-Cell`        | Table cell                                        |
| `Table-Header`      | Table header cell                                 |
| `Progress`          | Progress indicator or circular progress indicator |

Text view =

```stu
View; FSA; Home: (
    Object; Text-Box; MS(70%); Default; Greeting: (
        !Text = "Hello, StuLang!":
    ):
)::
```

`!Text` is a property reference. Table and progress structures can be composed from their matching object types and presets.

Button and Clicked event (preview supported) =

```stu
Var count = 0:
View; FSA; Counter: (
    Object; Text-Box; Default; Label: (
        !Text = "Count: **count**":
    ):
    Object; Button; BMS(30%); Rounded; Increment: (
        !Text = "Add one":
        ?Clicked >
            count = count + 1:
        <
    ):
)::
```

Named-variable text bindings refresh after a click handler.

Hover and unhover events (preview supported) =

```stu
Object; Text-Box; Label: (
    !Text = "Hover over me":
    ?Hovered > !Text = "You are hovering"<
    ?Unhovered > !Text = "Hover over me"<
):
```

`?Hovered` runs on pointer entry and `?Unhovered` runs on pointer exit. Either handler can update `!Text` or other supported properties. These events work alongside `?Clicked`.

Editable text and object properties (preview supported) =

```stu
View; FSA; Home: (
    Object; Editable-Text-Box; ML(20%); Default; Input: (
        !Text = "Type here":
        Set text = User-Text:
        ?Clicked >Set-Var(message; Input; User-Text)<
    ):
    Object; SideBar; Right(10%); Color(grey); Default; Output: (
        !Text = "You Typed **message**":
    ):
)::
```

`!Text` supplies the input's initial value; `Set text = User-Text` keeps `text` updated as the user types. `Set-Var(message; Input; User-Text)` copies the named input's current value into `message` when the event runs. If `message` has no declaration, the preview initializes it to an empty string. In this example, click the input after typing to copy the value. See [interactive.stu](../examples/interactive.stu) for the full example.

Checkbox =

```stu
Object; CheckBox; Toggle; Enabled: (
    !Checked = True:
):
```

Table =

```stu
Object; Table; DataTable; StripedTable; Results: (
    Object; Table-Row; HeaderRow: (
        Object; Table-Header; NameHeader: ( !Text = "Name": ):
    ):
    Object; Table-Row; ValueRow: (
        Object; Table-Cell; NameCell: ( !Text = "StuLang": ):
    ):
):
```

Progress =

```stu
Object; Progress; ProgressBar; Download: (
    !Value = 35:
    !Max = 100:
):
```

Navigation and containers =

```stu
Object; NavBar; FloatingNav; Navigation: ():
Object; Container; Card; Panel: ():
Object; SideBar; SidebarNav; Menu: ():
```

Supported `!` properties are `Text`, `Placeholder`, `Value`, `Max`, `Checked`, `Title`, `Duration`, and any entry in the styling-property registry.

### Page navigation

Navigation works in preview events and functions:

```stu
View; FSA; Home: (
    Object; Button; About: (
        !Text = "About":
        ?Clicked > Navigate("about.stu"): <
    ):
)::
```

| Call                              | Effect                            |
| --------------------------------- | --------------------------------- |
| `Navigate("about.stu")`           | Open another StuLang page         |
| `Navigate("https://example.com")` | Open an external website          |
| `Navigate.Back()`                 | Go back                           |
| `Navigate.Forward()`              | Go forward                        |
| `Navigate.Reload()`               | Reload the current page           |
| `Navigate.NewTab("about.stu")`    | Open a page in a new tab          |
| `Navigate.Replace("home.stu")`    | Replace the current history entry |

Local paths are relative to the current page. `STU run` serves `.stu` pages in the starting file's directory and its subdirectories; the starting page is loaded when the command starts, and linked pages are loaded on request. Restart to pick up edits to the starting page. For compiled HTML, compile each linked `.stu` file separately to the corresponding `.html` path (for example, `about.stu` to `about.html` alongside the current output). Browsers may block new tabs without a user action.

In the VS Code preview, local pages open in the preview using their current editor contents, including unsaved edits; new tabs open as separate preview panels. External HTTP(S) sites open in the default browser and do not become part of the preview's history. Query strings and fragments are supported for browser paths but not local VS Code preview paths. Only navigate to trusted pages.

## Presets

Object headers accept case-sensitive, built-in presets without imports. Presets stack from left to right; later presets override earlier ones, header modifiers override presets, and explicit properties override both.

```stu
Object; Button; MS(30%); Primary; Rounded; Elevated; Submit: (
    !Text = "Submit":
    !Background = Orange:
    !Radius = 20px:
):
```

The shared registry provides these presets:

- General: `Default`, `Minimal`, `Modern`, `Flat`, `Rounded`, `Pill`, `Outline`, `Glass`, `Soft`, `Custom`.
- Containers: `Card`, `Elevated`, `Bordered`, `Floating`, `Inset`, `Panel`, `Tile`, `Dashboard`, `Widget`, `Section`.
- Buttons: `Primary`, `Secondary`, `Success`, `Danger`, `Warning`, `Dark`, `Light`, `Ghost`, `LinkButton`, `IconButton`.
- Text: `Hero`, `Heading`, `Subheading`, `Paragraph`, `Caption`, `Label`, `Quote`, `Code`, `Highlight`, `Muted`.
- Navigation: `Bar`, `Nav`, `StickyNav`, `FloatingNav`, `SidebarNav`, `Tabs`, `Breadcrumb`, `Menu`, `DropdownMenu`, `Dock`.
- Forms: `StandardInput`, `FilledInput`, `OutlineInput`, `RoundedInput`, `UnderlineInput`, `SearchInput`, `PasswordInput`, `NumberInput`, `Toggle`, `FormGroup`.
- Feedback: `SuccessAlert`, `ErrorAlert`, `WarningAlert`, `InfoAlert`, `Toast`, `Badge`, `Chip`, `Tooltip`, `Skeleton`, `Loading`.
- Effects: `Gradient`, `Neon`, `Glassmorphism`, `Neumorphism`, `Retro`, `Brutalist`, `Cyberpunk`, `Material`, `Metallic`, `Transparent`.
- Data: `DataTable`, `StripedTable`, `CompactTable`, `BorderedTable`, `StatCard`, `Metric`, `ProgressBar`, `CircularProgress`, `Timeline`, `ListItem`.
- Sections: `HeroSection`, `Footer`, `Header`, `Banner`, `PricingCard`, `ProductCard`, `ProfileCard`, `Testimonial`, `Gallery`, `FeatureCard`.

`Custom(Bg="#FF6600"; Round=12px)` adds custom styles at its position in the preset stack. Alternatively, use `Custom` and explicit properties. Styling properties include `Background`, `Color`, `Radius`, `Padding`, `Margin`, `Gap`, `Border`, `Width`, `Height`, `Font-Family`, `Font-Size`, `Font-Weight`, and `Box-Shadow`; the same names can be used as single-argument header modifiers. `Bg` and `Round` are aliases.

The complete preview property registry also supports `Opacity`, `Display`, `Alignment`, `Overflow`, `Cursor`, `Transition`, `Animation`, `Transform`, and `Backdrop-Filter`. Property names and aliases are case-sensitive. Property assignments can set static values or expressions:

```stu
Object; Container; Home: (
    !Background = "#111827":
    !Opacity = 0.8:
    !Text = "Visible":
):
```

### Positioning

Bare position presets place an object at one of 25 anchor points in its parent. The point names map to the 5-by-5 percentage grid; the first coordinate is horizontal (`x`) and the second is vertical (`y`):

| Row    | Left          | Middle-left     | Center          | Middle-right    | Right           |
| ------ | ------------- | --------------- | --------------- | --------------- | --------------- |
| Top    | `TL` (0, 0)   | `TML` (25, 0)   | `TMS` (50, 0)   | `TMR` (75, 0)   | `TR` (100, 0)   |
| Upper  | `UTL` (0, 25) | `UML` (25, 25)  | `UMS` (50, 25)  | `UMR` (75, 25)  | `UTR` (100, 25) |
| Middle | `ML` (0, 50)  | `MML` (25, 50)  | `MS` (50, 50)   | `MMR` (75, 50)  | `MR` (100, 50)  |
| Lower  | `LBL` (0, 75) | `LML` (25, 75)  | `LMS` (50, 75)  | `LMR` (75, 75)  | `LBR` (100, 75) |
| Bottom | `BL` (0, 100) | `BML` (25, 100) | `BMS` (50, 100) | `BMR` (75, 100) | `BR` (100, 100) |

```stu
View; FSA; Home: (
    Object; Button; TMS; TopButton: ( !Text = "Top center": ):
    Object; Button; XY(30%; 60%); CustomPoint: ( !Text = "30% across, 60% down": ):
    Object; Button; MS; Offset(10px; 20px); ShiftedCenter: ( !Text = "Offset from center": ):
)::
```

Positioning controls:

| Syntax                    | Effect                                                                              |
| ------------------------- | ----------------------------------------------------------------------------------- |
| `XY(30%; 60%)`            | Absolute position at percentages of the parent                                      |
| `XY(100px; 200px)`        | Absolute position at pixel coordinates                                              |
| `Offset(10px; 20px)`      | Translate from the current position or anchor                                       |
| `Left(20%)`, `Right(20%)` | Place in the left or right region using the given width                             |
| `Top(10%)`, `Bottom(10%)` | Place in the top or bottom region using the given height                            |
| `Fill`                    | Stretch to fill the parent                                                          |
| `Auto`                    | Use normal document flow                                                            |
| `Cell(2; 3)`              | Place in grid row 2, column 3 (one-based, 1–5); enables a 5-by-5 grid on the parent |
| `Fixed`                   | Use fixed viewport positioning                                                      |
| `Sticky`                  | Stick to the top while scrolling                                                    |

Percentage anchor forms such as `MS(70%)` and `BMS(30%)` position at the same point as the bare anchor and set the width. `MS` and `MS(70%)` center on both axes inside the parent. Bare anchor presets such as `MS` and coordinate form `XY(...)` provide explicit point positioning.

Input presets require `Editable-Text-Box`; `Toggle` requires `CheckBox`; table presets require `Table`; progress presets require `Progress`; `IconButton` requires `Button`; and `DropdownMenu` requires `Container`, `SideBar`, or `NavBar`. Unknown or incompatible presets cause an error during preview or compilation. Syntax checking alone does not resolve presets.

`PasswordInput`, `NumberInput`, and `SearchInput` select input types. `DropdownMenu` uses an expandable native disclosure; `Toggle` is a checkbox switch. `Toast` hides after 5 seconds by default; `!Duration` specifies milliseconds. `Progress` accepts `!Value` and `!Max`. `Tooltip` uses the text as a native tooltip, or accepts `!Title`. Loading animations respect reduced-motion preferences.

Random choice (preview supported) =

```stu
Var result = Random.Choice(["Heads"; "Tails"]):
```

Custom condition event (parser only) =

```stu
?Custom_if(score > 10) >
    Print("High score"):
<
```

Async event (parser only) =

```stu
Async ?Clicked >
    Load():
<
```

Timed action (parser only) =

```stu
Time.Wait(1s) >
    Print("Later"):
<
```

Units (parser only outside supported percentage headers) =

```stu
Var width = 100px:
Var delay = 250ms:
Var angle = 90deg:
```

Recognized units are `rem`, `deg`, `px`, `em`, `vh`, `vw`, `ms`, and `s`. Percentage headers such as `MS(70%)` work in the preview.

Raw JavaScript =

```stu
Var message = "Hello":
JS.Run_JS {
    const greeting = message + " from JavaScript";
}:
View; FSA; Home: (
    Object; Text-Box; Label: ( !Text = "**greeting**": ):
)::
```

The preview, `run`, and `compile` execute JavaScript blocks in the browser. JavaScript can read and update visible StuLang variables whose names are valid JavaScript identifiers. Top-level JavaScript declarations in a block are available to subsequent StuLang statements and JavaScript blocks in the same scope; they cannot duplicate another declaration in that scope. JavaScript declarations inside nested blocks remain local; use a top-level declaration to share a value. JavaScript runs with browser APIs and the preview's content security policy, not Node.js APIs. Only run code you trust. A literal `</script` is rejected in JavaScript blocks to keep the generated HTML safe.

## JavaScript API

Parse source =

```js
const { tokenize } = require('./src/lexer');
const { parse } = require('./src/parser');

const source = 'Var score = 2 ** 3:';
const tokens = tokenize(source, { filename: 'example.stu' });
const ast = parse(source, { filename: 'example.stu' });
console.log(tokens, ast);
```

AST nodes have a `type`, location information in `loc`, and node-specific fields. Tokens include line, column, and source offsets. The parser separates syntax from runtime behavior.

## Controls, animation, and image backgrounds

Position and appearance are independent: use `Object; Text-Box; MS; Bar; Title` to center a bar-styled object. `Bar` replaces `Default` in the style slot; it does not replace `MS`. Names are case-sensitive.

`Slider` and `Switch` are native input object types. Alternatively use the `Slider` preset on `Editable-Text-Box`, or `Switch` on `CheckBox`. Sliders accept `!Min`, `!Max`, `!Step`, and `!Value`; switches accept `!Checked`. Use `?Input` for live updates and `?Changed` for committed changes, with `User-Value` (number) or `User-Checked` (boolean). `!Title` supplies a control label.

`FadeIn`, `SlideIn`, `Pulse`, and `Spin` add basic animations. Reduced-motion preferences disable them. `Transparent` removes the background color. PNG backgrounds accept HTTP(S) URLs or embedded PNG data URLs (local images must be embedded as data URLs).

```stu
Var volume = 50:
Var enabled = False:
View; FSA; Demo: (
    Object; Slider; MS; Volume: (
        !Min = 0: !Max = 100: !Step = 1: !Value = 50:
        !Title = "Volume":
        ?Input > volume = User-Value <
    ):
    Object; Switch; BMS; Power: (
        !Title = "Power": !Checked = False:
        ?Changed > enabled = User-Checked <
    ):
    Object; Container; TMS; Transparent; FadeIn; Artwork: (
        !Width = 200px: !Height = 100px:
        !Background-Image = "https://example.com/art.png":
        !Background-Size = cover:
        !Background-Position = center:
        !Background-Repeat = no-repeat:
    ):
)::
```

## Stackable style catalog

Use any of the following styles in an object header. Names are case-sensitive. Separate styles with semicolons or commas. The last style wins for overlapping CSS properties (including animation); independent properties combine. Position modifiers such as `MS` override preset positioning, and explicit body properties override presets. `Bar` and `Navbar` are compatible styles; neither replaces a position anchor. `NavBar` is the object type, while `Navbar` is its stackable appearance preset.

```stu
View; FSA; Home: (
    Object; Container; MS(80%); Bar, Navbar, Rounded, Soft-Shadow; Navigation: (
        Object; Button; Ghost, Hover-Grow, Hover-Glow; HomeButton: (
            !Text = "Home":
        ):
        Object; Button; Primary, Pill, Fade-In; StartButton: (
            !Text = "Get started":
        ):
    ):
)::
```

- **Base and color:** `Default`, `Minimal`, `Modern`, `Classic`, `Flat`, `Material`, `Outline`, `Solid`, `Ghost`, `Soft`, `Primary`, `Secondary`, `Accent`, `Success`, `Danger`, `Warning`, `Info`, `Light`, `Dark`, `Neutral`.
- **Shape and border:** `Rounded`, `Extra-Rounded`, `Pill`, `Circle`, `Square`, `Borderless`, `Thin-Border`, `Thick-Border`, `Dashed-Border`, `Dotted-Border`.
- **Shadow:** `No-Shadow`, `Soft-Shadow`, `Medium-Shadow`, `Heavy-Shadow`, `Inner-Shadow`, `Outer-Shadow`, `Elevated`, `Floating`, `Pressed`, `Glow`.
- **Text:** `Bold`, `Italic`, `Underline`, `Strike`, `Uppercase`, `Lowercase`, `Capitalize`, `Monospace`, `Wide-Text`, `Compact-Text`.
- **Surface:** `Glass`, `Frosted`, `Transparent`, `Translucent`, `Neon`, `Retro`, `Pixel`, `Gradient`, `Metallic`, `Neumorphic`.
- **Size and spacing:** `Tiny`, `Small`, `Medium`, `Large`, `Extra-Large`, `Full-Width`, `Full-Height`, `Compact`, `Spacious`, `Auto-Fit`.
- **Hover and focus:** `Hover-Grow`, `Hover-Shrink`, `Hover-Glow`, `Hover-Lift`, `Hover-Darken`, `Hover-Lighten`, `Hover-Fade`, `Hover-Rotate`, `Hover-Underline`, `Hover-Shadow`.
- **Animation:** `Fade-In`, `Fade-Out`, `Slide-Up`, `Slide-Down`, `Slide-Left`, `Slide-Right`, `Bounce`, `Pulse`, `Shake`, `Spin`.
- **Component appearance:** `Card`, `Panel`, `Bar`, `Navbar`, `Badge`, `Chip`, `Banner`, `Tooltip`, `Dialog`, `Container`.

Hover styles restore the original appearance on pointer exit or focus loss. Motion effects use independent CSS transforms so centered anchors stay centered. Reduced-motion preferences disable animations and hover movement. `Slide-Up` enters from below, `Slide-Down` from above, `Slide-Left` from the right, and `Slide-Right` from the left. `Fade-Out` ends invisible; `Bounce`, `Pulse`, and `Spin` repeat. `Dialog` is a visual preset, not a modal or focus trap. `Full-Height` requires a parent with a defined height.

## Object API (26.40.0)

The following properties and commands execute in compiled HTML, the playground,
and the VS Code preview. Define target objects before executing commands against
them. Bare object IDs, quoted IDs, and object references returned by getters are
accepted. A declared variable in an argument is evaluated; otherwise a bare name
such as `Red`, `Rounded`, or `PlayButton` is treated as a symbolic string.
Names remain case-sensitive; CSS keyword values such as `Center` are normalized.

### Additional properties

Place these assignments inside an object. They also work as setter/getter calls,
such as `Width(PlayButton; 200px)` and `Width(PlayButton)`.

```stu
!Alignment = Center:
!Text-Align = Center:
!Vertical-Align = Middle:
!Line-Height = 1.5:
!Letter-Spacing = 2px:
!Word-Spacing = 4px:
!Text-Indent = 10px:
!Text-Overflow = Ellipsis:
!Text-Wrap = True:
!Text-Shadow = "0px 2px 4px black":
!Font-Style = Italic:
!Min-Width = 100px:
!Max-Width = 500px:
!Min-Height = 50px:
!Max-Height = 300px:
!Aspect-Ratio = "16/9":
!Z-Index = 10:
!Border-Color = Red:
!Border-Width = 2px:
!Border-Style = Dashed:
!Padding-Top = 10px:
!Padding-Bottom = 10px:
!Padding-Left = 10px:
!Padding-Right = 10px:
!Margin-Top = 10px:
!Margin-Bottom = 10px:
!Margin-Left = 10px:
!Margin-Right = 10px:
!Flex-Direction = Row:
!Flex-Wrap = Wrap:
!Justify-Content = Center:
!Align-Items = Center:
!Align-Self = Start:
!Flex-Grow = 1:
!Flex-Shrink = 0:
!Grid-Columns = 3:
!Grid-Rows = 3:
!Column-Gap = 10px:
!Row-Gap = 10px:
!Pointer-Events = None:
!Visibility = Hidden:
```

`Alignment` and `Text-Align` align text, including text inside flex containers;
they do not move the object itself. `Vertical-Align` uses native alignment on
inputs and table cells and a column flex layout for other containers. Use
`!Display = Flex:` for flex layout. Positive integer grid counts enable grid and
create equally sized tracks; quoted CSS track definitions are also accepted.
`Text-Wrap = True` uses normal wrapping; `False` uses `nowrap`. Ellipsis needs a
constrained width, `!Overflow = Hidden:`, and usually `!Text-Wrap = False:`.
CSS sizing, flex, and grid rules still apply; properties can override each other.

### Property modification and retrieval (1–20)

```stu
Text(PlayButton; "Start Game"):
Color(PlayButton; Red):
Background(PlayButton; Blue):
Width(PlayButton; 200px):
Height(PlayButton; 100px):
Radius(PlayButton; 15px):
Opacity(PlayButton; 0.5):
Font-Size(PlayButton; 24px):
Text-Align(PlayButton; Center):
Z-Index(PlayButton; 10):
Var text = Text(PlayButton):
Var width = Width(PlayButton):
Var height = Height(PlayButton):
Var color = Color(PlayButton):
Var x = X(PlayButton):
Var y = Y(PlayButton):
Var value = User-Text(Input):
Var checked = Checked(CheckBox1):
Var visible = Is-Visible(PlayButton):
Var type = Type(PlayButton):
```

Style getters return CSS strings; `X`/`Y` return viewport pixel coordinates.
`Checked` and `Is-Visible` return booleans. Visibility queries inspect the object's
hidden flag, display, and visibility, not occlusion or hidden ancestors.
Setters replace that property's previous binding.

### Visibility and state (21–30)

```stu
Show(PlayButton):
Hide(PlayButton):
Toggle(PlayButton):
Enable(PlayButton):
Disable(PlayButton):
Focus(PlayButton):
Unfocus(PlayButton):
Lock(PlayButton):
Unlock(PlayButton):
Reset(PlayButton):
```

Show/hide/toggle control the HTML hidden flag. Explicit `Visibility = Hidden` or
`Display = None` must also be cleared to reveal an object. Enable/disable use the
native disabled state (for controls). Lock sets read-only and inert; unlock clears
them. Reset restores the initial appearance, text, value, checked and enabled
state and removes property bindings; it does not recreate deleted children.

### Positioning (31–40)

```stu
Move(PlayButton; MS):
Move(PlayButton; 50%; 30%):
Move-X(PlayButton; 100px):
Move-Y(PlayButton; 50px):
Offset(PlayButton; 10px; 20px):
Center(PlayButton):
Move-To(PlayButton; OtherObject):
Move-Above(PlayButton; OtherObject):
Move-Below(PlayButton; OtherObject):
Swap-Position(PlayButton; OtherObject):
```

Moves use absolute positioning within the parent. Anchor moves use the same 25
anchors as headers. Coordinate moves retain any existing anchor transform;
`Offset` sets an independent translation. Relative-object moves align visible
bounding rectangles and account for rotation/scale. Above/below touch the target
edge; swap exchanges visible top-left positions. Numeric lengths use pixels.

### Dynamic objects and children (41–50)

```stu
Delete(PlayButton):
Clone(PlayButton; NewButton):
Rename(PlayButton; StartButton):
Replace(PlayButton; NewObject):
Move-Into(PlayButton; Container1):
Add-Child(Container1; PlayButton):
Remove-Child(Container1; PlayButton):
Clear-Children(Container1):
Var count = Child-Count(Container1):
Var child = Get-Child(Container1; 0):
```

These are independent examples, not a sequence to run on one deleted object.
Clone makes a DOM snapshot with a new ID; descendant IDs become
`NewButton-OriginalChildID`. It copies appearance/content, not event handlers or
reactive bindings; attach those to the new ID explicitly. Rename changes registry
lookup, so subsequent calls use the new ID. Replace moves an existing unrelated
object into the old object's place and deletes the old subtree. Remove-Child
detaches without deleting, so Add-Child can reattach it. Delete and Clear-Children
unregister removed subtrees. Child queries count immediate StuLang objects, not
internal text spans; Get-Child is zero-based and returns null out of range.

### Style management (51–60)

```stu
Add-Style(PlayButton; Rounded):
Remove-Style(PlayButton; Rounded):
Toggle-Style(PlayButton; Dark):
Has-Style(PlayButton; Primary):
Clear-Styles(PlayButton):
Set-Styles(PlayButton; Primary; Pill; Bold):
Copy-Style(PlayButton; OtherButton):
Reset-Style(PlayButton):
Add-Hover-Style(PlayButton; Glow):
Add-Focus-Style(PlayButton; Outline):
```

Styles use the built-in preset registry. Set-Styles replaces the ordered stack;
Copy-Style copies the second object's inline appearance onto the first.
Reset-Style restores initial inline styles and clears CSS property bindings.
Explicit live property bindings take precedence over added presets. Dynamic
styles affect appearance; use object types/headers for structural behaviors such
as dropdowns and specialized inputs. Hover/focus styles restore the previous
appearance when the pointer/focus leaves.

### Animation (61–70)

```stu
Animate(PlayButton; Fade-In; 500ms):
Animate(PlayButton; Fade-Out; 500ms):
Animate(PlayButton; Bounce; 1s):
Animate(PlayButton; Shake; 300ms):
Rotate(PlayButton; 90deg):
Scale(PlayButton; 1.5):
Stop-Animation(PlayButton):
Pause-Animation(PlayButton):
Resume-Animation(PlayButton):
Animate-To(PlayButton; XY(50%; 50%); 1s):
```

Animate uses the named animation preset and supplied duration; repeating presets
remain repeating. Animate-To uses the browser animation API to tween left/top to
the destination. Pause/resume/stop control both forms. Rotate and scale are
independent transforms. Reduced-motion preferences disable movement animations.

### External events (71–80)

```stu
On-Click(PlayButton) > Text(PlayButton; "Clicked"): <
On-Hover(PlayButton) > Color(PlayButton; Blue): <
On-Unhover(PlayButton) > Color(PlayButton; Red): <
On-Double-Click(PlayButton) > Text(PlayButton; "Double click"): <
On-Change(Input) > Text(PlayButton; User-Text(Input)): <
On-Show(PlayButton) > Text(PlayButton; "Shown"): <
On-Hide(PlayButton) > Text(PlayButton; "Hidden"): <
On-Create(PlayButton) > Text(PlayButton; "Ready"): <
On-Resize(PlayButton) > Text(PlayButton; "Resized"): <
On-Animation-End(PlayButton) > Text(PlayButton; "Finished"): <
```

Register events after the target exists. Create runs after initial properties and
bindings are initialized. Resize uses ResizeObserver, including its first size
notification. Show/hide notifications come from the API or visibility bindings,
not arbitrary DOM edits. Animation-end runs for naturally completed animations;
infinite or cancelled animations do not finish. Handlers refresh bindings and
report errors in the preview's error area.

### Reactive bindings (81–90)

```stu
Bind(PlayButton; Text; score):
Bind(PlayButton; Color; currentColor):
Bind(PlayButton; Width; currentWidth):
Unbind(PlayButton; Text):
Bind-Visibility(PlayButton; isVisible):
Bind-Enabled(PlayButton; isReady):
Bind-Value(Input; username):
Bind-Value(Slider1; volume):
Bind-Checked(CheckBox1; enabled):
Bind-Value(Progress1; progress):
```

Declare each bound variable first. Bind is one-way. Value/checked bindings also
listen for input/change events and write into the variable; sliders produce
numbers. Progress has no user input, so its binding is effectively one-way; set
`!Max = 100:` when using percentages. Bindings refresh after StuLang events and
before object calls. Arbitrary asynchronous JavaScript mutations do not trigger
an automatic refresh. Unbind accepts the property name (`Visible`, `Enabled`,
`Value`, or `Checked` for specialized bindings).

### Utilities and groups (91–100)

```stu
Exists(PlayButton):
Get-Object("PlayButton"):
Get-All(Button):
Get-By-Style(Primary):
Group(MenuButtons; Button1; Button2):
Hide(MenuButtons):
Show(MenuButtons):
Add-Style(MenuButtons; Rounded):
Overlaps(PlayButton; OtherObject):
Distance-To(PlayButton; OtherObject):
```

Get-Object returns an object reference or null. Get-All/Get-By-Style return arrays
of references, also accepted as command targets. Group stores a named collection;
commands apply to each member, and getters return results in member order.
Overlaps compares visible bounding rectangles; touching edges are not overlap.
Distance-To returns the Euclidean distance between rectangle centers in pixels.
Unknown objects, duplicate IDs, incompatible presets, and malformed command
argument counts report errors.
