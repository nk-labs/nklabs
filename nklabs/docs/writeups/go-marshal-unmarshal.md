## `JSON Marshalling and Unmarshalling`

In this article we'll see

<ul>
  <li> how to convert string data into bytes (marshal) </li>
  <li> how to convert bytes into Go struct (unmarshal) </li>
  <li> how to convert bytes into Go interface (unmarshal) </li>
</ul>

<h3> data into bytes </h3>
We coverted data into bytes in two ways:
<ul>
  <li> One, using byte() function - read  function `convertToBytes` </li>
  <li> Other, using json.Marshal() - read function `convertToBytesUsingJsonMarshal` </li>
</ul>

<h3> bytes to struct </h3> 
<ul>
  <li> first defined a empty struct to hold values `jb := Response{}` </li>
  <li> then use json.Unmarshal to deserliaze into this struct `json.Unmarshal(<raw1 or raw2>,&jb)` </li>
</ul>



<h3> bytes to interface </h3> 
<ul>
  <li> first defined a empty interface to hold values `var jbi interface{}` </li>
  <li> then use json.Unmarshal to deserliaze into this interface `json.Unmarshal(<raw1 or raw2>,&jbi)` </li>
</ul>
<br/>

<h5> <b> Q: When to unmarshal in interface instead of struct </b> </h5>
<b> A: </b> There could be many use cases. But one we found is field extraction. For example, if you are given a json with certain fields and you want to validate this json, meaning you want to make sure if it has all the required fields. SO first you need to extract all the fields right? And then match against a required fields set. Unlike Javascript we don't have the luxury to get the keys of an object by doing Object.keys() to get all keys :( So what we do? 
This is covered in [go-json-advanced](#go-json-advanced) writeup.

<font color="purple"> Go Playground link </font>
[https://play.golang.org/p/Fia-l18hWvi](https://play.golang.org/p/Fia-l18hWvi)

