# Stage Three Example/In Class #

## What we want ##
* Player movement.  
  * Player can move.  
  * player can die.  
  * player can win.  
* Your own set of levels, that we can play through.  
  * at least 3 levels.  
  * must demonstrate collection of coins, and both types of lava.  
**NOTE:** They do not have to super big! Just enough to be playable.  

## Step 1: Understanding ##
We have added almost everything! Our game is almost done! Now we just need to get the player moving.  

## Step 2: ##

### Player Movement and Tracking Keys ###
Player motion is handled slightly different. We need to handle both the x and y axis seperatly, because a wall should not stop us falling but the floor should. We can move left or right if there are now walls in the way and up. We can also fall:  
```JavaScript
const playerXSpeed = 7;
const gravity = 30;
const jumpSpeed = 17;

Player.prototype.update = function(time, state, keys) {
  let xSpeed = 0;
  if (keys.ArrowLeft) xSpeed -= playerXSpeed;
  if (keys.ArrowRight) xSpeed += playerXSpeed;
  let pos = this.pos;
  let movedX = pos.plus(new Vec(xSpeed * time, 0));
  if (!state.level.touches(movedX, this.size, "wall")) {
    pos = movedX;
  }

  let ySpeed = this.speed.y + time * gravity;
  let movedY = pos.plus(new Vec(0, ySpeed * time));
  if (!state.level.touches(movedY, this.size, "wall")) {
    pos = movedY;
  } else if (keys.ArrowUp && ySpeed > 0) {
    ySpeed = -jumpSpeed;
  } else {
    ySpeed = 0;
  }
  return new Player(pos, new Vec(xSpeed, ySpeed));
};
```
The gravity strength, jumping speed, and pretty much all other constants in this game have been set by trial and error. I tested values until I found a combination I liked." - EJ. 
  
Now we will get into handling the action key presses. We want them to be able to be held down and have our player move. Another thing to note is that on most browsers, key presses can be used to scroll. `preventDefault` allows us to get around that issue. The function provided below is given an arrat of key names, will return an object that will allow us to track the current position of the keys. It allows us to track the arrow keys. `keydown` refers to when the key is pressed down:
```JavaScript
function trackKeys(keys) {
  let down = Object.create(null);
  function track(event) {
    if (keys.includes(event.key)) {
      down[event.key] = event.type == "keydown";
      event.preventDefault();
    }
  }
  window.addEventListener("keydown", track);
  window.addEventListener("keyup", track);
  return down;
}

const arrowKeys =
  trackKeys(["ArrowLeft", "ArrowRight", "ArrowUp"]);
```

### Running the Game ###
We can use the `runLevel` function you made in stage 2. But what if you have multiple leves? Lets make a helper function for that:  
```JavaScript
async function runGame(plans, Display) {
  for (let level = 0; level < plans.length;) {
    let status = await runLevel(new Level(plans[level]),
                                Display);
    if (status == "won") level++;
  }
  console.log("You've won!");
}
```

## Step 3: Demonstration ##
You can use the html you made in stage 1. Below is how the book calls `runGame`:
```html
<link rel="stylesheet" href="css/game.css">

<body>
  <script>
    runGame(GAME_LEVELS, DOMDisplay);
  </script>
</body>
```
## Notes ##
* `plans` parameter in `runGame` is an array containing string representations of your game, similiar to the single small level you made in stage one.  
* `GAME_LEVELS` is defined in a file that can be found on EloquentJavascript's coding sandbox. I recommend you do something similiar, but with your own levels. It is essentially an array containing levels, such as the one you made for Stage 1.  
* Refer to the [chapter 16 coding sandbox](https://eloquentjavascript.net/code/#16 "EJ Chapter 16 Code Sandbox") for a demonstration of the **full game** running.  
  
For questions, comments, or concerns, please email me at woboland@iu.edu
