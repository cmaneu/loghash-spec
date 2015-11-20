#>empty-string

{
  "tokens":[
  ],
  "items":{
    "tags": [],
    "identifiers": [],
    "object_dumps": [],
    "secure_string":  [],
    "counters": [],
    "timers": [],
	  "progress": [],
    "timestamps": []
  }
}


#>simple-string
This string is like any other string. No loghash-specific items.
{
  "tokens":[
    {"type":"text", "content":"This string is like any other string. No loghash-specific items."}
  ],
  "items":{
  }
}

#>string-startswith-hashtag
#start a string with a hashtag
{
  "tokens":[
    {"type":"tag", "content":"start"},
    {"type":"text", "content":" a string with a hashtag"}
  ],
  "items":{
    "tags": ["start"]
  }
}

#>string-endswith-tag
This string contains one #hashtag
{
  "tokens":[
    {"type":"text", "content":"This string contains one "},
    {"type":"tag", "content":"hashtag"}
  ],
  "items":{
    "tags": ["hashtag"]
  }
}

#>string-endswith-twotags
This string contains #two #hashtag
{
  "tokens":[
    {"type":"text", "content":"This string contains "},
    {"type":"tag", "content":"two"},
    {"type":"tag", "content":"hashtag"}
  ],
  "items":{
    "tags": ["two", "hashtag"]
  }
}

#>string-endswith-hierarchicaltag
This string contains one #hierarchical.hashtag
{
  "tokens":[
    {"type":"text", "content":"This string contains one "},
    {"type":"tag", "content":"hierarchical.hashtag"}
  ],
  "items":{
    "tags": ["hierarchical.hashtag"]
  }
}


// Line ends with #
// a # alone
// two # side-by-side #one#two
// #one is identical to #one
// #one.subone


// @id:1
// @username:"aurore"
//
