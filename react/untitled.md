---
description: Some apparent diffs in above two
---

# JSX Vs HTML

Replace **class** with **className**

Refer to style as json

{% tabs %}
{% tab title="Direct Way" %}
```javascript
<h1 style={{ border: '1px solid'}}></h1>
```
{% endtab %}

{% tab title="Indirect Way" %}
```javascript
const customStyle = { border: '1px solid' };
<h1 style={customStyle}></h1>
```
{% endtab %}

{% tab title="Convert hyphen to camelCase" %}
```javascript
// as opposed background-color in html/css 
<h1 style={{ backgroundColor : 'red'}}></h1>
```
{% endtab %}
{% endtabs %}

JSX cant read an object directly

```javascript
//Correct
const text="Show this text";
<lable>{text}</lable>
// Wrong
const obj={ text: "Show this text" };
<lable>{obj}</lable>

// Correct
const obj={ text: "Show this text" };
<lable>{obj.text}</lable>
```

