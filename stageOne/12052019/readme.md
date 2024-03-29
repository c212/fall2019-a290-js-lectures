# Stage One Example/In Class #
This will contain our in-class examples for stage one.

## What we Need ##
* Understanding of the printing of the world data representation
* Print your world
* Drawing of the level ***you made*** as an example.

***DO NOT USE THE EXAMPLE LEVEL PROVIDED IN THE BOOK***

## Step 1: Understanding and Representation ##  
We will look through and understand up to stage one of the project as well as provide an example.  
  
Inside of EloquentJavascript, we see that throughout [chapter 16's](https://eloquentjavascript.net/16_game.html "EJ Chapter 16") he provides code and examples for this project. Lets take a look at how he represents his world.  
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
* `#` represents a wall
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
  
He also mentions in chapter 16 various states our game will handle. So, he makes a class called `State`:
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
The status can change between "lost" and "won" depending on our state. We will go through this in class to grasp a better understanding.
  
We also need to declare a `Vec` (vector) class to assist us:
```js
class Vec {
  constructor(x, y) {
    this.x = x; this.y = y;
  }
  plus(other) {
    return new Vec(this.x + other.x, this.y + other.y);
  }
  times(factor) {
    return new Vec(this.x * factor, this.y * factor);
  }
}
```
We will go through this in class for understanding.
  
Now, we should have a `player` class:
```js
class Player {
  constructor(pos, speed) {
    this.pos = pos;
    this.speed = speed;
  }

  get type() { return "player"; }

  static create(pos) {
    return new Player(pos.plus(new Vec(0, -0.5)),
                      new Vec(0, 0));
  }
}

Player.prototype.size = new Vec(0.8, 1.5);
```
We will go through this in class for understanding.
  
We should also have a `lava` class:
```js
class Lava {
  constructor(pos, speed, reset) {
    this.pos = pos;
    this.speed = speed;
    this.reset = reset;
  }

  get type() { return "lava"; }

  static create(pos, ch) {
    if (ch == "=") {
      return new Lava(pos, new Vec(2, 0));
    } else if (ch == "|") {
      return new Lava(pos, new Vec(0, 2));
    } else if (ch == "v") {
      return new Lava(pos, new Vec(0, 3), pos);
    }
  }
}

Lava.prototype.size = new Vec(1, 1);
```
We will go through this in class for understanding.
  
Lastly, we should have a `coin` class:
```js
class Coin {
  constructor(pos, basePos, wobble) {
    this.pos = pos;
    this.basePos = basePos;
    this.wobble = wobble;
  }

  get type() { return "coin"; }

  static create(pos) {
    let basePos = pos.plus(new Vec(0.2, 0.1));
    return new Coin(basePos, basePos,
                    Math.random() * Math.PI * 2);
  }
}

Coin.prototype.size = new Vec(0.6, 0.6);
```
We will go through this in class for understanding.
  
"We can now define the levelChars object that maps plan characters to either background grid types or actor classes." - EJ
```js
const levelChars = {
  ".": "empty", "#": "wall", "+": "lava",
  "@": Player, "o": Coin,
  "=": Lava, "|": Lava, "v": Lava
};
```
  
Now that we have our class definitions, lets check out creating an instance of our `Level` class.
```js
let simpleLevel = new Level(simpleLevelPlan);
console.log(`${simpleLevel.width} by ${simpleLevel.height}`);
// → 22 by 9
```
***Note:*** He prints out variables of our instance of `Level`. We are looking for printing and understanding of the whole level. We will go through this part in class.
  

## Step 2: Drawing and Understanding ##
We will go through and explore how to draw.  
  
Looking at the chapter, we see: "The following helper function provides a succinct way to create an element and give it some attributes and child nodes":
```js
function elt(name, attrs, ...children) {
  let dom = document.createElement(name);
  for (let attr of Object.keys(attrs)) {
    dom.setAttribute(attr, attrs[attr]);
  }
  for (let child of children) {
    dom.appendChild(child);
  }
  return dom;
}
```
  
We also see that: "A display is created by giving it a parent element to which it should append itself and a level object."
```js
class DOMDisplay {
  constructor(parent, level) {
    this.dom = elt("div", {class: "game"}, drawGrid(level));
    this.actorLayer = null;
    parent.appendChild(this.dom);
  }

  clear() { this.dom.remove(); }
}
```
  
For scaling:
```js
const scale = 20;

function drawGrid(level) {
  return elt("table", {
    class: "background",
    style: `width: ${level.width * scale}px`
  }, ...level.rows.map(row =>
    elt("tr", {style: `height: ${scale}px`},
        ...row.map(type => elt("td", {class: type})))
  ));
}
```
  
Now we add some css:
```css
.background    { background: rgb(52, 166, 251);
                 table-layout: fixed;
                 border-spacing: 0;              }
.background td { padding: 0;                     }
.lava          { background: rgb(255, 100, 100); }
.wall          { background: white;              }
```
***NOTE:*** We can add this to its own file called `game.css`
  
Now lets add drawing functionality: 
```js
function drawActors(actors) {
  return elt("div", {}, ...actors.map(actor => {
    let rect = elt("div", {class: `actor ${actor.type}`});
    rect.style.width = `${actor.size.x * scale}px`;
    rect.style.height = `${actor.size.y * scale}px`;
    rect.style.left = `${actor.pos.x * scale}px`;
    rect.style.top = `${actor.pos.y * scale}px`;
    return rect;
  }));
}
```

Add some more css: 
```css
.actor  { position: absolute;            }
.coin   { background: rgb(241, 229, 89); }
.player { background: rgb(64, 64, 64);   }
```

Create DOM:
```js
DOMDisplay.prototype.syncState = function(state) {
  if (this.actorLayer) this.actorLayer.remove();
  this.actorLayer = drawActors(state.actors);
  this.dom.appendChild(this.actorLayer);
  this.dom.className = `game ${state.status}`;
  this.scrollPlayerIntoView(state);
};
```

Add even more css:
```css
.lost .player {
  background: rgb(160, 64, 64);
}
.won .player {
  box-shadow: -4px -7px 8px white, 4px -7px 8px white;
}
.game {
  overflow: hidden;
  max-width: 600px;
  max-height: 450px;
  position: relative;
}
```

Finish display:
```js
DOMDisplay.prototype.scrollPlayerIntoView = function(state) {
  let width = this.dom.clientWidth;
  let height = this.dom.clientHeight;
  let margin = width / 3;

  // The viewport
  let left = this.dom.scrollLeft, right = left + width;
  let top = this.dom.scrollTop, bottom = top + height;

  let player = state.player;
  let center = player.pos.plus(player.size.times(0.5))
                         .times(scale);

  if (center.x < left + margin) {
    this.dom.scrollLeft = center.x - margin;
  } else if (center.x > right - margin) {
    this.dom.scrollLeft = center.x + margin - width;
  }
  if (center.y < top + margin) {
    this.dom.scrollTop = center.y - margin;
  } else if (center.y > bottom - margin) {
    this.dom.scrollTop = center.y + margin - height;
  }
};
```

***Now we can display our level!***
```html
<link rel="stylesheet" href="game.css">

<script>
  let simpleLevel = new Level(simpleLevelPlan);
  let display = new DOMDisplay(document.body, simpleLevel);
  display.syncState(State.start(simpleLevel));
</script>
```

## Step 3: Demonstration ##
Now, when we open our html file, we can see the simple level we made. It should be noted that we should be able to print the instance of our simple level, and understand what it means.
  
We can also use the `five.js` file we made in [Lab 02](https://cs.indiana.edu/classes/a290-js/fall2019/lab02.html "Lab 02") to display our game in the browser from your port. You would need to add the game files as well to silo. If you're on mac, you can upload files [using scp](https://research.csc.fi/csc-guide-copying-files-from-linux-and-mac-osx-machines-with-scp "scp tutorial"). If you are using windows, you can [use winscp](https://winscp.net/eng/index.php "winscp website").

## Misc Notes ##
* We seperated our code into 3 different files. We could have kept the `JavaScript` inside of the html `<script>` tags, but this may be messy and hard to organize later on.
* I did not add many comments to the code. Go through [EloquentJavascript chapter 16's](https://eloquentjavascript.net/16_game.html "EJ Chapter 16") to get a detailed inner working. You will need this for easier understanding.
  
For questions, comments, or concerns, please email me at woboland@iu.edu
