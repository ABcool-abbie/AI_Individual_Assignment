# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Mei Koo
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: https://github.com/ABcool-abbie/AI_Individual_Assignment
Current stage and anything unfinished: Stage 05

## Initial prediction

Three observations the rival needs, including one it might not have:
A: 
- Its own position (start point) to know where the rival is starting from
- The target's position (end point) to know where to go.
- A vaild path (reachable or not) from start point to end point.

Seeing a pickup across a gap proves / does not prove reachability because:
A: Seeing a pickup across a gap does not prove reachability /  It does not prove a walkable NavMesh path exists, there could be some obstacles (eg. closed door/ cliff / invisible wall) in the NavMesh Surface between the two points.

One question about NPC behaviour:
A: In 007 First Light / Hitman, When a guard spots the player doing something suspicious (a question mark slider appears above his head) and the player break line of sight, he searches the player's last known position and player need to wait for a long time before the question-mark slider decays to zero and disappears and the guard later goes back to his routine. How does the search behave in 3D areas (with rooftops etc / different NavMesh layers)?

## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

Situation (Stage 1): 01_Normal, rival walking to scrap 11

| Step          | Code                                   | Debug Panel                          |
|---------------|----------------------------------------|-------------------------------------|
| Observation   | PickupSensor.Capture                   | Visible: 1, Memory: 1, Cargo: False |
| Facts         | DecisionPolicy.Infer                   | UsableTarget: True, Target: 11        |
| Decision      | DecisionPolicy.Choose                  | Task: Collect, Reason: "Retain current eligible target 11"         |
| Request       | RivalBrain → Navigator.Request → SetDestination | Requests:1               |
| Result        | Navigator.Poll → Classify              | Travel: Moving           |
| Game result   | MatchController                        | Cargo: True (scrap collected)       |
| Next decision | DecisionPolicy.Choose                  | Task: Deliver, Reason: "Cargo held: return to depot", Requests: 1 → 2         |

Situation (Stage 2): 03_Occlusion / Rule: candidate.Available && (candidate.Visible || candidate.Remembered)
| Step        | Code                  | Debug Panel                                        |
|-------------|-----------------------|----------------------------------------------------|
| Observation | PickupSensor.Capture  | Visible: 1    |
| Decision    | DecisionPolicy.Choose | Task: Collect, Reason: "Retain current eligible target 1" |
| Result      | —                     | Rival ignored the nearer hidden scrap and went to scrap 1 |
| Later       | PickupSensor.Capture  | Visible: 1 → 2 when the rival can spot the hidden one              |

## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart (F2) | find the nearest scrap and collect back to depot and continue / Press R to restart |  Collect target: scrap 11 → Deliver "return to depot with "Cargo: True" → RIVAL score 1/5 → Collect target: scrap 10 |
| Carrying cargo (F3) | Deliver to depot first before pursuing another scrap | Deliver with Target: -1, Cargo: True / Reason: "Cargo held: return to depot" → back to depot → Collect taget: scrap 10|
| Hidden / remembered pickup (F4) | Rival will ignore the nearer hidden scrap | Went to farther visible scrap + Visible 1 + Visible turn to 2 when rival can spot the hidden one on the way to collect the visible scrap |
| Target taken (F5) | Change to another target | Before: Collect target 0 ("Retain current eligible target 0"), Requests 1 / After the steal: Explore, target -1 ("No eligible known pickup: visit the next survey point"), Requests 2. Later: change to another target |
| Unreachable target (F6) | Fail to collect the unreachable target and switch to another target | Targeted scrap 0 ("Nearest eligible scrap 0…") → Travel Failed, "Route failed for pickup 0; ignore for 6 seconds" → switched to scrap 1 ("Retain current eligible target 1"), Moving. Tried scrap 0 again after 6s and failed. |
| No usable pickup (F7)  | Explore the whole NavMesh Surface | Task: Explore, Target: -1, Reason: "No eligible known pickup: visit the next survey point" → so moves between survey points instead of the whole NavMesh Surface (my prediction was wrong) |

Supplied test result, with any added tests identified:
Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened:
A:
Without the retention loop, Infer searches for the nearest eligible scrap again every decision (every 0.15 s), so a closer scrap can steal the target mid-route. I removed the loop to check: 2 fail(including Stage3_UsableCurrentTargetSurvivesCloserDistraction.) In all sences, it seems normal + the rival didn't visibly switch targets. The Reason line showed "Nearest eligible…" every time, never "Retain…".

What I changed and why it addresses the cause:
A:
Add the retention loop even it looks normal without it in game. Before searching for the nearest scrap, Infer now looks for the current target by Id and keeps it if it is still eligible. So a closer scrap can't steal a
target that is still useful, even though the scenes didn't happen to show a switch. All tests pass.

My counterexample or experiment: prediction, observed result, and one limitation:
A: 
Experiment: rival stopping distance 0.6 → 1.6 → 1.7 (collection radius 1.1)
Prediction: the rival would report Arrived while still too far to collect in both cases (1.6/1.7)
Observed result: at 1.6 it still collected (didn't match my prediction). At 1.7 it stopped in front of the target scrap and the scrap was not collected.
Limitation: the exact limit of stopping distance depends on speed + acceleration + approach angle /  stopping distance is where the agent starts braking and will slide a little far away instead of exact value (in this case 1.6).

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
