# TIL: Check BFS states before enqueueing, not after dequeueing

## What issue did I encounter?

While doing [3568. Minimum Moves to Clean the Classroom](https://leetcode.com/problems/minimum-moves-to-clean-the-classroom) with a canonical solution based on BFS, I wondered where I should check if the transition to the next stage was valid (considering boundaries, visited history, pruning, etc.):

```Python
queue.append(start_state)

while queue:
  level_size = len(queue)
  for i in range(level_size):
    state = queue.popleft()

    # Should I check BFS state validity here...?

    for next_state in calculate_next(state):

      # ... or maybe here?

      queue.append(next_state)
```

## My previous mental model

I used to think checking state immediately after dequeueing was cleaner - elegant single place, ideal for deciding if we want to elaborate next steps or if we reach end goal:

```Python
queue.append(start_state)

while queue:
  level_size = len(queue)
  for i in range(level_size):
    state = queue.popleft()

    if not is_valid(state):
      continue

    if is_goal(state):
      return success_value

    for next_state in calculate_next(state):
      queue.append(next_state)
```

However, LeetCode hit me with "Time Limit Exceeded" (TLE) during submission tests. Why?

## What I learned

Imagine traversing an utterly standard 2D board. In an upcoming step, three different adjacent cells can all transition into the target cell at (row=1, col=0), as below:

<img width="325" height="297" alt="image" src="https://github.com/user-attachments/assets/8b083b3e-1b5c-4460-9d54-505bb4fff826" />

BUT we only want to process (1, 0) once! Let's see what we get sticking to my previous mental model:

```Python
queue.append(start_state)
visited[start_row][start_col] = True

while queue:
  level_size = len(queue)
  for i in range(level_size):
    row, col = queue.popleft()

    # n+1 iteration: we need to handle (and filter out!) all 3 moves from previous iteration
    if visited[row][col]:
      continue
    visited[row][col] = True

    result = handle(row, col)

    # n iteration: calculate_next brings us move to (1, 0) from (0, 0), (2, 0) and (1, 1) 
    for next_row, next_col in calculate_next(state):
      queue.append(next_row, next_col)
```

We ended up with 3 entries for this field, even though 2 get skipped later - definitely not optimal. But if we do it before enqueueing:

```Python
queue.append(start_state)
visited[start_row][start_col] = True

while queue:
  level_size = len(queue)
  for i in range(level_size):
    row, col = queue.popleft()

    # n+1 iteration: we need to handle only one move from previous iteration
    result = handle(row, col)

    # n iteration: calculate_next brings us move to (1, 0) from (0, 0), (2, 0) and (1, 1) 
    for next_row, next_col in calculate_next(state):

      # filtering all unnecessary moves
      if visited[next_row][next_col]:
        continue
      visited[next_row][next_col] = True

      queue.append(next_row, next_col)
```

We are actually de-duplicating all the unnecessary work at the earliest possible state, keeping space and time complexity minimal and preventing TLE.

## Notes

Highly recommend you trying [3568. Minimum Moves to Clean the Classroom](https://leetcode.com/problems/minimum-moves-to-clean-the-classroom), really non-trivial task. See my solution at https://github.com/sokeks/LeetCode-Solutions/tree/main/LeetCode/Medium/3568-minimum-moves-to-clean-the-classroom .
