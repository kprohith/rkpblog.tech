---
toc: true
layout: post
description: A random silly story generator
categories: [markdown]
title: MADLIB - Story generator in JavaScript
---

# MADLIB - Story generator in JavaScript

main.js 

```javascript
 var customName = document.getElementById('customname');
var randomize = document.querySelector('.randomize');
var story = document.querySelector('.story');

function randomValueFromArray(array){
  return array[Math.floor(Math.random()*array.length)];
}

var storyText = "It was 94 fahrenheit outside, so :insertx: went for a walk. When they got to :inserty:, they stared in horror for a few moments, then :insertz:. Bob saw the whole thing, but was not surprised — :insertx: weighs 300 pounds, and it was a hot day.";
var insertX = ["Willy the Goblin",
                "Big Daddy",
                "Father Christmas"]

var insertY =["the soup kitchen",
              "Disneyland",
              "the White House"]

var insertZ = ["spontaneously combusted",
              "melted into a puddle on the sidewalk",
              "turned into a slug and crawled away"]

randomize.addEventListener('click', result);

function result() {
  var newStory = storyText;

  var xItem = randomValueFromArray(insertX);
  var yItem = randomValueFromArray(insertY);
  var zItem = randomValueFromArray(insertZ);
  
  newStory = newStory.replace(/:insertx:/g, xItem);
  newStory = newStory.replace(/:inserty:/g, yItem);
  newStory = newStory.replace(/:insertz:/g, zItem);
  
  
  if(customName.value !== '') { 
    var name = customName.value;
    newStory = newStory.replace(/Bob/g, name);
    

  }

  if(document.getElementById("uk").checked) {
    var weight = Math.round(300*0.071429) + ' stone';
    var temperature =  Math.round((94-32)/1.8) + ' centingrade';
    newStory = newStory.replace(/94 fahrenheit/g, temperature);
    newStory = newStory.replace(/300 pounds/g, weight);

  }

  story.textContent = newStory ;
  story.style.visibility = 'visible';
  
  
  
  
}

document.querySelector('html').style.backgroundColor = 'blue';
```
---

index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width">

    <title>Silly story generator</title>

    <style>
    body {
      font-family: helvetica, sans-serif;
      width: 350px;
    }
    label {
      font-weight: bold;  
    }
    div {
      padding-bottom: 20px;
    }
    input[type="text"] {
      padding: 5px;
      width: 150px;
    }
    p {
      background: #FFC125;
      color: #5E2612;
      padding: 10px;
      visibility: hidden;
    }
    </style>
  </head>

  <body>
    <div>
      <label for="customname">Enter custom name:</label>
      <input id="customname" type="text" placeholder="">
    </div>
    <div>
      <label for="us">US</label><input id="us" type="radio" name="ukus" value="us" checked>
      <label for="uk">UK</label><input id="uk" type="radio" name="ukus" value="uk">
    </div>
    <div>
      <button class="randomize">Generate random story</button>
    </div>
    <!-- Thanks a lot to Willy Aguirre for his help with the code for this assessment -->
    <p class="story"></p>
    

  <script src="main.js"></script>
  </body>
</html>
```

---

style.css
```css
body {
  font-family: "Benton Sans", "Helvetica Neue", helvetica, arial, sans-serif;
  margin: 2em;
}

h1 {
  font-style: italic;
  color: #373fff;
}
```
