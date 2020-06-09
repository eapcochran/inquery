# InQuery Grammar Specification

The grammer for InQuery is defined using [Augmented Backaus-Naur Form](https://tools.ietf.org/rfc/rfc5234.txt).

```
comparator             =  "=" / "<" / ">" / "<=" / ">="
unary_operator         =  "not"
binary_operator        =  "and" / "or"
function               =  "any" / "all" / "length"

non_reserved_character =  %x21 / %x23-39) / %3B-7E
  ; Any printable character, excluding " and : which are reserved

colon                  =  ":"

term                   =  1*non_reserved_character
  ; A term is a sequence of one or more non-reserved characters

phrase                 =  DQUOTE 1*( non_reserved_character / colon / "\" DQUOTE / WSP ) DQUOTE
  ; A phrase is a sequence of one or more characters and spaces,
  ; including the reserved : and ", the latter must be escaped with a \.
  ; The whole phrase must be enclosed in a pair of double quotes.

term_or_phrase         =  term / phrase

unary_expression       =  term colon expression
                       =/ term comparator term_or_phrase
  ; Examples:
  ; foo: baz = 1
  ; foo = "a phrase"
  ; bar > 12

expression             =  unary_expression
                       =/ unary_operator expression
                       =/ expression binary_operator expression
                       =/ function expression
                       =/ function comparator term_or_phrase
                       =/ "(" expression ")"
  ; Examples:
  ; foo = 12 and bar < 13
  ; any foo > 12
  ; all > 12
  ; (foo = "bar baz" and bar > 12) or baz < 10

query                  =  expression

```