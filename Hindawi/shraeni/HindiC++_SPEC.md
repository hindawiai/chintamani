# HindiC++ Language Specification

## 1. Scope

This document specifies HindiC++, the Hindawi Shaili Shraeni language implemented by the translator sources in this repository.

HindiC++ is a source-to-source language. A conforming HindiC++ program is translated to C++ source by `h2cpp` and compiled by an implementation-provided C++ compiler through `shraenicc`.

The normative implementation sources are:

| Role | File |
| --- | --- |
| HindiC++ to C++ translator | `Hindawi/shraeni/h2cpp.uhin` |
| C++ to HindiC++ translator | `Hindawi/shraeni/cpp2h.uhin` |
| Compiler driver | `Hindawi/shraeni/shraenicc` |
| Translator build rules | `Hindawi/shraeni/Makefile` |
| Dispatcher | `Hindawi/hindrv/hincc.awk` |

## 2. Conformance

A HindiC++ implementation shall provide:

1. The `h2cpp` translator generated from `Hindawi/shraeni/h2cpp.uhin`.
2. A compiler driver equivalent to `shraenicc`.
3. A C++ compiler accepting the C++ source emitted by `h2cpp`.
4. The Hindawi encoding filters used by the driver pipeline.

A conforming HindiC++ program shall:

1. Use the HindiC++ style directive before program text when compiled through `hincc`.
2. Contain source text accepted by the HindiC++ lexical substitution rules.
3. Translate to a C++ translation unit accepted by the selected C++ compiler.

Behavior not specified by this document is the behavior of the emitted C++ program under the selected C++ implementation.

## 3. Processing Model

### 3.1 Style Dispatch

When compiled through `hincc`, a HindiC++ source file shall begin with a style directive:

```text
<शैली श्रेणी>
```

After normalization to the internal Hindawi representation, the dispatcher recognizes the equivalent style token:

```text
shraee_nnee>
```

The dispatcher invokes `shraenicc` for this style.

### 3.2 Translation Pipeline

The compiler driver performs this translation:

```text
HindiC++ source
  -> acii2uni
  -> iconv UTF-16 to UTF-8
  -> h2cpp
  -> tempfil0123.tmphin.cpp
  -> g++
  -> hin.exe
```

The translator `h2cpp` is a Flex scanner. It does not parse C++ grammar. It performs lexical substitution and passes unmatched text through according to the generated scanner behavior.

### 3.3 Reverse Translation

The reverse translator `cpp2h` maps supported C++ tokens to HindiC++ tokens. It is a source translation aid, not a semantic validator.

## 4. Source File

A HindiC++ source file is a text file accepted by the Hindawi driver pipeline. The repository sample files use the `.uhin` extension.

The driver expects the source to be readable by the Hindawi Unicode and internal-encoding filters. Source code entering `h2cpp` is UTF-8 text.

## 5. Lexical Model

### 5.1 Token Replacement

HindiC++ translation is defined as lexical replacement over a token stream.

For every HindiC++ terminal defined by `h2cpp.uhin`, the translator shall emit the corresponding C++ terminal. For tokens not matched by a HindiC++ terminal rule, the scanner default behavior applies.

### 5.2 String And Character Literals

Single-quoted and double-quoted literals are preserved before keyword replacement.

Consequently, HindiC++ keywords appearing inside character or string literals shall not be translated.

### 5.3 Identifiers

Identifiers that are not HindiC++ reserved terminals are preserved by the scanner behavior. A program may use Hindi identifiers when the emitted C++ compiler accepts the resulting source after translation.

### 5.4 Whitespace

Whitespace has the same syntactic role as in the emitted C++ translation unit.

### 5.5 Comments

HindiC++ does not define an independent comment syntax in Shaili Shraeni beyond tokens that are emitted to C++ by the scanner or preserved as unmatched input. C++ comment syntax may be used where it passes through to the emitted C++ program.

## 6. Syntax

HindiC++ syntax is C++ syntax after replacing HindiC++ terminals with their C++ equivalents.

This specification therefore defines HindiC++ grammar by translation:

```text
hindi-cpp-translation-unit:
    optional-style-directive hindi-cpp-token-sequence

hindi-cpp-token-sequence:
    sequence of terminals which, after HindiC++ lexical replacement,
    forms a valid C++ translation unit
```

The grammar, overload resolution, object model, template semantics, exception semantics, access control, linkage, storage duration, and undefined behavior rules are those of the selected C++ compiler for the emitted C++ source.

## 7. Preprocessing

HindiC++ supports C/C++ preprocessing directives by terminal substitution:

| HindiC++ | C++ |
| --- | --- |
| `#समावेश` | `#include` |
| `#घोषणा` | `#define` |
| `#अगर` | `#if` |
| `#अगर_घोषित` | `#ifdef` |
| `#अगर_अघोषित` | `#ifndef` |
| `#या_अगर` | `#elif` |
| `#अन्यथा` | `#else` |
| `#पूर्ण_अगर` | `#endif` |
| `#त्रुटि` | `#error` |
| `#पंक्ति` | `#line` |
| `#आशय` | `#pragma` |
| `#अघोषित` | `#undef` |

