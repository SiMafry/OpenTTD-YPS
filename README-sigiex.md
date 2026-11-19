OpenTTD Decouple patch - sigiex 1.7.8
=====================================

Modified version of Palo123's Decouple patch (OpenTTD-YPS, branch "Decouple").

Base:   https://github.com/Palo123/openttd-yps  branch Decouple
        commit 251e5384176afa8e3faed699ef80bd2a3f1a7565 (2018-08-14,
        "Change: set immutable variables after user_data callback")
        = the build that shows itself as "20180814-Decouple".


Built and tested as 32-bit Windows (MinGW, i686) and on Linux.

Savegames
---------
Savegame version 201 (sigiex 1.3 - 1.6.x) and 202 (sigiex 1.7+).
Saves of the original Decouple build (version 200) load fine and are
converted. Saves from sigiex cannot be loaded by the original build.

Changes
-------
Crash fixes
- Keep the loading state when a loading train is reversed in a station
  (assertion in economy.cpp "front->current_order.IsType(OT_LOADING)");
  saves with this broken state are repaired when loaded.
- Decouple assertion with implicit orders.
- Cloned trains of wagons only were left marked as free wagons (crash).
- Order list pool check when restoring orders (pool assertion).
- Build fixes for newer MinGW headers.

Coupling / decoupling
- Decouple option "Own orders": each part continues with its own orders.
- Wagon groups with "hard" own orders (1.7): every vehicle belongs to a
  wagon group; one vehicle of the group (anchor) keeps the group's own
  orders and the last place reached in them. The orders of a train are
  only a working copy; at decoupling ("Own orders") and at sorting every
  part gets the own orders of its group again (a part with a locomotive
  gets the locomotive's own orders). Own orders change only when the
  player edits a train that is just that one group. No guessing, so
  routes of different groups never get mixed up.
- Decoupled parts wait for each other instead of colliding.
- Decouple with a fixed number of units keeps a train that is not longer.
- "Go to couple": maximum waiting time in days (counted only while waiting),
  "same destination" condition.
- A coupling train no longer runs into the waiting train when that one has
  reserved track ahead.
- A conditional order right after "Go to couple" is evaluated again after
  joining (so it sees the whole train), with a new path.

Shunting yards (wagon sorting)
- Decouple option "Sort": in a station with a train depot next to it, the
  locomotive keeps the wagon groups going where it goes; the other groups
  are stored in the depot (not animated - "teleport"). Groups go on in
  their own route at every yard.
- "Wait for load": the locomotive waits (in the yard depot, at most the
  timetabled wait time) for groups that go its way; direct hand-over of
  waiting groups; maximum train length in units.
- Cargo filter for sorting: take along only groups that carry a chosen
  cargo (and go where the locomotive goes).
- A train that sorted in a yard turns around by running around (the
  locomotive goes to the other end) instead of flipping the whole train,
  which left it pushed.
- Clone trains of wagons only (to create wagon groups quickly).

Orders
- "Turn around" order: turn the train around where it stands, either
  "push" (the whole train is flipped, the locomotive stays where it is) or
  "run around" (the locomotive moves to the other end).
- Conditional order "Number of vehicles" (articulated vehicles count once,
  as decoupling counts them).
- Conditional order "Free platforms in station" (trains): number of
  platforms of a chosen station with no train and no reservation; the
  station is picked by clicking it on the map.

Other
- A starting train waits until the track to the next signal is free.
- Advisor setting: warn when a train runs pushed (no locomotive in front)
  for N days in a row (standing still not counted; 0 = off).

Code was written with the help of an AI (Claude, Anthropic).
