---
title: "Dynamic sendgrid template for multiple items!"
date: "2024-08-28"
description: "Ever wondered how to add multiple items in sendgrid template ?"
tags: ["html", "design", "beginners"]
---

Ever wondered how to add multiple items in sendgrid template ?
For example you need to notify your platform users about list of courses available. Since the number of courses are dynamic you cannot create a static template for it.
Lets take an example of Grocery list template

The list of items is dynamic.
```javascript
{
   "items" : [
      {
        "name" : "onion",
        "quantity": 5,
      },
      {
        "name" : "potato",
        "quantity": 5,
      },
      {
        "name" : "chilli",
        "quantity": 5,
      }

   ]
}
```
Inorder to iterate over the items of the nested array, sendgrid gives handlebar capbilities. Example code
```html
{{#if items}}
  <h2 style="margin:10px 10px">Grocery items !</h2>
  {{#each items}}
    <div style="border:1px solid black;min-width:300px;min-height:50px;padding:10px 10px;margin:10px 10px">
        Got to buy {{this.name}}
    </div>
    
  {{/each}}
{{/if}}
```
You can include the similar code to iterate over items and produce a good marketing email templates.