Header-name aliases include:

| HindiC++ | C++ |
| --- | --- |
| `मानकपन.स` | `stdio.h` |
| `मानककोष.स` | `stdlib.h` |
| `माला.स` | `string.h` |
| `गणित.स` | `math.h` |
| `मानकघोष.स` | `stddef.h` |
| `मानकतर्क.स` | `stdarg.h` |
| `संकेत.स` | `signal.h` |
| `समय.स` | `time.h` |
| `पनप्रवाह.स` | `iostream.h` |
| `पन_प्रवाह` | `iostream` |
| `खप्रवाह.स` | `fstream.h` |
| `मप्रवाह.स` | `strstream.h` |
| `मप्रवाह_स` | `sstream` |

The complete set of header aliases is the set of header-name rules in `h2cpp.uhin`.

## 8. Declarations

HindiC++ declaration syntax follows C++ declaration syntax after terminal replacement.

Fundamental declaration terminals include:

| HindiC++ | C++ |
| --- | --- |
| `प्रकार` | `typedef` |
| `बाह्य` | `extern` |
| `अटल` | `static` |
| `रेजिस्टर` | `register` |
| `स्वतः` | `auto` |
| `स्थिर` | `const` |
| `अस्थिर` | `volatile` |
| `अंतरभूत` | `inline` |
| `मित्र` | `friend` |
| `भव` | `virtual` |

Type-name terminals include:

| HindiC++ | C++ |
| --- | --- |
| `व्योम` | `void` |
| `अक्षर` | `char` |
| `लघु` | `short` |
| `पूर्णांक` | `int` |
| `दीर्घ` | `long` |
| `भग्न` | `float` |
| `द्विगुन` | `double` |
| `चिन्हित` | `signed` |
| `अचिन्हित` | `unsigned` |
| `माप_प्रकार` | `size_t` |
| `ब_अक्षर_प्रकार` | `wchar_t` |

Aggregate and user-defined type terminals include:

| HindiC++ | C++ |
| --- | --- |
| `काष्ठा` | `struct` |
| `जोड़` | `union` |
| `क्रमागत` | `enum` |
| `श्रेणी` | `class` |
| `ढाँचा` | `template` |

## 9. Classes

Class definitions, inheritance, member declarations, constructors, destructors, virtual functions, overloads, and templates follow C++ rules after terminal replacement.

Access-specifier terminals are:

| HindiC++ | C++ |
| --- | --- |
| `खुला` | `public` |
| `निजी` | `private` |
| `रक्षित` | `protected` |

Object-model terminals include:

| HindiC++ | C++ |
| --- | --- |
| `यह` | `this` |
| `नया` | `new` |
| `मिटाओ` | `delete` |
| `चालक` | `operator` |

## 10. Functions

Function declarations and definitions follow C++ rules after terminal replacement.

Common function-related terminals include:

| HindiC++ | C++ |
| --- | --- |
| `मुख्य` | `main` |
| `वापस` | `return` |
| `बहु_सूची` | `va_list` |
| `बहु_शुरू` | `va_start` |
| `बहु_तर्क` | `va_arg` |
| `बहु_पूर्ण` | `va_end` |

## 11. Statements

HindiC++ statement syntax follows C++ statement syntax after terminal replacement.

Statement terminals include:

| HindiC++ | C++ |
| --- | --- |
| `अगर` | `if` |
| `अन्यथा` | `else` |
| `चयन` | `switch` |
| `हाल` | `case` |
| `शेष` | `default` |
| `क्रम` | `for` |
| `जबतक` | `while` |
| `करो` | `do` |
| `अवरोध` | `break` |
| `जारी` | `continue` |
| `जाओ` | `goto` |
| `वापस` | `return` |
| `प्रयास` | `try` |
| `पकड़ो` | `catch` |
| `फेंको` | `throw` |

## 12. Expressions And Operators

Expression syntax and precedence are C++ expression syntax and precedence after terminal replacement.

Most operator spellings are emitted through `orictxt` tokens in `h2cpp.uhin`. These tokens represent punctuation and operators that may be difficult to express directly through the Hindawi transliteration pipeline.

Normative operator-token mappings include:

