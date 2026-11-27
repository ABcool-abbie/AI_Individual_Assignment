# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Mei Koo
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository: 4e192ee31dd089d5d44d6fbcf528a8884806248b
Current stage and anything unfinished: Stage 01

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

Situation: 01_Normal, rival walking to scrap 11

| Step          | Code                                   | Debug Panel                          |
|---------------|----------------------------------------|-------------------------------------|
| Observation   | PickupSensor.Capture                   | Visible: 1, Memory: 1, Cargo: False |
| Facts         | DecisionPolicy.Infer                   | UsableTarget: True, Target: 11        |
| Decision      | DecisionPolicy.Choose                  | Task: Collect, Reason: "Retain current eligible target 11"         |
| Request       | RivalBrain → Navigator.Request → SetDestination | Requests:1               |
| Result        | Navigator.Poll → Classify              | Travel: Moving           |
| Game result   | MatchController                        | Cargo: True (scrap collected)       |
| Next decision | DecisionPolicy.Choose                  | Task: Deliver, Reason: "Cargo held: return to depot", Requests: 1 → 2         |

## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | | |
| Carrying cargo | | |
| Hidden / remembered pickup | | |
| Target taken | | |
| Unreachable target | | |
| No usable pickup | | |

Supplied test result, with any added tests identified:
Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened:
What I changed and why it addresses the cause:
My counterexample or experiment: prediction, observed result, and one limitation:

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
