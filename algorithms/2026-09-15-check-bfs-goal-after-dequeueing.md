# TIL: Check BFS goal after dequeueing

## What issue did I encounter?

After analyzing where to check state validity BFS state in my [previous post](algorithms/2026-09-14-check-bfs-states-before-enqueueing.md) I wondered: should I also check if the end goal has been reached **before** enqueueing the next state?

```Python
queue.append(start_state)  # + populating visited structure

while queue:
  level_size = len(queue)
  for i in range(level_size):
    state = queue.popleft()

    # (row, col) handling code

    for next_state in calculate_next(state):
      # checking BFS state + populating visited array

      # SHOULD I CHECK THE GOAL CONDITION HERE...?

      queue.append(next_state)
```

## My previous mental model

If checking BFS state just before enqueueing was a good idea and saved us from processing duplicate entries, it felt natural to place the goal check right next to it:

```Python
queue.append(start_state)  # + populating visited array

while queue:
  level_size = len(queue)
  for i in range(level_size):
    state = queue.popleft()

    # state handling code

    for next_state in calculate_next(state):
      if not is_valid(next_state) or visited[next_state]:
        continue
      visited[next_state]

      if is_goal(next_state):
        return success_value 

      queue.append(next_state)
```

However, what if the exit conditions are satisfied right away? Without any need to move anyhow?

## What I learned

In the situation described above, either one would need to reach next state's handling code, which not always may be possible due to other dependencies, or most likely add duplicated code back around enqueueing `start_state`. That's why, the canonical approach will be to move the goal checking just after dequeueing:

```Python
queue.append(start_state)  # + populating visited array

while queue:
  level_size = len(queue)
  for i in range(level_size):
    state = queue.popleft()

    if is_goal(state):
      return success_value 

    # state handling code

    for next_state in calculate_next(state):
      if not is_valid(state) or visited[next_state]:
        continue
      visited[next_state]

      queue.append(next_state)
```

## Notes

Checking the goal before pushing to the queue allows you to terminate the search **one level earlier** (as soon as the target state is generated, rather than waiting for it to be dequeued). If milliseconds matter on competitive programming platforms like LeetCode, pair early goal checking with a explicit `start_state` check upfront.
