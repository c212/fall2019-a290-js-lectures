# Stage Two Example/In Class #
This will contain what we go over in class plus a possible stage two example.  

## What we Need ##
* Intrinsic World Dynamics Demonstrated.  
* Understanding of the code.  

## Step 1: Understanding ##  
*What is intrinsic world dynamics?*
**Intrinsic World Dynamics** are actions that happen in the world without the user causing them to happen.  
An example of this would be [the clock we saw in HW05](https://cs.indiana.edu/~dgerman/a290-js/spr2019/hw05.pdf "EJ Chapter 16")  
We will take a look at this clock in class.  

"Now we’re at the point where we can start adding motion—the most interesting aspect of the game. The basic approach, taken by most games like this, is to split time into small steps and, for each step, move the actors by a distance corresponding to their speed multiplied by the size of the time step. We’ll measure time in seconds, so speeds are expressed in units per second" - EJ

## Step 2: Working Through It ##  
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


## Step 3: Demonstration ##  

## Notes ##  
