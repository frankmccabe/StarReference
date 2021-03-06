# Introduction

*Star* is a high-level symbolic programming language oriented to the needs of large-scale high performance processing in modern parallel and distributed computing environments.

*Star* is a 'functional-first' language -- in that functions and other programs are first class values. However, it is explicitly not a 'pure' language: it has support for updatable variables and structures. However, its bias is definitely in favor of functional programming and in order to get the best value from programming in *Star*, such side-effecting features should be used sparingly.

*Star* is strongly, statically, typed. What this means is that all programs and all values have a single type that is determined by a combination of type inference and explicit type annotations.

While this definitely increases the initial burden of the programmer; we believe that correctness of programs is a long-term productivity gain -- especially for large programs developed by teams of programmers.

The type language is very rich; including polymorphic types, type constraints and higher-rank and higher kinded types. Furthermore, except in cases where higher-ranked types are required, type inference is used extensively to reduce the burden of 'type bureaucracy' on programmers.

*Star* is extensible; there are many mechanisms designed to allow extensions to the language to be designed simply and effectively. Using such techniques can significantly ease the burden of writing applications.

## About this Reference
This reference is the language definition of the *Star* language. It is intended to be thorough and as precise as possible in the features discussed. However, where appropriate, we give simple examples as illustrative background to the specification itself.

### Syntax Rules
Throughout this document we introduce many syntactic features of the language. We use a variant of traditional BNF grammars to do this. The meta-grammar can be described using itself; as shown in Figure~\vref{metaGrammar}.

```
_MetaGrammar_& \arrow& _Production_\sequence{\,}_Production_\\
_Production_ &\arrow& _NonTerminal_\ \q{::=}\ _Body_\\
_Production_ &\arrowplus&_NonTerminal_\ \q{::+}\ _Body_\\
_Body_ &\arrow& _Quoted_\,\choice\,_NonTerminal_\,\choice\,_Choice_\, \choice\,_Optional_\,\choice\,_Sequence_\,\choice\,( _Body_ )\\
_Quoted_ &\arrow &\q{Characters}\\
_NonTerminal_ &\arrow&_Identifier_\\
_Choice_& \arrow &_Body_\sequence{|}_Body_\\
_Optional_ &\arrow &\q{[} _Body_ \q{]}\\
_Sequence_&\arrow&_Body_\sequence{\rm[_op_]}_Body_\ \choice\,_Body_\sequence{\rm[_op_]}_Body_\plus
\end{eqnarray*}
```
\caption{Meta-Grammar Used in this Reference}\label{metaGrammar}
\end{figure}

Some grammar combinations are worth explaining as they occur quite frequently and may not be 'standard' in BNF-style grammars. For example the rules for actions contain:
\begin{eqnarray*}
_Action_& \arrow&\q{\{}\, _Action_\sequence{;}_Action_\, \q{\}}
\end{eqnarray*}
This grammar rule defines an _Action_ as a possibly empty sequence of _Action_s separated by semi-colons and enclosed in braces -- i.e., the classic definition of a block. 

The rule:
\begin{eqnarray*}
_Decimal_& \arrow&_Digit_\sequence{}_Digit_\plus
\end{eqnarray*}
denotes a definition in which there must be at least one occurrence of a _Digit_; in this case there is also no separator between the _Digit_s.

Occasionally, where a non-terminal is not conveniently captured in a single production, later sections will _add_ to the definition of the non-terminal. This is signaled with a \arrowplus{} production, as in:
\begin{eqnarray*}
_Expression_&\arrowplus&_ListLiteral_
\end{eqnarray*}
which signals that, in addition to previously defined expressions, a _ListLiteral_ is also an _Expression_.

### Typographical Conventions
\index{typographical conventions}
Any text on a programming language often has a significant number of examples of programs and program fragments. In this reference, we show these using a \q{typewriter}-like font, often broken out in a display form:
\begin{alltt}
...
P has type integer
...
\end{alltt}
We use the \q{...} ellipsis to explicitly indicate a fragment of a program that is embedded in a context.


Occasionally, we have to show a somewhat generic fragment of a program where you, the programmer, are expected to put your own text in. We highlight such areas using an \q{_italicized typewriter_} font:

(_Args_) => _Expr_

This kind of notation is intended to suggest that \q{_Args_} and \q{_Expr_} are a kind of _meta-variable_ which are intended to be replaced by specific text.

>Some parts of the text require more careful reading, or represent comments about potential implications of the main text. These notes are highlighted the way this note is, with a \dbend{} symbol.\footnote{Notes which are not really part of the main exposition, but still represent nuggets of wisdom are relegated to footnotes.}

>>Occasionally, there are areas where the programmer may accidentally 'trip over' some feature of the language.
