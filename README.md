# INISON

JSON is too bureaucratic and restricted, YAML is too freeform and complex -- INISON is just rigjt!

Flexible enough and simple enough.

Brings new life to old good INI files.

INISON is just JSON except:

* using \= instead of \:
* allowing bare identifiers as object keys {a=1}
* no NULL and NAN
* # comments: {"a"=1} # my "A"
* allowing trailing commas: [1,2,]
* tagged values: {b=DATE:"2015-01-01",c=}
* toplevel INI like syntax for objects, so {a=1,b={c=2,d=3}} may be written as a=1 [b] c=2 d=3 which greatly improve readability and reduces {{}} clutter

File extension is *.ison

## Grammar

\<\<insert rendered railroad diagram picture here like at json.org\>\>
