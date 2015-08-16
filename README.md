# INISON

JSON is too bureaucratic and restricted, YAML is too freeform and complex -- INISON is just rigjt!

Flexible enough and simple enough.

Brings new life to old good INI files.

INISON is just JSON except:

* using = instead of :
* allowing bare identifiers as object keys {a=1}
* # comments: {"a"=1} # my "A"
* allowing trailing commas: [1,2,]
* additional quote to disable escaping (')
* python like triple quoting (""" and ''') to enable multiline strings (''' does not parse escapes)
* tagged values: {b=DATE:"2015-01-01",c=PCRE:"/^x[0-9]/i"}
* no NULL and NAN (use tags instead, {a=1,b=NaN:0,c=3,d=NULL:0})
* toplevel INI like syntax for objects, so {a=1,b={c=2,d=3}} may be written as a=1 [b] c=2 d=3 which greatly improve readability and reduces {{}} clutter

File extension is *.ison

## Grammar

\<\<insert rendered railroad diagram picture here like at json.org\>\>
