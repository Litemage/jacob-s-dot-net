---
title: "Formatting C Code With Clang Format"
description: "My personal clang-format file which implements my taste in a style guide for C/C++ projects."
author: "Jacob Simeone"
pubDate: "2025-01-15"
updatedDate: "2025-01-15"
coverImage: ""
tags: ""
---

# Why Format Code?

If you have written any length of a scripting programming language, you can probably already appreciate how difficult it can be to maintain a consistent style, especially as projects grow to tens or hundreds of files. That kind of blows, right? Especially because a consistent style is *very* important for clarity and general neatness of the program, which can aid in debugging your code, or new developers easily understanding what is going on.

Especially when you reach production code-bases, your code's functionality and technical completeness is equally important to the quality in which it is written. My favorite example of this? Take the donut-shaped-C-code-that-generates-a-spinning-donut program:

```c
             i,j,k,x,y,o,N;
         main(){float z[1760],a
      #define R(t,x,y) f=x;x-=t*y\
   ;y+=t*f;f=(3-x*x-y*y)/2;x*=f;y*=f;
   =0,e=1,c=1,d=0,f,g,h,G,H,A,t,D;char
 b[1760];for(;;){memset(b,32,1760);g=0,
h=1;memset(z,0,7040);for(j=0;j<90;j++){
G=0,H=1;for(i=0;i<314;i++){A=h+2,D=1/(G*
A*a+g*e+5);t=G*A        *e-g*a;x=40+30*D
*(H*A*d-t*c);y=          12+15*D*(H*A*c+
t*d);o=x+80*y;N          =8*((g*a-G*h*e)
*d-G*h*a-g*e-H*h        *c);if(22>y&&y>
 0&&x>0&&80>x&&D>z[o]){z[o]=D;b[o]=(N>0
  ?N:0)[".,-~:;=!*#$@"];}R(.02,H,G);}R(
  .07,h,g);}for(k=0;1761>k;k++)putchar
   (k%80?b[k]:10);R(.04,e,a);R(.02,d,
     c);usleep(15000);printf('\n'+(
        " donut.c! \x1b[23A"));}}
           /*3D-spinning-donut

// Original credit: https://www.a1k0n.net/2011/07/20/donut-math.html
```

