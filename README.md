`Note` `: My notes on XSS for personal reference`

## ⁍ 𝐗𝐒𝐒 𝐓𝐲𝐩𝐞𝐬

![XSS](https://user-images.githubusercontent.com/68887544/179916978-efe8ea00-ec42-4c03-9330-edfa64a8f7c6.png)

## ⁍ 𝐅𝐢𝐧𝐝𝐢𝐧𝐠 𝐑𝐞𝐟𝐥𝐞𝐜𝐭𝐞𝐝 𝐗𝐒𝐒

```
1. Submit a random string for example: myxssteststring
2. Identify all locations where this string is reflected in the application’s response
3. For each reflection, identify the syntactic context in which the reflected data appears.
4. Modify the payload accordingly to get an XSS.
5. If input is sanitized or filtered in anyway, try modifying your payload to make it 
circumvent the defense mechanism.
```
