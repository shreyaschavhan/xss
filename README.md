`Note` `My notes on XSS for personal reference`

## 𝐗𝐒𝐒 𝐓𝐲𝐩𝐞𝐬

![XSS](https://user-images.githubusercontent.com/68887544/179921615-68aef3f0-3f23-450f-9fa8-2b93b690bb75.png)


## 𝐑𝐞𝐟𝐥𝐞𝐜𝐭𝐞𝐝 𝐗𝐒𝐒

- Finding Reflected XSS
```
1. Submit a random string for example: myxssteststring
2. Identify all locations where this string is reflected in the application’s response
3. For each reflection, identify the syntactic context in which the reflected data appears.
4. Modify the payload accordingly to get an XSS.
5. If input is sanitized or filtered in anyway, try modifying your payload to make it 
circumvent the defense mechanism.
```

- Common Reflection Positions


![Common Reflection Positions](https://user-images.githubusercontent.com/68887544/179921399-fd02889c-d051-4876-bdfc-fb8484de0090.png)