| HindiC++ token | C++ |
| --- | --- |
| `orictxt1` | `!` |
| `orictxt2` | `!=` |
| `orictxt21` | `%` |
| `orictxt22` | `%=` |
| `orictxt23` | `&` |
| `orictxt24` | `&&` |
| `orictxt25` | `&=` |
| `orictxt26` | `*` |
| `orictxt27` | `*=` |
| `orictxt28` | `+` |
| `orictxt29` | `++` |
| `orictxt30` | `+=` |
| `orictxt31` | `-` |
| `orictxt32` | `--` |
| `orictxt33` | `-=` |
| `orictxt34` | `->` |
| `orictxt35` | `->*` |
| `orictxt36` | `.` |
| `orictxt37` | `.*` |
| `orictxt38` | `/` |
| `orictxt39` | `/=` |
| `orictxt40` | `::` |
| `orictxt41` | `<` |
| `orictxt42` | `<<` |
| `orictxt43` | `<<=` |
| `orictxt44` | `>` |
| `orictxt45` | `>>` |
| `orictxt46` | `>>=` |
| `orictxt47` | `?:` |
| `orictxt832` | `|=` |
| `orictxt833` | `||` |
| `orictxt835` | `~` |

The following punctuation tokens are also defined:

| HindiC++ token | C++ |
| --- | --- |
| `अणु` | `{` |
| `पूर्ण` | `}` |
| `orictxt361` | `[` |
| `orictxt362` | `[]` |
| `orictxt376` | `]` |
| `orictxt363` | `\` |
| `orictxt364` | `\"` |
| `orictxt365` | `\'` |
| `orictxt366` | `\?` |
| `orictxt367` | `\\` |

## 13. Escape Sequences

Escape sequence aliases are:

| HindiC++ | C++ |
| --- | --- |
| `\च` | `\a` |
| `\म` | `\b` |
| `\प` | `\f` |
| `\न` | `\n` |
| `\ल` | `\r` |
| `\ट` | `\t` |
| `\ख` | `\v` |
| `\ष` | `\x` |

## 14. Standard Library Names

HindiC++ provides aliases for C and C++ standard library identifiers. These aliases are lexical substitutions and do not define library behavior independently of the selected C++ implementation.

Representative library aliases include:

| HindiC++ | C++ |
| --- | --- |
| `म_लिखो` | `printf` |
| `म_पूछो` | `scanf` |
| `ख_खोलो` | `fopen` |
| `ख_बंद` | `fclose` |
| `ख_लिखो` | `fprintf` |
| `ख_पूछो` | `fscanf` |
| `म_माप` | `strlen` |
| `म_नकल` | `strcpy` |
| `म_भेद` | `strcmp` |
| `दो_स्मृति` | `malloc` |
| `मुक्त` | `free` |
| `वर्ग_मूल` | `sqrt` |
| `घात` | `pow` |
| `प्रणाली` | `system` |

C++ stream aliases include:

| HindiC++ | C++ |
| --- | --- |
| `अ_बाहर` | `cout` |
| `अ_अंदर` | `cin` |
| `अ_त्रुटि` | `cerr` |
| `प_पूर्ण` | `endl` |
| `सफाई` | `flush` |
| `ब_प्रवाह` | `ostream` |
| `अ_प्रवाह` | `istream` |
| `ख_प्रवाह` | `fstream` |
| `अख_प्रवाह` | `ifstream` |
| `बख_प्रवाह` | `ofstream` |
| `लो_पंक्ति` | `getline` |

The complete library alias vocabulary is the set of identifier rules in `h2cpp.uhin`.

## 15. Predefined Macro Aliases

| HindiC++ | C++ |
| --- | --- |
| `__दिन__` | `__DATE__` |
| `__खाता__` | `__FILE__` |
| `__पंक्ति__` | `__LINE__` |
| `__मानक__` | `__STDC__` |
| `__समय__` | `__TIME__` |

## 16. Diagnostics

`shraenicc` writes the emitted C++ source to:

```text
tempfil0123.tmphin.cpp
```

It invokes:

```text
g++ tempfil0123.tmphin.cpp -o hin.exe
```

Compiler diagnostics are appended to `tempfil0123.tmphin`, printed to standard output, and the diagnostics file is removed.

Translation-time diagnostics are otherwise those produced by the scanner, encoding filters, shell pipeline, or selected C++ compiler.

## 17. Output

A successful compilation produces:

```text
hin.exe
```

The name is fixed by `shraenicc`; repeated compilations overwrite this file.

## 18. Implementation-Defined Behavior

The following are implementation-defined:

1. The C++ language version selected by `g++`.
2. The target ABI and platform behavior of the emitted executable.
3. The exact handling of unmatched Unicode identifiers by the selected C++ compiler.
4. The availability of legacy headers such as `iostream.h`, `fstream.h`, and `strstream.h`.
5. The availability of POSIX and system identifiers represented by `orictxt` aliases.

## 19. Non-Normative Example

```text
<शैली श्रेणी>
#समावेश <मानकपन.स>

श्रेणी उदाहरण
{
खुला:
    भव व्योम लिखो()
    {
        म_लिखो("नमस्ते\n");
    }
};

पूर्णांक मुख्य()
{
    उदाहरण उ;
    उ.लिखो();
    वापस 0;
}
```

After HindiC++ lexical substitution, the program is compiled as C++ by `g++`.
