# Discussing tags

Maybe it worths to add some simple tagging to the string values? This helps to keep spec simple and keep users of various date formats #263 and regexen #292 happy at the same time.

e.g.,  \<ISODATE\>"2015-02-06T11:55:12-08:00"

So, allow to tag strings with \<TYPE\> which describes its format. If parser have no support for this extension it may ignore that and pass string as is, printing warning to stdout.

Nice point of extension.

Fans of NULL may use \<NULL\>"", for example.

\<BASE64\>"aGVsbHA=" as a way to embed binary blobs.

and \<REGEX\>"/[0-9A-Z_a-z]+/".

You may choose other syntax, e.g., !REGEX"/[0-9A-Z_a-z]+/" or REGEX@"/[0-9A-Z_a-z]+/".
I personally like this:

    timestamp = (DATETIME)"Mon, 20 Jan 2014 20:00:00 GMT"

or

    timestamp = DATETIME:"Mon, 20 Jan 2014 20:00:00 GMT"
    pattern = REGEX:"/[0-9A-Z_a-z]+/"
    sshkey = BASE64:"aGVsbHA="
    request = JSON:'{"a":true,"b":["",false]}'
    limit_bandwidth = { up=Kbit:"8", down=Kbit:"16" }

if allow tags for arbitrary values, we got ability to write

    coords = [ tuple:[10,2], tuple:[44,-1], tuple:[91,11]  ]
    m = matrix:[ [1,2,3] , [4,5,6] , [7,8,9] ]
    expr = term:{head="Mul",args=[term:{head="Add",args=[10,term:{head="Var",args=["x"]}]}]}
    mysets = [ set:[1,2,3], set:["a","b",c"], set:[true,false] ]
    

----

I think these “type hints” complicate things. Maybe we just need a customizable TOML parser, provide custom functions which comply with the protocols, i.e. array is a container, table is still a key-value table, number is some number, you provide the proper containers and structs, and if everything can work together ... done.

If we just don't like the date-time type, I suppose dropping them, parse the date-time strings ourselves, it is not hard. And transform a date-time string between different format should be trivial in most cases, even if we are so worried about the compatibility between different apps.

----

TOML user which is strongly concerned about simplicity may ignore "type hints" completely.

Without type hints it is impossible to construct custom objects/records from TOML fully automatically, in clean and generic way: it needs individual (de)serializer per record, or messing with type introspection, or hacks like JSON hooks which is not very convenient and notably error prone.

Also "type hints" provide nice point of extension for future TOML versions.

----

I mean that when you give simple hints like REGEX, ISODATE, DATETIME, SET, you still need to consider what is the exact type for a value, i.e. posix regex, extended regex, pcrs, re2, isoXXXX, ordered set, unordered set? What if you want posix regex, but you write the extended one? Need to parse again, or make a bloated parser? So it is still best to get a customizable parser.

----

Type hints are not necessary if you know the needed types for every “path” (like "first.second.third") beforehand. Then you can tell a customizable parser how to deal with special values. This is enough for simple configuration files. For the complicated ones, you can also wrap them in special objects/tables with the type hints type = "set". It is a flexible method.

----

I think type conversion must be done entirely at user level. Generic TOML parser just gives you \<type\>,\<value\> pair and let it you consider what to do next on your own.

I think concrete tag specification must not go in TOML. It may go to special TOML Extension Registry standard or be absent at all.

----

> Type hints are not necessary if you know the needed types for every “path” (like "first.second.third") beforehand. 

Yes, but in some programming languages (like e.g., Haskell) it is easy, in more dynamic (e.g., Python) it is a way less convenient. Not being self describing complicates things ;)

Also, to me, having types specified here and there improves readability a lot.

> For the complicated ones, you can also wrap them in special objects/tables with the type hints type: "set". It is a flexible method.

Yes. But it disrupts readability and adds clutter.

Compare:

    { "type": "Point", "x": 123, "y": 321 }

and

    Point:[123,321]

or (real example!)

    { "type": "box", "x": 321, "y": 123, "width": 44, "height": 500 }

and

    box:"44x500+321+123"

(this is ImageMagick geom syntax)

----

I think this pretty definitively goes against being minimal IMO. Let's just keep things simple. If you need to support a variety of different types then perhaps YAML is a better fit for you.


----

I enjoy this new idea though I think it is not for simple use cases. I feel that it is somewhat like XML.

----

Tags won't used if you do not use "nonstandard" data types (only strings/dicts/lists) so simplicity won't hurt.

Oops. In starting message I forgot to mention tags are optional so one may think they are mandatory :/

I hate XML too =)