Now you tell me: what on earth is going in that code? Well, you probably can't tell from this! Just in this donut, there are lots of optimizations that make it very well-written, but you probably could not tell that by looking at it in the donut form. If you have the time, re-format the code and look at it, and read up about it on [Andy Sloane's website](https://www.a1k0n.net/2011/07/20/donut-math.html).

That being said: Is anyone ever going to write something like this in production? No, probably not, this is hyperbole to really drive the point home that formatting *does* matter to others viewing your program.

> *If you ever find something like this in production, contact me, and we'll cry together.*

So, what would be a solution to this problem? Well, you could just keep a consistent style and go through all of your source code and style it the way you would like. However, we are programmers after all, and these are the kind of things we tend to automate... enter formatting tools.

# Clang-Format

I'm sure there are a lot of C/C++ language formatters out there, but `clang-format` has been my go-to for a few years now, and I don't see it going anywhere. If you are un-aware, [Clang](https://clang.llvm.org/) is a "front-end" compiler build for the [LLVM](https://www.llvm.org/) project. Put simply: LLVM is a collection of reusable tools designed for "languages in the C language family" for use in compiling code, and clang uses this toolset.

When you install the Clang compiler, you also get a couple extra tools as part of the project, one of those tools is [clang-format](https://releases.llvm.org/10.0.0/tools/clang/docs/ClangFormat.html). The `clang-format` tool is designed to be used as a stand-alone or integrated tool for formatting your C/C++ source code automatically, and maintaining a consistent style. (Note this is different from a [static analyzer](https://clang-analyzer.llvm.org/), which warns users about bugs before compile-time). Personally, I use both the `clang-format` CLI tool and, and a [VsCode extension](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format) that integrates `clang-format` into the IDE.

Calling the `clang-format` executable causes it to crawl through your code and perform all of the formatting for you! So if some inferior human goes and writes a function like this:

```c
         int



 foo(
    uint8_t *pBuf

            ){ if 
            
            
            (pBuf 
            != NULL){*pBuf = 
            
            0;}  }
```

Then `clang-format` would be more than happy to turn it into:

```c
int
foo(uint8_t *pBuf)
{
    if (pBuf != NULL)
    {
        *pBuf = 0;
    }
}
```

# My Style

Naturally, all of these formatting options are very much to-taste. `clang-format` solves this by allowing users to specify a `".clang-format"` file in the root directory of your project, which will hold your style rules. Honestly, there is no right or wrong style guide. The only thing that matters here is consistency. Use whatever style has come before you, and if you are picking a new one, you can sample from mine. There are a few style guides out there to take inspiration from:

- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
- [GNU C Style](https://www.gnu.org/prep/standards/html_node/Writing-C.html)
- [Linux Kernel Coding Style](https://www.kernel.org/doc/html/v4.10/process/coding-style.html)

And many more...

This is my personal `".clang-format"` file:

```
---
Language: Cpp
# BasedOnStyle:  LLVM
AccessModifierOffset: -2
AlignAfterOpenBracket: Align
AlignArrayOfStructures: None
AlignConsecutiveMacros:
  Enabled: true
  AcrossEmptyLines: true
  AcrossComments: true
AlignConsecutiveAssignments: None
AlignConsecutiveBitFields:
  Enabled: true
  AcrossEmptyLines: true
  AcrossComments: true
AlignConsecutiveDeclarations: None
AlignEscapedNewlines: Right
AlignOperands: Align
InsertBraces: true # Control statements must have curly brackets
AlignTrailingComments: true
AllowAllArgumentsOnNextLine: true
AllowAllParametersOfDeclarationOnNextLine: true
AllowShortEnumsOnASingleLine: true
AllowShortBlocksOnASingleLine: Empty
AllowShortCaseLabelsOnASingleLine: false
AllowShortFunctionsOnASingleLine: All
AllowShortLambdasOnASingleLine: All
AllowShortIfStatementsOnASingleLine: Never
AllowShortLoopsOnASingleLine: false
AlwaysBreakAfterDefinitionReturnType: None
AlwaysBreakAfterReturnType: AllDefinitions
AlwaysBreakBeforeMultilineStrings: false
AlwaysBreakTemplateDeclarations: Yes
AttributeMacros:
  - __capability
BinPackArguments: false
BinPackParameters: false
BraceWrapping:
  AfterCaseLabel: false
  AfterClass: false
  AfterControlStatement: Never
  AfterEnum: false
  AfterFunction: false
  AfterNamespace: false
  AfterObjCDeclaration: false
  AfterStruct: false
  AfterUnion: false
  AfterExternBlock: false
  BeforeCatch: false
  BeforeElse: false
  BeforeLambdaBody: false
  BeforeWhile: false
  IndentBraces: false
  SplitEmptyFunction: true
  SplitEmptyRecord: true
  SplitEmptyNamespace: true
BreakBeforeBinaryOperators: NonAssignment
BreakBeforeConceptDeclarations: true
BreakBeforeBraces: Allman
BreakBeforeInheritanceComma: false
BreakInheritanceList: BeforeColon
BreakBeforeTernaryOperators: true
BreakConstructorInitializersBeforeComma: false
BreakConstructorInitializers: BeforeColon
BreakAfterJavaFieldAnnotations: false
BreakStringLiterals: true
ColumnLimit: 120
CommentPragmas: "^ IWYU pragma:"
QualifierAlignment: Leave
CompactNamespaces: false
ConstructorInitializerIndentWidth: 4
ContinuationIndentWidth: 4
Cpp11BracedListStyle: true
DeriveLineEnding: true
DerivePointerAlignment: false
DisableFormat: false
EmptyLineAfterAccessModifier: Never
EmptyLineBeforeAccessModifier: LogicalBlock
ExperimentalAutoDetectBinPacking: false
PackConstructorInitializers: BinPack
BasedOnStyle: ""
ConstructorInitializerAllOnOneLineOrOnePerLine: false
AllowAllConstructorInitializersOnNextLine: true
FixNamespaceComments: true
ForEachMacros:
  - foreach
  - Q_FOREACH
  - BOOST_FOREACH
IfMacros:
  - KJ_IF_MAYBE
IncludeBlocks: Preserve
IncludeCategories:
  - Regex: "^<(.*)>"
    Priority: 0
  - Regex: '^"(.*)"'
    Priority: 1
  - Regex: "(.*)"
    Priority: 2
IncludeIsMainRegex: "(Test)?$"
IncludeIsMainSourceRegex: ""
IndentAccessModifiers: false
IndentCaseLabels: true
IndentCaseBlocks: false
IndentGotoLabels: true
IndentPPDirectives: None
IndentExternBlock: AfterExternBlock
IndentRequires: true
IndentWidth: 3
IndentWrappedFunctionNames: false
InsertTrailingCommas: Wrapped
JavaScriptQuotes: Leave
JavaScriptWrapImports: true
KeepEmptyLinesAtTheStartOfBlocks: true
LambdaBodyIndentation: Signature
MacroBlockBegin: ""
MacroBlockEnd: ""
MaxEmptyLinesToKeep: 1
NamespaceIndentation: None
ObjCBinPackProtocolList: Auto
ObjCBlockIndentWidth: 2
ObjCBreakBeforeNestedBlockParam: true
ObjCSpaceAfterProperty: false
ObjCSpaceBeforeProtocolList: true
PenaltyBreakAssignment: 2
PenaltyBreakBeforeFirstCallParameter: 19
PenaltyBreakComment: 300
PenaltyBreakFirstLessLess: 120
PenaltyBreakOpenParenthesis: 0
PenaltyBreakString: 1000
PenaltyBreakTemplateDeclaration: 10
PenaltyExcessCharacter: 1000000
PenaltyReturnTypeOnItsOwnLine: 60
PenaltyIndentedWhitespace: 0
PointerAlignment: Right
PPIndentWidth: -1
ReferenceAlignment: Pointer
ReflowComments: false
RemoveBracesLLVM: false
SeparateDefinitionBlocks: Always
ShortNamespaceLines: 1
SortIncludes: false
SortJavaStaticImport: Before
SortUsingDeclarations: true
SpaceAfterCStyleCast: false
SpaceAfterLogicalNot: false
SpaceAfterTemplateKeyword: true
SpaceBeforeAssignmentOperators: true
SpaceBeforeCaseColon: false
SpaceBeforeParens: ControlStatements
SpaceBeforeParensOptions:
  AfterControlStatements: true
  AfterForeachMacros: true
  AfterFunctionDefinitionName: false
  AfterFunctionDeclarationName: false
  AfterIfMacros: true
  AfterOverloadedOperator: false
  BeforeNonEmptyParentheses: false
SpaceAroundPointerQualifiers: Default
SpaceBeforeRangeBasedForLoopColon: true
SpaceInEmptyBlock: false
SpaceInEmptyParentheses: false
SpacesBeforeTrailingComments: 1
SpacesInAngles: Never
SpacesInConditionalStatement: false
SpacesInContainerLiterals: true
SpacesInCStyleCastParentheses: false
SpacesInLineCommentPrefix:
  Minimum: 1
  Maximum: -1
SpacesInParentheses: false
SpacesInSquareBrackets: false
SpaceBeforeSquareBrackets: false
BitFieldColonSpacing: Both
Standard: Latest
StatementAttributeLikeMacros:
  - Q_EMIT
StatementMacros:
  - Q_UNUSED
  - QT_REQUIRE_VERSION
TabWidth: 8
UseCRLF: false
UseTab: Never
WhitespaceSensitiveMacros:
  - STRINGIZE
  - PP_STRINGIZE
  - BOOST_PP_STRINGIZE
  - NS_SWIFT_NAME
  - CF_SWIFT_NAME
SpaceBeforeCpp11BracedList: false
SpaceBeforeCtorInitializerColon: true
SpaceBeforeInheritanceColon: true
---
```