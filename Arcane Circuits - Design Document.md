# Overview

Arcane Circuits is a logic system I've envisioned for Hytale, the long-awaited game developed by Hypixel Studios that has recently entered open beta.

While this system has some resemblance to Minecraft's redstone, the flavor and technical considerations were thought through from the ground up. I am deeply familiar with the quirks from both Java and Bedrock Minecraft, and I've been thinking about how they can be improved on for a long time.

Arcane Circuits is a logic architecture, meaning it handles signals, gates, and state. It's designed to complement a physics-based system (like Create or Hynergy) as a way for players to control their creations in precise ways.

Arcane Circuits has three main philosophical points behind every design decision, which this document will lay out the specifics of and describe how they affect the design considerations. These precepts are:

- No Bluetooth Power
- Emergent Complexity Only
- Don't Break Immersion

Originally this was planned as a standalone mod. However, with @Simon_Hypixel's recent announcement regarding the inclusion of automation systems in Hytale, I've decided to change tack and aim this project towards being a technical showcase and a demonstration of my opinions on how the logic portion of such a system should work in a sandbox game like Hytale.

Luckily, @Simon_Hypixel's wording in the announcement indicated to me that we agree on the basic constraints, including a fantasy aesthetic and the importance of simplicity enabling emergent complexity.

As a result, this document was made with very little time (with my conception of the original mod starting in mid-January, and the writing this document starting on January 26, 2026).

All documentation was made by human hands. However, AI tools assisted with rubber-ducking design decisions, searching through Hytale's documentation, and answering my questions about Java syntax.
# Gameplay Philosophy

This system's gameplay is built on three main philosophical precepts, which I will describe the reasoning for and the details of in this section.
### No Bluetooth Power

Arcane Circuits is built with the explicit constraint that the circuits must *ALWAYS* make visual sense. If every block in the world except the ones involved in this system were to turn invisible, the circuits must still be complete and make visual sense.

To explain the reasoning for this being the first and most important rule for Arcane Circuits, I must explain how Minecraft violates this principle.

As a long-term technical player of Minecraft I am familiar with the quirks of redstone, and I have a deep understanding of how those systems work in practice. One thing I have always taken issue with is Minecraft's attitude towards self-consistency and realism with power transmission, the worst of its sins being the inclusion of Quasi-Connectivity.

For context, Quasi-connectivity is an old bug that allows pistons, droppers, and dispensers to be powered by anything that would soft-power the block of air one block above them, which happened as a result of pistons and droppers borrowing code from doors when they were first introduced. When the bug was fixed and the technical community revolted, Mojang re-implemented the bug purposefully.

Other than that, redstone power can be transmitted through most (but not all) non-redstone blocks in the game, and has inconsistent connection logic. In addition, much of the behavior that technical players take advantage of depends on the orientation of components relative to the world, and on arbitrary rules regarding update order.

It is my opinion that these quirks are the reason so many new players are intimidated by redstone when they first dip their toes in the water, either by experimenting themselves or by watching tutorials or reading discussions about the topic online. My system is built from the ground up to sidestep these problems entirely by being as self-explanatory and intuitive as possible.

Every design decision has been made with visual coherence in mind.
### Emergent Complexity Only

When building a logic system like Minecraft's redstone or Arcane Circuits, it is the developer's responsibility to balance the twin desires to cram as much logic into as little space as possible, and to force the player to make even the most basic building blocks for themselves.

I believe that my system strikes a balance between these two considerations. Logic Tiles, which will be described in a later section, only have 6 variants. Just enough to cover the basic logic gates and operations, while not including logic that can be easily made by combining other tiles together.

For instance: AND and NOT gates are included as the Greedy and Spiteful tiles, while NAND gates are not included, since it's reasonable to expect a player to simply combine an AND gate with a NOT gate to get the same behavior. On the other hand, the Scornful tile (which has similar functionality to Minecraft's comparator) is included, as it is not reasonable to expect a player to build the logic to compare and subtract signal strengths on their own.

It is with these considerations that I hope to allow new and inexperienced players to learn the logic system intuitively through trial and error without being overwhelmed by an abundance of choices, while also allowing advanced players the tools necessary to build extremely complex systems if they wish to do so.

Every design decision has been made with the balance between inherent simplicity and emergent complexity in mind.
### Don't Break Immersion

In my opinion, breaking immersion is one of the cardinal sins of game development. Whatever expectations a game sets for the player's suspension of disbelief must be held to, or the game risks pulling the player out of their fantasy.

