# Lexical Analysis

Set lexical analysis function by setting variable `parser-generator-lex-analyzer--function`. Optionally set reset function by setting variable `parser-generator-lex-analyzer--reset-function`. The lexical analysis is indexed on variable `parser-generator-lex-analyzer--index`. All parsers expect a list of tokens as response from lexical-analysis.

If you detect that the pointer needs to move, set flag `parser-generator-lex-analyzer--move-to-index-flag` to non-nil to move lex-analyzer position.

To enable exporting the functions need to be specified in a way that the entire body is within the same block, do that using `(let)` or `(progn)` for example.

## Token

A token is defined as a list with 3 elements, first is a string or symbol, second is the start index of token in stream and third is the end index of token in stream, second and third element have a dot between them, this structure is to be compatible with Emacs Semantic system. Example token:

``` emacs-lisp
'("a" 1 . 2)
```

## Peek next look-ahead

Returns the look-ahead number of next terminals in stream, if end of stream is reached a EOF-identifier is returned. Result is expected to be a list with each token in it.

``` emacs-lisp
(require 'ert)
(setq
   parser-generator-lex-analyzer--function
   (lambda (index)
     (let* ((string '(("a" 1 . 2) ("b" 2 . 3) ("c" 3 . 4) ("d" 4 . 5)))
            (string-length (length string))
            (max-index index)
            (tokens)
            (next-token))
       (while (and
               (< (1- index) string-length)
               (< (1- index) max-index))
         (setq next-token (nth (1- index) string))
         (push next-token tokens)
         (setq index (1+ index)))
       (nreverse tokens))))
(parser-generator-lex-analyzer--reset)

(setq parser-generator--look-ahead-number 1)
(should
 (equal
  '(("a" 1 . 2))
  (parser-generator-lex-analyzer--peek-next-look-ahead)))

(setq parser-generator--look-ahead-number 2)
(should
 (equal
  '(("a" 1 . 2) ("b" 2 . 3))
  (parser-generator-lex-analyzer--peek-next-look-ahead)))

(setq parser-generator--look-ahead-number 10)
(should
 (equal
  '(("a" 1 . 2) ("b" 2 . 3) ("c" 3 . 4) ("d" 4 . 5) ($) ($) ($) ($) ($) ($))
  (parser-generator-lex-analyzer--peek-next-look-ahead)))
```

## Pop token

Returns the next token in stream and moves the lexical analyzer index one point forwa<rd. If end of stream is reached return nil. The result is expected to be a list containing each token popped.

``` emacs-lisp
(require 'ert)
(setq
   parser-generator-lex-analyzer--function
   (lambda (index)
     (let* ((string '(("a" 1 . 2) ("b" 2 . 3)))
            (string-length (length string))
            (max-index index)
            (tokens))
       (while (and
               (< (1- index) string-length)
               (< (1- index) max-index))
         (push (nth (1- index) string) tokens)
         (setq index (1+ index)))
       (nreverse tokens))))
(parser-generator-lex-analyzer--reset)

(setq parser-generator--look-ahead-number 1)
(should
 (equal
  '(("a" 1 . 2))
  (parser-generator-lex-analyzer--pop-token)))
(should
 (equal
  '(("b" 2 . 3))
  (parser-generator-lex-analyzer--pop-token)))
(should
 (equal
  nil
  (parser-generator-lex-analyzer--pop-token)))
```

[Back to start](../../../)