----



I might not write a parser for this idea, but just want to talk something about it. (And I'm ready to sleep. ;-)

    When you give the type hints, do you very likely want to use that type of values, then losing them will disappoint you a lot?
    Who is the end user? The developer or a normal person? As I know, the developer should know what he wants from the configuration files most of the time.


----

End users are both developers and non developers. I didn't plan initially use tags as hints for non developers but why not, after all?

What I want is to use generic code for config file reading, without boring conversions from JSON dicts to my internal data structures (ugh). And I fed up with {"":"Box","x":123,"y":321,"w":40,"h":30} stuff instead of my lovely "40x30+123+321" to which I have used but I don't want to complicate programs by instantiating (de)seralizers for each data record type which uses Box in one of its fields.

And I sometimes use config files which I read/write both programmatically and manually.

So yes,  losing type hints destroys everything outright.

There may be some generic scanning/reporting tools so they perhaps survive tag omission but not owning program.

----

One more use case:


    [[favorites]]
    url = 'http://microsoft.com/'
    title = 'largest nest of humanity enemies ever'
    favicon = "http://files.softicons.com/download/social-media-icons/free-social-media-icons-by-aha-soft/png/256x256/Microsoft.png"

    [[favorites]]
    url = 'http://google.com/'
    title = 'interesting home page'
    favicon = image:"iVBORw0KGgoAAAANSUhEUgAAACgAAAAoCAYAAACM/rhtAAAB70lEQVRYw+2Xsa2DMBCGGQEmeBFvgUgsQMEAr6F/FXU62pR0qVOlT5MBKFggUiZA2SBvBL+z9Fs6XUIw2EAKLP0kGPv8cbbPR6CUCsbo+2uzJe1JDelC2pHCsfa6NBZMQ6kXumnQpQHvHXBc20UAaeDUAk5rvwI6Av5MBkhFX1QH4MYSMF0K0NaDp08HbLwCGqguiRhoA3hZBBCQlx64v0nj4LspZpDvAKc9STwApp8AeOiAu/tOGMacxeFcIWbsSdJY7OJfHdRnAYTHdpZZzJM3XddkX953QthQjrrBq6EzYE9C6kMHV8D9hHCDE9pXgM0MgOkKuCTgFvFuSoWTnSRzawVcAT8CkEpMykiJyA11Xew0QBBEsBONsatLKb5BrsYYlZpUOAJmsJuJpLi0BdTXI24S3pm/KfcE+43R58lLtoDCZvwKUHvsQapIuZgKBQ8naHOFVxUM6mct6lu0iQYCKvQzdgsJqOmPGMA0TgTgWT9HXSEAFWzkEmQAoJlBzfGQgAWbxhigtQCsWV0mAbtABgCa/6X8YAvY1GXwwoO9kQGszPQxr9kCRqiv0abCfc7GqNmmbCVgDkCzi89sFxvAiK2R6xBAtiz4EqqEN2vUt0+hbkCoMG9s1lriJRD3hBxbI4nwcuXtpPABuKT+AfGNHTJ5HbnLAAAAAElFTkSuQmCC"

Do you still think we can live without tags?

----

One more (killer) use case (for static typing fans):

    [section]
    member1 = Nullable:{"a"=1,"b"=300,"c"=-1}
    member2 = { "aa"=Nullable:1,"bb"=Nullable:300,"cc"=Null:"" }

----

Use case:

You may optionally run preprocessors and convertors which is not known to intended reader of config file (and may stack up even after config reading program released, given toml exts are in .so):

    [section1]
    entry1 = "abc"
    entry2 = compress_spaces:"abc  def gh "
    entry3 = break_lines:read_file:"include_me.txt"
    list_of_items = comma_split:"a,b,c,d"
    path = expand_tildas:interpolate_env_vars:"~/.bin/${ARCH}/distrib"
    mylist = sort:[1,2,30, -1]

----

what about 

    limit_bandwidth = { up=Kbits:500, down=Mbits:0.5 }

isn't that already convincing per se?

This is less readable even with _ in numbers:

    limit_bandwidth = { up=500_000, down=500_000_000 }

what units are? bits or bytes? oops!

or:

    limit_bandwidth = { up={speed=500,units="Kbits"},down={speed=0.5,units="Mbits"} }

a lot uglier and bulky, no?
