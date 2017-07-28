# Heuristic Analysis

For my agent the initial heuristic selection was based on intuition from playing the game
a few times on paper manually.

Something I noticed quickly was that this variant of isolation is quite different from the one played in the classroom.
The knights jump means that partitions just aren't as valuable, and that number of available moves isn't
necessarily a great proxy for how many moves the user *might* arrive at, and that over time I tended (especially on larger
boards) to move towards areas that were sparsely filled rather than densely filled (even if I happened to have a lot of moves
available), and away from the edges.

To try to represent this I came up with the idea of tracking secondary moves (the places I could jump to on the next move
after making a knights move) that were available from my current position.  The most obvious ones are layed out like this:

```
[ ] [ ] [ ] [ ] [ ] [ ] [ ]
[ ] [ ] [1] [2] [1] [ ] [ ]
[ ] [1] [2] [ ] [2] [1] [ ]
[ ] [2] [ ] [P] [ ] [2] [ ]
[ ] [1] [2] [ ] [2] [1] [ ]
[ ] [ ] [1] [2] [1] [ ] [ ]
[ ] [ ] [ ] [ ] [ ] [ ] [ ]
```

each "1" represents a primary move for the player, each "2" is a secondary move that could be jumped to from a 1.  If you
are close to the edge, or jumping into densely blocked territory, you'll have less 2s available.

## Heuristic 1: Weighted Mobility

This one makes no use of the secondary moves, it just weights the players available moves based on my intuition
(more moves for me is worth more than less moves for the opponent, unless he's down to just 1 legal move or less.

To do this I weighted *my* moves by 0.7, and the opponents moves by 0.3, but granted a 3-point bonus
if the opponent was being restricted to 1 move or less.


## Heuristic 2: Secondary Mobility

This function makes use of secondary moves both for the player, and the opponent.  It takes a bit of time to calculate so
timeout thresholds have been increased.

This is the same idea as "my moves minus his moves", but adds an additional 50% credit for my secondary moves and
his secondary moves.

## Heuristic 3: Weighted Secondary Mobility

This scoring method combines 1 and 2, it both weights the primary moves available, and adds the secondary moves available (
secondaries weighted by 0.5).

## tournament results:

```
                        *************************
                             Playing Matches
                        *************************

 Match #   Opponent    AB_Improved   AB_Custom   AB_Custom_2  AB_Custom_3
                        Won | Lost   Won | Lost   Won | Lost   Won | Lost
    1       Random       6  |   4    10  |   0     8  |   2     9  |   1
    2       MM_Open      7  |   3     6  |   4     6  |   4     7  |   3
    3      MM_Center     9  |   1     7  |   3     7  |   3     9  |   1
    4     MM_Improved    6  |   4     6  |   4     8  |   2     8  |   2
    5       AB_Open      5  |   5     5  |   5     6  |   4     4  |   6
    6      AB_Center     6  |   4     6  |   4     6  |   4     5  |   5
    7     AB_Improved    5  |   5     5  |   5     3  |   7     6  |   4
--------------------------------------------------------------------------
           Win Rate:      62.9%        64.3%        62.9%        68.6%
```

At least in this small sample of games, Weighted Secondary Mobility seems to play favorably against AB_Improved (6 to 4) and
eeked out a higher winning percentage overall (68.6% to 62.9%).  These are pretty close results though, I think it would be
fair to say that it's not demonstrably better, all 3 heuristics perform fairly close to the simpler (and faster to calculate)
my_moves - your_moves.

## Recommendation:

In choosing an algorithm, I have no reason not to prefer AB_Custom_3 (Weighted Secondary Mobility).
It achieved a slightly higher win-rate than all 3 other algorithms and it's not markedly more complex
than the others to calculate.  It also has the property of being intuitively appealing (that is, staying
away from the sides and dense areas of play "feels" correct), which should make it rather
easy to debug potential problems in implementation.  One downside is the weights
are rather arbitrarily chosen by me.  Further work could be done to calibrate
these weights with longer-term experimentation.
