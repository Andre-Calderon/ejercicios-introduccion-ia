Trained 1500 episodes on config\mi_cueva_4x4.yaml
Mean score (all): 446.3
Mean score (last 50): 837.6
Q-table size: 816 state-action pairs

--- Greedy demo (epsilon = 0) ---

Step 0  Score 0  IN CAVE
 4 | P  G  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | >  .  .  . 
      1  2  3  4
Percept [None]
Agent: learning
----------------------------------------
Action: Forward
Step 1  Score -1.0  IN CAVE
 4 | P  G  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  >  .  . 
      1  2  3  4
Percept [None]
Reward -1  (moved)
----------------------------------------
Action: TurnLeft
Step 2  Score -2.0  IN CAVE
 4 | P  G  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  ^  .  . 
      1  2  3  4
Percept [None]
Reward -1  (ok)
----------------------------------------
Action: Forward
Step 3  Score -3.0  IN CAVE
 4 | P  G  .  . 
 3 | .  .  .  P 
 2 | .  ^  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Stench]
Reward -1  (moved)
----------------------------------------
Action: Forward
Step 4  Score -4.0  IN CAVE
 4 | P  G  .  . 
 3 | .  ^  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [None]
Reward -1  (moved)
----------------------------------------
Action: Forward
Step 5  Score -5.0  IN CAVE
 4 | P  ^  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze, Glitter]
Reward -1  (moved)
----------------------------------------
Action: Grab
Step 6  Score -6.0  IN CAVE
 4 | P  ^  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze]
Reward -1  (grabbed_gold)
----------------------------------------
Action: TurnRight
Step 7  Score -7.0  IN CAVE
 4 | P  >  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze]
Reward -1  (ok)
----------------------------------------
Action: TurnRight
Step 8  Score -8.0  IN CAVE
 4 | P  v  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze]
Reward -1  (ok)
----------------------------------------
Action: Forward
Step 9  Score -9.0  IN CAVE
 4 | P  .  .  . 
 3 | .  v  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [None]
Reward -1  (moved)
----------------------------------------
Action: TurnRight
Step 10  Score -10.0  IN CAVE
 4 | P  .  .  . 
 3 | .  <  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [None]
Reward -1  (ok)
----------------------------------------
Action: Forward
Step 11  Score -11.0  IN CAVE
 4 | P  .  .  . 
 3 | <  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze]
Reward -1  (moved)
----------------------------------------
Action: TurnLeft
Step 12  Score -12.0  IN CAVE
 4 | P  .  .  . 
 3 | v  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [Breeze]
Reward -1  (ok)
----------------------------------------
Action: Forward
Step 13  Score -13.0  IN CAVE
 4 | P  .  .  . 
 3 | .  .  .  P 
 2 | v  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [None]
Reward -1  (moved)
----------------------------------------
Action: Forward
Step 14  Score -14.0  IN CAVE
 4 | P  .  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | v  .  .  . 
      1  2  3  4
Percept [None]
Reward -1  (moved)
----------------------------------------
Action: Climb
Step 15  Score 985.0  CLIMBED
 4 | P  .  .  . 
 3 | .  .  .  P 
 2 | .  .  W  . 
 1 | .  .  .  . 
      1  2  3  4
Percept [None]
Reward +999  (climbed_with_gold)
----------------------------------------
Result: climbed with gold  steps=15  score=985.0
