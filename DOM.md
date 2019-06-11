## 练习

### 1. `nodeType`属性

- `elem.nodeType == 1` 是元素节点，
- `elem.nodeType == 3` 是文本节点，
- `elem.nodeType == 9` 是 document 对象，

```html
<html>

<body>
  <script>
    alert(document.body.lastChild.nodeType);// 在执行 <script> 时，最后一个 DOM 节点是 <script>，因为浏览器还没有处理页面的其余部分,所以是1
  </script>
</body>

</html>
```

2. 注释标记的值

```html
<script>
  let body = document.body;

  body.innerHTML = "<!--" + body.tagName + "-->";

  alert( body.firstChild.data ); // BODY
</script>

/*
1. <body> 内容被注释所取代 <!–BODY–>，因为 body.tagName == "BODY"。正如我们所记住的，tagName 在 HTML 中总是大写的。
2. 这个注释现在是唯一的子节点，所以我们可以在 body.firstChild 中获取。
3. 注释的data 属性是它的内容 (inside <!--...-->)："BODY"。
*/
```

