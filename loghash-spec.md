# Loghash log markup language

This document serves as a specification for the Loghash markup language.

## Why a log markup language ?

Since your first programming course, we output *things* to see if our code run 
well. We've all done a "hello world" echo/printf/console.writeline !

Now, we have fancy things like *analytics platforms*, 
*error management solutions* or *mobile application performance*. However, we 
still output plain text log lines for debbuging, analysis and performance 
testing.

### The need of semantic logging

When you log extensively within your app, finding the information you want get 
really complicated over time. You end up writing "disposable logs that stays".

Among others, the patterns & practices at Microsoft has proposed an interesting 
concept: **semantic logs**. These logs are crafted in a specific way that 
enables developers output meaningful, repeatable, "filterable" logs. However, 
the complete solution is really cumbersome: you need to create a method/class by
"log type". You could easily end-up with more logging classes than 'code' 
classes.

**LogHash log markup language** aims to provide a solution to add semantics to a
simple string log. Every string log is, de facto, a "loghash log". If you want 
take advantage of features from the markup language, you only have to add some 
specifics to existing or new log code, like a hashtag. 

Therefore, this markup language works with any programming language / framework 
/ os / logging infrastructure you're already using ! This is one of the core 
principles driving the creation of the markup language: **being able to add 
semantics to your log without any upfront cost**.

### Log is the root of all things

When you have semantics in your logs, you have anything you want ! Take example 
on [segment.io](https://segment.io/) service. They have a really neat idea: 
track anything you want in your app with their tool, and they send data, at will
, to various analytics tools. That way, a developer can only track things one 
time, and any service in the company can use the best suited tool without adding
 development overhead: advertising, A/B test, support, user testing, marketing, 
 etc...

With semantic logs, you can go a step further: with the appropriate framework, 
you can generate these tracking events directly from your technical logs, even 
after the app was released !


## Log markup language

### Quick filtering & classification

To classify a log line, you only have to add a specific hashtag to the line.

```
New user created #useraccount 
```

You can add several tags to a log line.

```	
New user added in MongoDb #useraccount #database 
```

You can also create a hash hierarcy, by chaining several words separated by dots `.`. 
The following log line will be identified as containing #db and #db.mongo tags.

```
New user added in MongoDb #db.mongo 
```

Some hashtags are "reserved". You can use them in your logs, but they have 
special meaning for parsers, viewers and utilities working with the loghash 
markup: 

- `#error`,
- `#warn`,
- `#info`,
- `#debug`, 
- `#trace`.

### Identifiers

In some cases (including server-side logging, multi-thread or distributed work, 
...), you may want to get only logs from a specific user/action/thread/etc... 
For this, you can use identifiers. An identifier starts with an at (`@`) symbol,
  followed by the identifier name, a `:` symbol and a value. The identifier 
  cannot contain spaces or `:` symbol.

```
@userid:42
```

If your identifier value contains special characters, you can encapsulate it 
between two double-quotes (`"`) symbol

```
@username:"Christopher Maneu"
```

All identifiers starting by `@__` are reserved.

### Object dumps

You can dump object / object hierarchies through a json format. The json content
  must be preceded by a `@` symbol.

```
@{"userid":42,"status":"premiumplus"}
```

A log line can contains hashtags, identifiers and object dumps.

```
Google Play callback: #inapppurchase @userid:42 @{"iap-product-id":42,"success":true}
```

Some JSON properties names are reserved. Your object dump should not contain the 
identifiers starting by a double underscore `__` 

### Secure/confidential content

Logs can contains many sensitive info (password hash, encryption keys, etc...). 
We recommend to NOT log any sensitive info at all, but in specific case, it can 
be really useful. To secure a string, you can use the `@$` construct.

> **Warning**: this markup element is only secure if the LogHash markup parser 
you're using supports this feature. If you save/send your logs before being 
handled by the parser, content is not secured.

```
@$""
```

If you want to secure an identifier, you can use the identifer structure with 
the `@$` construct.

```
@$userEncryptionKey:"sdhq238hfkqsd82hf"
```

### Counters

Counters can be useful to track many things inside your app or service. You can 
use a specific object dump structure to handle couters in your log.
The following log lines increment, and the decrement the `user.offlinealbums` 
counter by 1.

```
@{"__count":"user.offlinealbums","v":1}
@{"__count":"user.offlinealbums","v":-1}
```

You can also set the counter value with the `c` property.

```
@{"__count":"user.offlinealbums","c":12}
```

### Timings

If you are working on performance issues, or monitoring a production system, 
timing things is a useful feature. You can add a timing to any log with the 
following construct. The duration is in milliseconds.

```
@{"__timing":"account creation","d":323}
```

### Progressions

For logging long-running operations, you can use the following construct. The 
progress value is an int value from 0 to 1

```
@{"__progress":"demo","p":0.3721}
```

### Timestamping 

If your log files are not ingested in real-time, you will need to timestamp your
  log lines. The following construct enables you to log the creation time 
  timestamp in two formats: 


- `@__ts:10:12:14`: the value is an human-readable timestamp, with `HH:mm:ss` 
24-h value,
- `@__ts:1414759128`: the value is an unix timestamp,


## Custom viewers

In the future, you'll be able to use custom viewers within a LogHash log. These 
*custom events* use a more detailed structure for logs used widely within a 
development framework/language.

```
@{"__e":"timing","d":{"action":"user.login","duration":1244}}
@{"__e":"http","d":{"url":"http://api.dz.com/artist/42","statuscode":200}}
@{"__ee":"android.activity.lifecycle","d":{"name":"feedactivity","status":"created"}}
@{"__e":"log","l":"#userviewmodel User logged in @{"userid":42,"status":"premiumplus"}", "c":[ {"device.type":"android"},{"os.version":"4.1.3.45"},]}
``` 