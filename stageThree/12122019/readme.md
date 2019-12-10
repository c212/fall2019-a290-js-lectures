# Stage Three Example/In Class #

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