To this end, UI elements should be kept to a minimum, and technical language must be avoided wherever possible. Elements of the game must make effort to separate themselves from real-world technical systems, and instead try their best to fit the tone, theme, and level of immersion established by other aspects of the game.

The main way this manifests in Arcane Circuits is the choice to rename logic gates to more evocative terms. Examples include the four basic logic gates:

- AND gates are called "Greedy Tiles"
- OR gates are called "Reckless Tiles"
- XOR gates are called "Jealous Tiles"
- NOT gates are called "Spiteful Tiles"

Every design decision has been made with immersion in mind.
# Gameplay Implementations

This section outlines how the gameplay actually works, and why each design decision was made.
### The Arcane Brush

The Arcane Brush is a tool which players can use to configure their circuits. Most components in Arcane Circuits can be modified by interacting with them while holding the Arcane brush.

This tool exists to give players finer control of how their logic circuits operate, and enable more compact systems than would otherwise be possible if components chose which neighbors to connect to through context.
### Compound Blocks

This section has several paragraphs that mention multiple components being placeable in the same block. This is mainly so players can make circuits that climb walls, and so they can make their circuits smaller and more hidden than would otherwise be possible.

As of now I'm not exactly sure how to accomplish this, though I've seen mods for Hytale that have accomplished something like this already so I know it's possible. My best guess is that Arcane Circuits components could be part of "compound blocks", which would combine different models in the same space and keep track of all the inputs and outputs that are possible.

At the moment this is just something I'm planning on figuring out later when it comes time to actually make this system in-game.
### Arcane Ink

Arcane Ink serves as the main crafting ingredient for Arcane Circuits components, and the primary medium for signal propagation.

Arcane Ink is crafted by combining crystal shards and tree sap. The ink can come in as many colors as there are variants of crystal shards to afford cosmetic flexibility, as well as allowing players to color-code their circuits if they desire.

There are no special rules regarding how inks of different colors interact with each other; Arcane Ink is Arcane Ink. This is to prevent players from having to go hunting for a specific color of crystal to make their creations work, while also requiring extra effort if they want to make their creations look nice.

Arcane Ink can be placed on any side of a block, as long as the face it's being placed on is full and solid. Multiple lines of ink can be placed in the same block on different faces and are able to connect to each other if they are on adjacent faces.

When placed Arcane Ink starts off wet, which is indicated by the texture. While wet the player will be able to retrieve the ink and modify its connections with adjacent components with the Arcane Brush. After a period of time the ink dries, which will mean that its connections can no longer be modified and breaking it will drop nothing.

Arcane Ink's drying behavior is meant to encourage prototyping in survival while also serving to make Arcane Ink a resource sink. This has the added benefit of making subtle or accidental sabotage of a permanent system more difficult.
### Logic Tiles

Logic Tiles are where player ingenuity and creativity will shine the most. There are 6 tiles currently involved in this system, with names chosen to be memorable and reflect their function. This has the added benefit of helping keep immersion intact and not scaring off less technical players with names associated with real-world logic. These tiles are:

- Greedy Tiles (which act like AND gates)
- Reckless Tiles (which act like OR gates)
- Jealous Tiles (which act like XOR gates)
- Spiteful Tiles (which act like NOT gates)
- Stubborn Tiles (which are similar to repeaters)
- Scornful Tiles (which are similar to comparators)

Logic Tiles are crafted with Arcane Ink, crystal shards, stone, and whatever other materials might be deemed appropriate during development. To keep things simple, Logic Tiles will not come in different color variants, and might instead have a pearlescent quality to them, to indicate different colors being mixed.

Like Arcane Ink, players can configure Logic Tiles with the Arcane Brush, which includes configuring its connections to adjacent components and any adjustable or toggleable behavior that's appropriate for its function. For example:

- A player might want an AND gate that only needs two inputs, so they configure a Greedy Tile so that one of its inputs isn't considered for logic.
- A player might want a longer delay, so they add a Stubborn Tile to their circuit and use the Brush to extend the delay.
- A player might want to subtract signal strengths instead of just comparing them, so they use the Arcane Brush to change a Scornful Tile from compare mode to subtract mode.

Unlike Arcane Ink, Logic Tiles do not dry out and can be picked up and interacted with via an Arcane Brush at any time in the future. Logic tiles are a larger investment of materials, and we want to respect the time of players.

