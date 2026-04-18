# `The BCS`

`“BCS” stands for “Beryllium Community Standards.” It covers style, naming, the zen of Beryllium, etc. It is separated into separate documents.`

# `BCS 1`

`This part covers style and naming conventions.`

| Formatting Rule | Standard Name |
| :---: | :---: |
| No tab characters for indentation; only 2, 4, or 8 spaces | Indentation Width & Character |
| Column limit is 120 characters; individual project overrides allowed | Maximum Line Length |
| Same-line braces for **if**, **loops**, and functions | Bracing Style |
| Function names cannot be on a newline after the keyword | Function Declaration Placement |
| Variable names cannot be on a newline after the keyword | Variable Declaration Placement |
| Chained method calls: one line if under limit, else one per line indented | Method Chaining Style |
| Content inside a namespace must be indented | Namespace Indentation |
| Multi-line function calls: indent one level; closing paren on new line | Function Call Multiline Formatting |
| Multi-line arg/generic lists: one indent after declaration; closing bracket aligned | List Alignment |
| No space before colon, one space after colon | Type Annotation Spacing |
| Binary operators must have spaces around them | Operator Whitespace |
| Multi-line functions: space between each; space between single-lines | Whitespaces (Functions) |
| Spaces between classes are same as multi-line functions | Whitespaces (Classes) |
| Single-line functions: one statement only, no vertical spacing | Inline Function Constraints |
| All functions must be **snake\_case** | Naming Conventions (Functions) |
| All variables must be **snake\_case** | Naming Conventions (Variables) |
| All types (including enums) must be **PascalCase** | Naming Conventions (Types) |
| Enum members and constants must be **SCREAMING\_SNAKE\_CASE** | Naming Conventions (Constants) |

`However, if there are previous codebases and the only reason for changing would be to keep in style with this, keep the style the same in those codebases.`

`*this document uses tab and indent interchangeably to mean a shift right of code, and it only uses tab characters because google docs makes that far easier`  
`**constants are things like pi which are guaranteed to never change. Even though the language makes things immutable by default, for example a generic local variable might need to be mutable eventually, so it is still a variable`

# `BCS 2`

`This is the Zen of Beryllium.`

1. `Explicit is better than implicit.`  
2. `Safety exists by default.`  
3. `Ownership kind is explicit.`  
4. `Visibility is not hierarchy.`  
5. `Zero-cost abstractions should be used wherever possible.`  
6. `Pragmatism should be prioritized over following a random document.`  
7. `Namespacing is important.`