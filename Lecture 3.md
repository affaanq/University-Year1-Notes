moon modeler: Data modelling, data structure making for the user. Basically it is making a schema out for the user

Eraser.io: Used for data modelling

Always think about where the data is been saved. We make the registration 
So the process for the todays lectue is as foloow
We will sue stackblitz for today
We will choose express.js as the model
then we will install  mongoose

```
npm i mongoose
```

we will create a file using the name 
```
user.models.js
// The word models denotes that it is a data model, it is professionals and usually a good practice in companies
```

We will be making three files:

1. Sub_todo.models.js
todo.models.js
user.models.js

``` js
user.models.js

import mongoose from "mongoose"
const userSchema = new mongoose.Schema(

  {
    username: {

      type: String,
      required: true,
      unique: true,
      lowercase: true
    },

    email: {

      type: String,
      required: true,
      unique: true,
      lowercase: true
    },

    password: {

      type: String,
      required: [true, "password is required"], // one of the best mongoose feature, we can send custom message from the data model.
    }
  },
  {timestamps: true}
  )

export const User = mongoose.model("User", userSchema) // model will convert in plural, it is the practice of MongoDB so it will be users.
```


``` js
todo.models.js

import mongoose from "mongoose"
const todoSchema = new mongoose.Schema({

  content: {
    type: String,
    required: true,
  },

  complete: {
    type: Boolean,
    default: false
  },

  createBy: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"

  },

  subTodos: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: "SubTodo"
    }
  ]
}, {timestamps: true});

export const Todo = mongoose.model("Todo", todoSchema);
```

``` js
subtodo.models.js

import mongoose from "mongoose";
const subTodoSchema = new mongoose.Schema({

  content: {
    type: String,
    required: true

  },

  complete: {
    type: Boolean,
    default: false

  },

  createdBy: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",

  }

}, {timestamps: true});

export const subTodo = mongoose.model("subTodo", subTodoSchema);
```