Like Arcane Ink, tiles can be placed on any side of a block, as long as the face it's being placed on is full and solid. Multiple tiles can be placed in the same block on different faces, and are able to connect to each other and lines of Arcane Ink if they are on adjacent faces.

To ensure that players can point Arcane Tiles in whatever direction they want, they can be rotated around the axis perpendicular to the surface they are being placed on with the R key, using the same rotation feature as other rotatable blocks in the game.
#####  *- A Note Regarding Scornful and Stubborn Tiles -*
The way Arcane Circuits' Scornful and Stubborn tiles interact is more complex than Minecraft's Comparators and Repeaters. This complexity is built in a way that should not affect the behavior of simple circuits that beginner players might build, but can be taken advantage of by more experienced players to make more advanced logic systems.

Scornful Tiles will always search for the source of their input signal from any given side, and if that source is a Stubborn tile, it will then search for the source of *that* tile's input signal, and it will continue to do so until it has found a source of signal that is not a Stubborn Tile.

In this way, a Stubborn Tile can "repeat" the strength of a specific signal in situations where sending a specific value long distances matters to the player, without degrading the tile's performance for other applications.

If player testing reveals that this somehow affects how simpler circuits operate in a non-intuitive way, Stubborn Tiles can have an Arcane Brush configuration added to enable or disable this behavior, or a new type of tile can be developed to fill this niche.
### Signal Propagation

Like with Minecraft's redstone, the strength of any given logic signal degrades by 1 unit for every unit of Arcane Ink it passes through. However, as Hytale's chunks are 32 blocks wide, signal strengths can go all the way from 0 to 31 (for a total of 32 distinct signal strengths).

This is following what I've interpreted as a clever piece of game design from Minecraft, in which signals can only go about as far as about a chunk without being manipulated in some way. My assumption is that this gently encourages players to build circuits that are more likely to fit inside of loaded areas without breaking immersion.

Note that signal decay is calculated per component traversed, not per block. Signal traveling along multiple components on adjacent faces in the same block will decay like one would intuitively expect.

Unlike redstone signals, Arcane Circuits' signals cannot pass through blocks not explicitly intended to do so. This is intended to ensure that every arcane circuit makes visual intuitive sense, to prevent scaring off novice players with hidden or unintuitive complexity.

A good way to test whether this principle is being held to is to imagine that every non-Arcane-Circuits block in the world has turned invisible. If this causes the circuit to no longer function or make sense, then there's a problem.
##### *- A Note About Power Sources -*
In Arcane Circuits, logic tiles cannot power themselves. If the only signal a logic tile is receiving is coming from a path that includes itself, they will not output any signal. This is to prevent unintuitive behavior where logic circuits are able to do things without any inputs whatsoever.

Unlike Redstone, Arcane Circuits can't do "pulse and coast" style clocks (where an orphan signal goes in circles forever), and players must instead use logic operations to accomplish their goals. If a circuit doesn't have any levers, dials, observers, or any other original source of power, it will go dead.
##### *- A Note About Conduits -*
Since signals can't propagate through regular blocks, we need to give players an explicit way to pass a signal through a wall without having to leave an ugly hole with exposed circuitry visible. My solution to this problem is the Conduit, an Arcane Circuits component that can be disguised as any other block in the game while still being able to carry a signal.

Conduits act similarly to regular hard-powerable blocks in Minecraft, meaning every side can act as both a signal input and a signal output. It can only take inputs directly from other outputs, not from transmission lines, but it can output to anything that can transmit or accept a signal. Undisguised conduits look like a mesh or a cage with a pearlescent dot in the center of each face (the same color as parts of Logic Tiles).

To disguise a conduit, a player can interact with it while holding a building block, which consumes the block and causes the conduit to look like that block. At this point, the only indicator that the block is a conduit is the dot in the center of each face, which can then be covered by a lever, button, pressure plate etc.

To input a signal or take an output signal from a conduit, Arcane Ink must be placed on the face you're trying to get the signal from. If there is no Ink visually connected to the dot in the center of the face, then power will not propagate.
### Inputs and Outputs

Like most existing logic systems, players must be able to interact with Arcane Circuits. I've come up with a few basic inputs and outputs that might be included in the game alongside this system:

