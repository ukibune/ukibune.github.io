
# fail
---
<24/11/12>

中国語のフォントをMDに指定したいですけど、失敗しました。
~~~ css
<head>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@200..900&display=swap" rel="stylesheet">

<style>
.noto-serif-sc-uniquifier {
  font-family: "Noto Serif SC", serif;
  font-optical-sizing: auto;
  font-weight: 300;
  font-style: normal;
}
</style>

</head>

<div id="noto-serif-sc-uniquifier">
~~~
---