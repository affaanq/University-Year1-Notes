
1)  es6 introduced two pronged fascade function
2) old school was xhr and the new school is the fetch or the two pronged fascade fucntion
3) the two pronged fascade function sets a web browser feature and also returns a promise object in the memory 
4) Most powerful word: fetch speaks to the interned via the web browser to speak to the twitter
```js
function display(data)

{ console.log(data)
 }

 const futureData = fetch('https://twitter.com/will/tweets/1') 
 
 futureData.then(display)

 console.log("Me first!");
```

What is tehe two progned facade function in the context of the web api:

A function that simultaneously initiates a background web browser task (like a network request) and returns a promise object in JavaScript, allowing tracking and handling of the asynchronous operation directly in JavaScript memory

Problem with the old school setTimeout feature in the Js: They trigger browser features without providing a way to track or maintain consistency between the background operation and JavaScript memory, making it difficult to reason about and manage asynchronous operations

fetch returns a special type of an object known as the promise object which acts like a placeholder  that will be updated with the result fon teh background web browser

Two consecunses of the fetch 
One it created a promise object placeholded in th Javascript and it also ask for a Network request in the web browser api