- Levers, Buttons, and Pressure Plates, which act how you might expect.
- Dials, which players can turn to input a specific signal strength.
- Observers, which can be configured with an Arcane Brush to either read how full a container is, or to detect block updates.
- Dimmable Lights, which change their brightness depending on the strength of the input signal they're receiving. They can use the signal propagation feature of Stubborn tiles in the same way Scornful Tiles are able to.
- Color changing lights, which change their color based on the three inputs of a Scornful tile (if it's getting power from one), configurable to interpret the inputs as RGB or HSV (depending on if the Scornful tile is in subtract or compare mode). If it is not getting power from a Scornful tile they will act like a regular dimmable lights.

It might be favorable to require Essence of Life to be used as part of the crafting recipes for inputs, to explain where the power is coming from, though I'm not entirely sold on the idea.

I would like to note that the use case I've given for Scornful tiles in the example above is outside the intuitive scope of that tile, and this function might be better served by a new component. For now, however, giving the Scornful Tile a third use case can be part of the initial implementation, and we can decide how to proceed based on user testing.

If Hytale decides to implement physics-based power propagation systems (like Create or Hynergy), Arcane Circuits will also be able to interact with those by reading from and changing the behavior of those systems' components when applicable.
# Technical Philosophy

When a player is trying to use a logic system to accomplish a task, their design decisions should have simple, direct, and consistent consequences. There should *never* be a case where understanding a circuit requires knowing the specifics of how the game's code handles the relationships between components.

The most advanced technical players in any game are always trying to pry apart game systems looking for edge cases, quirks, and unintended behaviors they can use to make their creations better. The moment an easily-abusable consequence of implementation presents itself, they explore that consequence as deeply as they can and spread what they learn to others.

Luckily, voxel logic systems are not new. Redstone has been around for over 15 years, and I've spent several of those years as a technical player thinking about where its problems come from. The system has two major implementation mistakes which have led to strange, unintuitive designs from advanced players, which in turn have discouraged new players from ever engaging with redstone in the first place:

1. **It is possible for logic circuits to be partially loaded if they are built across chunk borders.**
	- This causes stateful circuits and interactive systems to break, since the unloaded half freezes while the loaded half continues to operate.
	- When the unloaded part is loaded again, it picks up where it left off, which means it can now be out of sync with the rest of the circuit.

2. **Update order (meaning the order that blocks are checked during the phase between ticks) affects how logic circuits resolve in several different ways. For example:**
	- Repeaters update before comparators, which technical players exploit to generate sub-tick pulses. These are then used to influence other components in non-intuitive ways.
	- Redstone dust depowers in an order determined by block coordinates, which can interact with quasi-connectivity to cause different block states in pistons and droppers depending on the circuit's coordinates.
	- Minecraft's hash-based update scheduling resolves ties based on position, meaning a logic circuit with race conditions will act deterministically, but only when built at the exact same coordinates every time.
	- Blocks (such as pistons) and tile entities (such as repeaters) each schedule their updates through different systems. Technical players build designs that depend on knowing which type updates first.

Arcane Circuits aims to avoid these types of issues, with the primary goal of maintaining intuitive behavior and deterministic operation with no influence from arbitrary hierarchies of different components, world coordinates, or the current state of loaded chunks.
# Technical Implementations

Like with all projects, Arcane Circuits' plans for gameplay meets its technical philosophy in implementation, and how robust it ends up being depends entirely on how well these two meet. This section will show how I plan to actually implement the logic system, with each item describing how a different aspect works and how the specifics uphold Arcane Circuits' design philosophy.
### Phased Processing (Overview)

The core of Arcane Circuits is the centralized system used for processing the logic. Being centralized accomplishes two things: it allows us to split processing into several phases, and it allows us to avoid the types of update-order dependencies that plague the Minecraft community's more advanced redstone creations.

The general phases that happen each tick are listed below. How the specifics work will be explained in depth in the following subsections.

1. Find all components tagged for the logic check or logic update phases. Filter out any that are marked "frozen". Frozen components should not be processed this tick. This is called the "Filter phase".
2. All components tagged for a logic update because of placed or broken blocks should flood-search along transmission lines (ink and conduits) and tag logic components (tiles and power inputs) for logic updates. This is called the "Logic Checks phase".
3. All components tagged for signal propagation should propagate their changed output signals along transmission lines, tagging affected components for logic updates along the way. This is called the "Signal Propagation phase".
4. All components tagged during the previous phase should update their state based on their new inputs. This is called the "Logic Updates phase".
### Checks and Propagation

Most checks and signal propagation in Arcane Circuits uses flood searches and flood fills.
##### *- Logic Checks -*
Any time an Arcane Circuits component is placed or destroyed, adjacent AC components are flagged for logic checks. During the logic checks phase, all components flagged for logic checks this tick are included in the logic check flood fill (which travels along transmission lines for 32 steps).

This fill does the following:
1. Sets all affected transmission lines to 0 signal strength
2. Tags any logic signal outputs it finds for signal propagation
3. Tags any logic signal inputs it finds for logic updates
##### *- Signal Propagation -*
Any signal sources or logic tiles marked for signal propagation are included as starting points in the signal propagation flood fill (which travels along transmission lines for 32 steps). This includes both components touched by a logic check and components which had their state changed in a previous tick.

This fill does the following:
1. Updates the signal strength of transmission lines. For each step the fill progresses, the signal strength written into the line will decrease by 1. Once the fill strength reaches 0, it will continue overwriting transmission lines until it has reached the maximum distance for the fill.
2. Writes a list of all previous power sources in the path the signal took to get where it is. This includes both hard sources (buttons, levers, etc) and soft sources (logic tiles, conduits, etc). This will be used to help with tracking loopback signals to prevent logic tiles from being able to keep themselves powered with no external power source.
3. Watches for a signal strength higher than the old strength of the previous step. If the fill encounters this, it will stop (except when loopbacks are involved, which are a special case and will be explained later), and if necessary the fill will start propagating the valid signal it encountered backwards along the line until it reaches 0.
4. If two propagation fronts encounter each other, the stronger signal wins. If they are both the same strength, a random winner will be chosen as it does not affect circuit behavior (except when loopbacks are involved, which are a special case and will be explained later)
##### *- Loopback Tracking -*
To prevent logic tiles from powering themselves with orphan signals, we must track when a loopback signal is happening. Loopback signals can be considered in logic operations, but a logic tile cannot send an output if none of its inputs are non-loopback signals.

While signal propagation is happening, the propagation fill also writes the signal path to each component. The signal path is a list of component IDs that the current strongest signal went through to get to the current component, traced back to the first hard source of signal (being levers, buttons, dials, etc).

If the fill encounters an active signal that comes from earlier in the current signal's history (so, if it's the same but shorter), it will continue propagation. However, instead of writing to the main signal strength and signal history, it will begin writing to each component's *separate* loopback signal strength and signal history entries, which are stored in addition to the main strength and history.

