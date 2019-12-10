# Stage Two Example/In Class #
This will contain what we go over in class plus a possible stage two example.  
**NOTE:** This may or may not contain some code for stage 3 that should not be used in stage 2

## What we Need ##
* Intrinsic World Dynamics Demonstrated.  
 * at least 1 coin that wobbles
 * at least 1 demonstration of each lava block moving (falling/bouncing)
* Understanding of the code.  
  
**NOTE: While the code we go through below allows us for more than just instrinsic world dynamics, we only want the steps for intrinsic world dynamics. Only include what you think demonstrates intrinsic world dynamics and still runs.** 

## Step 1: Understanding ##  
*What is intrinsic world dynamics?*
**Intrinsic World Dynamics** are actions that happen in the world without the user causing them to happen.  
An example of this would be [the clock we saw in HW05](https://cs.indiana.edu/~dgerman/a290-js/spr2019/hw05.pdf "EJ Chapter 16")  
We will take a look at this clock in class.  

"Now we’re at the point where we can start adding motion—the most interesting aspect of the game. The basic approach, taken by most games like this, is to split time into small steps and, for each step, move the actors by a distance corresponding to their speed multiplied by the size of the time step. We’ll measure time in seconds, so speeds are expressed in units per second" - EJ

## Step 2: Working Through It ## 
### Hit Detection and Updates ###
This method tells us whether a rectangle (specified by a position and a size) touches a grid element of the given type:
```JavaScript
Level.prototype.touches = function(pos, size, type) {
  var xStart = Math.floor(pos.x);
  var xEnd = Math.ceil(pos.x + size.x);
  var yStart = Math.floor(pos.y);
  var yEnd = Math.ceil(pos.y + size.y);

  for (var y = yStart; y < yEnd; y++) {
    for (var x = xStart; x < xEnd; x++) {
      let isOutside = x < 0 || x >= this.width ||
                      y < 0 || y >= this.height;
      let here = isOutside ? "wall" : this.rows[y][x];
      if (here == type) return true;
    }
  }
  return false;
};
```  
  
The state update method uses touches to figure out whether the player is touching lava. The method is passed a time step and a data structure that tells it which keys are being held down. The first thing it does is call the update method on all actors, producing an array of updated actors. The actors also get the time step, the keys, and the state, so that they can base their update on those. Only the player will actually read keys, since that’s the only actor that’s controlled by the keyboard.  
```JavaScript
State.prototype.update = function(time, keys) {
  let actors = this.actors
    .map(actor => actor.update(time, this, keys));
  let newState = new State(this.level, actors, this.status);

  if (newState.status != "playing") return newState;

  let player = newState.player;
  if (this.level.touches(player.pos, player.size, "lava")) {
    return new State(this.level, actors, "lost");
  }

  for (let actor of actors) {
    if (actor != player && overlap(actor, player)) {
      newState = actor.collide(newState);
    }
  }
  return newState;
};
```
  
Overlap between actors is detected with the overlap function. It takes two actor objects and returns true when they touch—which is the case when they overlap both along the x-axis and along the y-axis.  
```JavaScript
function overlap(actor1, actor2) {
  return actor1.pos.x + actor1.size.x > actor2.pos.x &&
         actor1.pos.x < actor2.pos.x + actor2.size.x &&
         actor1.pos.y + actor1.size.y > actor2.pos.y &&
         actor1.pos.y < actor2.pos.y + actor2.size.y;
}
```
  
If an actor overlaps, collide method is called. Lava sets the state to `lost` while coins get collected and set to `won` if they're the last coin.  
```JavaScript
//Lava Collide
Lava.prototype.collide = function(state) {
  return new State(state.level, state.actors, "lost");
};

//Coin Collide
Coin.prototype.collide = function(state) {
  let filtered = state.actors.filter(a => a != this);
  let status = state.status;
  if (!filtered.some(a => a.type == "coin")) status = "won";
  return new State(state.level, filtered, status);
};
```
  
### Level Updates and Wobbling ###
"Actor objects’ update methods take as arguments the time step, the state object, and a keys object. The one for the Lava actor type ignores the keys object." - EJ  
This method makes a new position by getting the speed and time step from the previous position. If there is not object in the way, it moves there as expected. IF there is something there, the way it moves depends on what *type* of lava block it is. Bouncing lava reverses direction, dripping lava goes back to a default position.  
```JavaScript
Lava.prototype.update = function(time, state) {
  let newPos = this.pos.plus(this.speed.times(time));
  if (!state.level.touches(newPos, this.size, "wall")) {
    return new Lava(newPos, this.speed, this.reset);
  } else if (this.reset) {
    return new Lava(this.reset, this.speed, this.reset);
  } else {
    return new Lava(this.pos, this.speed.times(-1));
  }
};
```
  
Coins can **wobble** inside their own square. The `wobble` property is increased along with `time` multiplied by `speed` to create a wobble affect. This is their own update method:  
```JavaScript
const wobbleSpeed = 8, wobbleDist = 0.07;

Coin.prototype.update = function(time) {
  let wobble = this.wobble + time * wobbleSpeed;
  let wobblePos = Math.sin(wobble) * wobbleDist;
  return new Coin(this.basePos.plus(new Vec(0, wobblePos)),
                  this.basePos, wobble);
};
```
  
### Running the Game ###
You saw, [in chapter 14](https://eloquentjavascript.net/14_dom.html#animationFrame "chapter 14"), a function called `requestAnimationFrame`. It was a bit "clunky", so lets make a helper function that lets us handle that easier: 
```JavaScript
function runAnimation(frameFunc) {
  let lastTime = null;
  function frame(time) {
    if (lastTime != null) {
      let timeStep = Math.min(time - lastTime, 100) / 1000; //1/10 of a second
      if (frameFunc(timeStep) === false) return;
    }
    lastTime = time;
    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
}
```
  
Something that is important to note, is that if we hide the tab or window, our gmae will skip ahead.  
"When the browser tab or window with our page is hidden, requestAnimationFrame calls will be suspended until the tab or window is shown again. In this case, the difference between lastTime and time will be the entire time in which the page was hidden. Advancing the game by that much in a single step would look silly and might cause weird side effects, such as the player falling through the floor." - EJ.  
The function also converts to seconds.  
  
Now, lets make a function `runLevel` that is given an instance of the `Level` we made [in stage one on 12/05/2019](https://github.com/c212/fall2019-a290-js-lectures/tree/master/stageOne/12052019 "Stage One"). It displays the level, allows user to play, and clears the display if we won or lost.

```JavaScript
function runLevel(level, Display) {
  let display = new Display(document.body, level);
  let state = State.start(level);
  let ending = 1;
  return new Promise(resolve => {
    runAnimation(time => {
      state = state.update(time, arrowKeys);
      display.syncState(state);
      if (state.status == "playing") {
        return true;
      } else if (ending > 0) {
        ending -= time;
        return true;
      } else {
        display.clear();
        resolve(state.status);
        return false;
      }
    });
  });
}
```
  
Display and run.

## Step 3: Demonstration ##  
Use your html from stage one to run this. You will need to update it. The book provides this example:
```html
<link rel="stylesheet" href="css/game.css">

<body>
  <script>
    runLevel(simpleLevel, DOMDisplay);
  </script>
</body>
```
## Notes ##  
Refer to the [chapter 16 coding sandbox](https://eloquentjavascript.net/code/#16 "EJ Chapter 16 Code Sandbox") for a demonstration of the **full game** running.
  
For questions, comments, or concerns, please email me at woboland@iu.edu
