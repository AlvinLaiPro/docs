![](https://cdn.nlark.com/yuque/0/2023/svg/35422548/1680268991392-338e2eed-9f2c-4676-bd64-1a53bc18600c.svg#from=url&id=fS8wm&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)<br />![](https://cdn.nlark.com/yuque/0/2023/svg/35422548/1680268991392-338e2eed-9f2c-4676-bd64-1a53bc18600c.svg#from=url&id=OKD0r&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## Legend
![](https://www.growingwiththeweb.com/images/2014/02/26/legend.svg#from=url&id=imebG&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## <script>
Let’s start by defining what <script> without any attributes does. The HTML file will be parsed until the script file is hit, at that point parsing will stop and a request will be made to fetch the file (if it’s external). The script will then be executed before parsing is resumed.<br />![](https://www.growingwiththeweb.com/images/2014/02/26/script.svg#from=url&id=ByO0Y&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## <script async>
async downloads the file during HTML parsing and will pause the HTML parser to execute it when it has finished downloading.<br />![](https://www.growingwiththeweb.com/images/2014/02/26/script-async.svg#from=url&id=NAyTc&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## <script defer>
defer downloads the file during HTML parsing and will only execute it after the parser has completed. defer scripts are also guaranteed to execute in the order that they appear in the document.<br />![](https://www.growingwiththeweb.com/images/2014/02/26/script-defer.svg#from=url&id=H5oh8&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## When should I use what?
Typically you want to use async where possible, then defer then no attribute. Here are some general rules to follow:

- If the script is modular and does not rely on any scripts then use async.
- If the script relies upon or is relied upon by another script then use defer.
- If the script is small and is relied upon by an async script then use an inline script with no attributes placed _above_ the async scripts.
## Support
IE9 and below have some pretty bad bugs in their implementation of defer such that the execution order isn’t guaranteed. If you need to support <= IE9 I recommend not using defer at all and include your scripts with no attribute if the execution order matters. [Read the specifics here](https://github.com/h5bp/lazyweb-requests/issues/42).