Like with regular strength and history, each component only tracks the strongest current source of loopback signal, since it's all that's necessary to accomplish what we're aiming for. 

For behavior and logic, Arcane Ink renders its strength and affects logic inputs based on whichever signal is stronger (original or loopback).
##### *- Logic Updates -*
Any component that was tagged for a logic update checks the inputs signals from each direction it can take an input from. As long as there is at least one non-loopback signal present, the component will work.

Components can use either a hard signal or a loopback signal from each input, whichever is stronger. Depending on what the component is programmed to do, it will update its output or behavior based on its new inputs.

Scornful tiles have special behavior. They will follow the path of each of their inputs, following increasing signal strengths, until it finds the next signal source. If that source is a stubborn tile, it will ignore the immediate received signal strength and instead follow the input to that stubborn tile.

It will continue searching like this until it finds a signal source that is not coming from a stubborn tile, at which point it will use the received signal strength of the last stubborn tile its search passed through.

The reason for this behavior is to enable players to send specific signal strengths long distances with little hassle. Most simpler use-cases for scornful tiles are unlikely to be affected by this feature, and this feature will be included in the item description for scornful tiles to help avoid any confusion it might cause anyway.
### Chunk Lifecycle Hooks

In Minecraft, certain advanced redstone creations are vulnerable to being partially loaded, meaning if a player walks away from a circuit that wasn't carefully aligned with chunk boundaries, it can break and need to be reset.

Since we don't want players to need immersion-breaking insights to make their creations work, Arcane Circuits aims to prevent situations like this by freezing logic components that are likely to be part of partially-loaded circuits. To do this, we can take advantage of Hytale's lifecycle hooks for chunk loading and unloading.

When a chunk is loaded or unloaded, it will trigger a boundary check. In the case of a chunk loading, it affects the chunk that was loaded. In the case of an unloaded chunk, it affects neighboring loaded chunks.

A boundary check does the following:
- Filters for Arcane Circuits components, specifically on the border with unloaded chunks (if such a filtering is possible)
- Does a flood fill starting from any Arcane Circuits components found on the border along all adjacent Arcane Circuits components.
	- Any components touched by the flood fill are marked as "frozen", and will not have their logic progressed this tick.
	- Any components not touched by the flood fill will trigger another flood fill, removing the "frozen" tag and checking if the circuit they are a part of touches any boundaries with unloaded chunks outside the chunk being checked. This fill will also flag unfrozen components for logic checks.
	- If the second flood search finds components touching unloaded chunks, another flood fill will be performed to freeze components.
