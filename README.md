# Roster API
Most of Roster's functionality will be accessed via an API. This should allow multiple front-ends to use the Roster infrastructure.


# Potential Applications
* Website for listing teams, players, scrims, etc.
* Single-page web app for organising matches in real-time.
* Android/iOS apps for notifying players and team leaders.


# Endpoints
## Leagues
One of the features of Roster will be the ability for anyone to organise a league or tournament series (may require approval). These can consist of group stages, before quarter- and semi-finals, or simply be a straightforward round robin. League matches will be organised automatically for participating teams.

* __/leagues__: POST creates a new league.
* __/leagues/:id:__: GET will return league information, such as current stage, skill level, start/finish dates. PUT will allow altering league settings. DELETE cancels the league.
* __/leagues/:id:/teams__: GET will return the teams participating in the league. League organisers can POST to enter a team for participation and DELETE to remove them.
* __/leagues/:id:/matches__: GET will return league matches that have been played or organised. POST will allow league organisers to manually create matches.

## Teams
* __/teams__: POST to create a new team. 
* __/teams/:id:__: GET will return team information. PUT will update basic team information such as name (provided you are a team leader). DELETE disbands the team (provided you are the team's creator). 
* __/teams/:id:/players__: GET will return players on the team. Team leaders can POST to add new players or DELETE to remove current players.
* __/teams/:id:/matches__: GET will return a list of matches for the team, with filtering options. Team leaders can POST to schedule new scrims for that team. Times and dates should be in GMT.

## Matches
Roster tries to make organising and playing matches as pain-free as possible. Using the [serveme.tf](https://serveme.tf) API, Roster will create a server, set up the map and league rules and keep track of players (no need to check someone is who they say they are.) Roster will also create a private mumble server for the duration of the match. Of course, team leaders can specify their own servers.

* __/matches__: GET will return all matches, with filtering options.
* __/matches/:id:__: GET will return match information such as teams, map, date and logs (if completed). PUT will allow league organisers to change match status (e.g. in disputes) or DELETE the match. For non-league scrims, team leaders (of the initiating team) can change map or date (if an opposing team hasn't been found) with PUT or cancel the scrim with DELETE. POST will be used for match-altering changes such as joining the game as opposing team or requesting/allowing/denying mercs.

## Players
* __/players__: GET will return a list of all players, with filtering options (for mercs, class, skill level etc.)
* __/players/:id:__: GET will return that player's information. _id_ can be a Steam ID. PUT will allow players to update their own information.
* __/players/:id:/teams__: GET will fetch all teams (past and present) the player was enrolled in (filterable).
* __/players/:id:/matches__: GET will fetch all matches the player was involved in as a team player or merc (filterable).


# Match Lifecycle
Matches are at the core of Roster, whether they're official league games or friendly scrims. In light of this, creating and playing matches should be easy. Roster should be facilitating, rather than getting in the way. To make sure most of the bases have been covered, here are some possible match lifecycles.

## A normal scrim.
1. Team leader for home team (HT) creates a scrim scheduled for 2 days from now at 1800 GMT on map pl_badwater.
2. Another team leader takes up the challenge. An email is sent to HT's leaders and the Rosterbot sends a message over Steam.
3. HT's leader checks the prospective team's skill level and past matches before approving the request.
4. The match is now active. Team leaders from both HT and the opposing team (OT) can request map changes, additional maps and time changes. OT requests an additional map, koth_wubwubwub which is approved by HT.
5. Half an hour before the match is due to start, Roster provisions a game and mumble server (if needed). Players will be notified by email/Steam/app.
6. Players reply to the email/message Rosterbot/use the app to say if they can make it or not.
7. Players joining the game will be indicated on the match page. At this stage, mercs can be requested for missing classes. Clicking the missing class slot on the match page should bring up a list of potential online mercs, with recent players displayed first.
8. HT requests a pyro merc. OT checks the merc's skill level and recent matches before approving them.
9. The merc is notified, with a link to the match page.
10. Once all players/mercs are approved and ready, the match starts. Game and mumble server links will be made available.
11. Roster talks with the game server over RCON and checks that joining players are those approved for the match. Discrepancies will be announced in game chat and on the match page.
12. When the teams want to play the next map (if more than one map exists), a HT leader must switch using the match page. Additional maps can also be requested by leaders from either team. Roster changes map to koth_wuwubwub.
13. All maps have been played, so HT ends the game, kicking all players and shutting down the server.
14. On completion of the match, results are calculated and a link to the logs from logs.tf is appended to the match information.
