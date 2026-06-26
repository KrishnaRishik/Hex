# HEX

A cave-diving roguelite of **Unstable power** every weapon rides a burning fuse. Fire it dry and the next curse in your queue takes over; run empty and it backfires, costing you health and conjuring something new. You never get to keep a favourite. You adapt, or you die.

** Play (one click, no install):** [your GitHub Pages URL]
**Code:** The whole game is one self-contained `index.html`.

## Controls

| Action | Keys |
|---|---|
| Move | `A` `D` or `←` `→` |
| Jump (double-jump) | `Space` / `↑` (press twice) |
| Shoot toward aim | **click**, or `K` |
| Drop through a ledge | hold `↓` / `S` |
| Pause | `P` or `Esc` |

## What's interesting under the hood

- **Unstable-power core loop:** a fuse on every weapon, a 3-slot queue that feeds you the next curse, and a backfire-and-conjure when you run empty.
- **A black-market economy:** persistent gold, a menu loadout, and shops between depths (after 3, 5, 10, then every two floors).
- **PCG caves with a reachability guarantee:** cellular-automata caves, then a double-jump-aware flood-fill that walls off anything the player can't actually reach (verified across 400+ seeds).
- **AI as a feature, not the headline:** when your queue empties, the curse conjures a fresh weapon — generated into a constrained, validated schema, async-buffered, with a baked batch so it works offline. Pipeline lives in `/ai` (`worker.js`, `generate-weapons.mjs`, `weapons-ai.json`). The game is fully playable without it.
- No assets — plain shapes and colour.

---

# Submission — Written Answers

## Question 1 : Build a Playable Prototype (Core Loop + D1)

**HEX is the prototype.** Play it at the link above; the full design writeup (pitch, core loop, day-1 hook, metagame, monetization, AI usage, shipping plan, and reference-game teardown) is in **`HEX_writeup.pdf`** in this repo.

The one-paragraph version: HEX is a real-time, roguelike cave platformer about going deeper while maintaining unstable power. Every weapon has a fuse, you can use one weapone while having a queue three more, and as fuses burn out the queue feeds you the next Hex. Run empty and it backfires for health and conjures a new weapon. The **day-1 hook** is a visible unlock ladder that fires its first reward on a brand-new player's very first run, plus a persistent gold wallet you can spend at a black market between levels. Hex also features a date-seeded Daily Curse.

---

## Question 2 : Gameplay Insights & Strategy

### An overdue-for-reinvention genre: durable social deduction

**The genre: the Mafia/Werewolf lineage, free-to-play, mobile.**

Among Us is one of the most popular mobile games in 2020. It went from near-zero to one of the most-played games on earth in months, then fell almost as fast. The game was constant with no change but it got boring fast, the appetite for lying to your friends is permanent. Mafia has worked at sleepovers for decades. Nobody had built a mobile game that brought out the essence of mafia and *engineered to keep* it. Among Us had the brilliance of a party toy and the retention too.

Three structural reasons the genre still hasn't been cracked:

1. **The empty-lobby problem.** Deduction needs a full room of humans, right now, to function. Off-peak, in a small region, or for a new player at 2am, the lobby is dead, leaving players waiting for hours just to start the game. You can't even solo for five minutes while you wait.
2. **Nothing to sell.** The concept of the game revolves around social interaction in the game, not progression. There is no gear, no power, nothing that the players would want to move towards, barely a metagame. The only honest monetization is cosmetics.
3. **Toxicity.** Lying to strangers curdles fast when it hits the wrong spot or the wrong person, reputation is damaged and importantly it could lead to conflicts and harassment, there is no proper moderation which a quick-match party game should have.

**What a breakout would actually take** 

- **Kill the empty lobby with believable AI players.** A match should be instant and full at any hour, in any region, for a brand-new player. If the search takes more than 5 minutes, the lobby can be filled with agents good enough to accuse, defend, and deceive convincingly. This is the single biggest unlock, and it's only recently become possible. Although deceiving AI isn't always as satisfying as fooling a real person, so the system should still prioritize humans, auto-switching regions and adding crossplay to widen the player search, with AI as the fallback that keeps the lobby instant. 
- **Monetize identity and creators, not power.** Cosmetic personas, a creator economy for custom roles and maps, seasonal social events, a player membership which allows the player to create separate rooms and gamemode and release on a marketplace for other players to try.


One of my favorite mobile f2p games is Genshin Impact, The game allows all sorts of players to play the game and has a variety of features which bring in a huge player base, The game has a very well built progression and story line, and also offers other content for players who do not enjoy constant story. This game also features co-op functionality which a lot of players are a huge fan of.
If I could steal one feature from this game, it would be the cadence, the game rewards the player after completion of each activity. This not only motivates players to continue playing but also encourages the players to spend their rewards towards items in game.

## Question 3 : Design Specification (survivor-like feature)

### Feature: "The Swarm Learns" , an adaptive adversary that drafts against your build

**Rationale (why this feature).** Survivor-likes are dopamine-rich but decision-poor: after the first few minutes your build outpaces the horde and the run becomes solved, you're watching numbers, not making choices. The mid-run upgrade picks, the genre's one real decision, lose their stakes the moment you know the optimal path. "The Swarm Learns" attacks that central weakness *without* adding input complexity (combat stays fully auto): the horde **reacts to your build**, so every upgrade pick becomes a read-and-counter against an opponent instead of a lookup. It keeps the genre's signature choice meaningful across the whole run, and it manufactures "I outsmarted the swarm" stories the genre currently can't tell.

**Design intent (how it works).**
- A director tracks your **dominant damage profile** (heavy projectile, AoE/burn, melee aura, crit/burst) via a rolling tally of where your damage comes from.
- Every ~90 seconds it **adapts**, introducing a counter-archetype to your dominant profile: go all-projectile and it spawns fast, weaving flankers that punish straight-line aim; go all-AoE and it spawns sparse, high-HP elites that AoE can't farm efficiently.
- Adaptations are **telegraphed one wave ahead** the next level-up draft is seeded with at least one pick that answers the incoming counter, so you can always respond, but you have to *notice* and *choose* to.

When the director commits to an adaptation, a thin banner slides across the top of the play field, *"The Swarm is adapting to your fire"* with a small icon of the incoming counter-archetype and a countdown ring lasting fewe seconds. This is information meant to provoke a *decision*. The countdown does two jobs at once. It gives a skilled player a window to reposition before the wave lands, and it builds a beat of anticipation so the wave arrives as a payoff rather than a surprise. The player can literally *see* the swarm's answer on the field. Crucially it is never forced: the counter sits beside the usual greedy upgrades, so the player chooses between pressing their build harder or bending to answer the swarm. That one recurring choice, "*commit or adapt*" is the entire engine of the feature, and it repeats with rising stakes at every adaptation tier.