- Since the secondary flood search in the previous step unfroze components, there is no need to make an additional check for circuits that should be unfrozen

By freezing components in this way, we can prevent circuits from ever having their logic progressed while not fully loaded. Since players will expect their machines to stop running while they're gone anyway, they won't notice this is happening.

While the most advanced players are likely to learn this mechanic and take advantage of it, it is not something the vast majority of players will ever have to know about to make their creations work, or to follow a tutorial.
### Phased Processing In-Depth

In total, this is what the Arcane Circuits system does each tick:

- **PHASE 1: BOUNDARY CHECKS**
	- Loaded/Unloaded chunks trigger boundary checks, which look for Arcane Circuits components that are touching a boundary with an unloaded chunk.
	- Components found on such boundaries are used as the starting points for a flood fill that marks any components it finds as "frozen".
	- Components in the chunk being checked that are not part of this fill have their own boundary check performed, which clears the "frozen" tag, flags them for logic checks, and looks for components bordering with unloaded chunks.
	- Any components found bordering unloaded chunks in this second check will start a second flood fill marking components as "frozen".

- **PHASE 2: LOGIC CHECKS**
	- Any time an Arcane Circuits component is placed or destroyed, it flags adjacent components for the logic check.
	- A logic check fill travels along transmission lines and does the following:
		- Sets all affected transmission lines to 0 signal strength
		- Tags any logic signal outputs it finds for signal propagation
		- Tags any logic signal inputs it finds for logic updates

- **PHASE 3: SIGNAL PROPAGATION**
	- All logic components marked for signal propagation are filtered for and included in the signal propagation flood fill.
	- The signal propagation flood fill travels along transmission lines for 32 steps and does the following:
		- Updates the signal strength of transmission lines, decreasing the strength by 1 for each step taken.
		- Once signal strength reaches 0, the fill continues by setting affected lines to 0 until the fill has traveled its max distance, there are no more lines affected, or the next step's old signal strength is higher than the previous step's old signal strength.
			- In the last case, the fill assumes there is a valid active output further down the line. It re-propagates the valid signal it found back along the line until its strength reaches 0
		- Propagates the list of all previous power sources in the path the signal took to get where it is from the original hard-power source.
		- Tags signal inputs for logic updates.
		- Stronger signals overwrite weaker ones.
	- If the signal propagation flood fill encounters a signal coming from another valid active output, and that signal's path history is a prefix match for the current signal's path history, a loopback signal is propagated. In addition to acting mostly like regular propagation, loopback propagation does the following:
		- Updates the loopback signal strength of transmission lines, which is stored separately from the regular signal strength
		- Updates the loopback path history of transmission lines, which is stored separately from the regular path history
		- A loopback signal propagates in parallel with regular signals. These cannot overwrite each other. A transmission line's behavior and visual strength will use whichever signal is stronger.

- **PHASE 4: LOGIC UPDATES**
	- All logic components marked for logic updates check their input signals.
		- If there are no non-loopback signals present, the logic component's output will be set to 0. This way, logic components cannot keep themselves powered without external sources.
		- Logic components will use either an original signal or a loopback signal from each input, whichever is stronger.
		- These components will update their output or behavior based on their inputs (depending on how the component is supposed to react)
	- Scornful tiles have special behavior:
		- When a scornful tile does a logic update, it follows the path of each of their used input signals (meaning, either loopback or original, whichever is stronger) looking for the source of the signal.
		- If the signal is a stubborn tile, it continues its search by looking for the source of *that* signal. This search continues until it finds a source that is not a stubborn tile.
		- Once it finds a source other than a stubborn tile, the scornful tile will use the signal strength received by the last stubborn tile its search passed through.
		- This means that players can transmit specific signal strengths long distances without having to do anything too complicated. This feature also should not affect simpler use-cases of scornful tiles, which likely won't involve receiving signal from stubborn tiles.
		- A short description of this behavior will be included in the inventory description of Scornful tiles to avoid any unwanted confusion.
### Performance Considerations

While I have made an effort to avoid unnecessary operations, and to use cheaper operations where possible, further optimizations can likely be done by someone more experienced. Further stress testing will have to be done when implementing this system.