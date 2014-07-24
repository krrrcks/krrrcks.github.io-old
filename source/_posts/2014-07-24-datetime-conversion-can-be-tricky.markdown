---
layout: post
title: "DateTime conversion can be tricky"
date: 2014-07-24 09:41:36 +0000
comments: true
categories: [JavaScript, "Common Lisp"] 
---

I wrote a small Lisp application and a JavaScript client gets some
date from that application. All time stamps are returned as "Lisp"
time stamps, i.e. an integer with seconds where zero equals Jan 01
1900. 

In the JS client the time stamp is then converted to JS time stamps,
i.e. millisconds where zero equals Jan 01 1970. 

When testing the application I noticed that sometimes the displayed
date is one day behind. For example in the data base I have Jan 05
1980 but in JavaScript I get a Jan 04 1980. But some other dates
worked: A time stamp Jan 05 1970 was correctly converted to Jan 05
1970.

I had a look into the JavaScript code and found:

```
convA = function(ts) {
  tmp = new Date(ts*1000);
  tmp.setFullYear(tmp.getFullYear() - 70);
  return tmp.getTime();
}
```

It's likely the developer thought: "Well, it's millisecond instead of
second. Therefore I multiply by 1,000. But then I am 70 years in the
future and I have to substract 70 years and everything will be ok."

After thinking a while I came to the conclusion: Of course not!

The developer made the assumption that there are as many leap years
between 1900 and 1970 as between `ts` and `ts+70`. Obviously that
assumption does not hold for all time stamps. And therefore sometimes
the resulting JavaScript date is one day behind.

So a better solution would be to substract all seconds between 1900
and 1970 from `ts`, multiply by 1,000 and treat this as a JavaScript
time stamp. Perhaps best would be to do the conversion in the Lisp
process and only deliver a JavaScript-like time stamp.

