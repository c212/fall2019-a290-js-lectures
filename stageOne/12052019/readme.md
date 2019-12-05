# Stage One Example/In Class #
This will contain our in-class examples for stage one.
## What we Need ##
* Understanding of the printing of the world data representation  
* Drawing of the world you made as an example  

## Step 1: Understanding ##  
We will look through and understand up to stage one of the project as well as provide an example.  
  
Inside of EloquentJavascript, we see that throughout chapter 16 he provides code and examples for this project. Lets take a look at how he represents his world.  
```js
let simpleLevelPlan = `
......................
..#................#..
..#..............=.#..
..#.........o.o....#..
..#.@......#####...#..
..#####............#..
......#++++++++++++#..
......##############..
......................`;
```  
This is his simple level he outlines as an example. You will make your own, smaller and more simple level for stage one.  
Note what each symbol means in terms of the level. In chapter 16, he mentions what each character means.  
* # represents a wall
* o represents a coin
* = represents a sliding lava block
* + represents a lava block
* @ is where the player starts

We should be able to read worlds and understand what they do, draw them, and print them that contain data we can understand.  
In the book, he creates a class `Level` that allows us to create a data representation of our level we made.
```js
class Level {
  constructor(plan) {
    let rows = plan.trim().split("\n").map(l => [...l]);
    this.height = rows.length;
    this.width = rows[0].length;
    this.startActors = [];

    this.rows = rows.map((row, y) => {
      return row.map((ch, x) => {
        let type = levelChars[ch];
        if (typeof type == "string") return type;
        this.startActors.push(
          type.create(new Vec(x, y), ch));
        return "empty";
      });
    });
  }
}
```
We will walk through this in class to grasp a better understanding.  
  
He also mentions in chapter 16 various states our game will handle. So, he makes a class called `State`.
```js
class State {
  constructor(level, actors, status) {
    this.level = level;
    this.actors = actors;
    this.status = status;
  }

  static start(level) {
    return new State(level, level.startActors, "playing");
  }

  get player() {
    return this.actors.find(a => a.type == "player");
  }
}
```
We will go through this in class to grasp a better understanding.
  


## Step 2: Demonstration ##
We will go through and demonstrate an example of stage 1, as well as walk through the process.  

## Step 3: Running and Submission ##
Lastly, we will go through how to submit onto Github if you need help.
