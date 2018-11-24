# Assignment 8: Comments

## Answer Keys

### Maze

The general idea to solve this problem involves recursively attempting to move up, right, down, left.  Unlike the past recursive problems, however, here we need to keep track of whether Scully has visited a cell before so that she does not repeatedly visit the same cell over and over again.   

Like any recursive problem, we need to first consider the base (most trivial) case.  For this problem, the simplest case is when Scully is already at the border so she has already escaped. 

If Scully is still in the maze, then she should try to escape by moving up.  If she escaped, then we are done!  Otherwise, she should move one step back to where she was previously, and try to escape by moving right, and so on.

The following _pseudocode_ (English-like, informal, code) explains this:

```
can_escape() {
  if already at the border
    return true

  for each direction up, right, down, left:
    move in that direction if possible
    if can_escape()
      return true
    else
      move back

  return false (since cannot escape in all four directions)
```

Moving back is important since this is where Scully back tracks to try another path.

Translating the idea above into C, we have the following:

```C
/**
 * Try to move one step in the maze in the given direction.
 *
 * @param[in,out] maze The 2D array representing the maze.
 * @param[in]     x    The current x position.
 * @param[in]     y    The current y position.
 * @param[in]     dx   The x direction to move (-1, 0, 1).
 * @param[in]     dy   The y direction to move (-1, 0, 1).
 * @param[in,out] steps The number of steps taken so far.
 *
 * @return true if we successfully moved.  false otherwise.
 */
bool move_one_step(char **maze, long x, long y, long dx, long dy, long *steps)
{
  if (maze[x + dx][y + dy] != EMPTY) {
    return false;
  }

  maze[x][y] = EMPTY;
  maze[x + dx][y + dy] = USER;
  *steps += 1;

  return true;
}

/**
 * Try to escape the maze from the position x, y.
 *
 * @param[in]     maze    The 2D array representing the maze.
 * @param[in,out] visited The 2D array to remember whether a cell has been
 *                        visited before.
 * @param[in]     m       The number of rows in the maze.
 * @param[in]     n       The number of cols in the maze.
 * @param[in]     x       The x position of Scully.
 * @param[in]     y       The y position of Scully.
 * @param[in,out] steps   The num of steps that has been taken.
 */

#define NUM_OF_DIRS 4
#define X 0
#define Y 1
bool can_escape(char **maze, bool **visited, long m, long n, long x, long y, long *steps){

  visited[x][y] = true;

  // If already at the border, then escape.
  if (x == 0 || x == m-1 || y == 0 || y == n-1) {
    return true;
  }


  for (long dir = 0; dir < NUM_OF_DIRS; dir += 1) {
    // For each direction.
    char delta[NUM_OF_DIRS][2]={ {-1, 0}, {0,  1}, {1,  0}, {0, -1} };
    char dx = delta[dir][X];
    char dy = delta[dir][Y];

    if (!visited[x + dx][y + dy]) {
      bool can_move = move_one_step(maze, x, y, dx, dy, steps);
      if (can_move) {
        print_maze(maze, m, *steps);
        if (can_escape(maze, visited, m, n, x+dx, y+dy, steps)) {
          return true;
        }
        move_one_step(maze, x+dx, y+dy, -dx, -dy, steps);
        print_maze(maze, m, *steps);
      }
    }
  }
  return false;
}
```

### Fill

The idea of `fill` is similar to `maze`, except that now, we fill the different neighboring pixels with color, and we do not back track.  This question meant to contrast between the execution of a recursive function with and without backtracking.

```C
/**
 * Fill a 2D canvas of size m x n, starting from position (x, y), replacing
 * current color `curr`, with color `new`.
 *
 * @param[in,out] canvas The 2D canvas to flood fill with new color.
 * @param[in] m The width of the canvas.
 * @param[in] n The height of the canvas.
 * @param[in] x The starting x position to fill.
 * @param[in] y The starting y position to fill.
 * @param[in] curr The color to be replaced.
 * @param[in] new  The new color.
 */
void fill(char **canvas, long m, long n, long x, long y, char curr, char new) {
  long delta[NUM_OR_DIRS][2] = { {0, 1}, {0, -1}, {1, 0}, {-1, 0}};
  for (long dir = 0; dir < NUM_OR_DIRS; dir += 1) {
    long new_x = x + delta[dir][X];
    long new_y = y + delta[dir][Y];
    if (new_x >= 0 && new_x < m && new_y >= 0 && new_y < n 
         && canvas[new_x][new_y] == curr) {
      canvas[new_x][new_y] = new;
      fill(canvas, m, n, new_x, new_y, curr, new);
    }
  }
}
```
