Pasadena Coup
------------

THANKS TO THE ORIGIONAL VERSION OF THIS GAME BY Christopher Brown and Jackie Niebling!!
---------------------------------------------------------------------------------------

A clone of the card game Coup written in Node.js.

To run it, checkout the source code, install Node.js (version 6 or later), and run:

    npm install 
    node server.js [--debug]

Then open `http://localhost:3271` in one or more browser windows. For an explanation of how to play, see `web/rules.html`.


AI
--

To play against the AI, click the "Add AI" button before starting the game. The AI implementation is in `ai-player.js`. The `onStateChange` method responds to the current game state and sends commands to the server to play its turn.

Game messages
-------------

In state `waiting-for-players`, the game has not yet started.

In state `start-of-turn`, the player whose turn it is may play an action using the command `play-action`.

In state `action-response`, any player may challenge the action (held in the `action` field) using the `challenge` command. Actions which can be blocked by another player can be blocked using the `block` command. Players who do not wish to challenge or block should send the `allow` command.

After an action has been unsuccessfully challenged, the target of the action may have a final chance to block. This is represented by state `final-action-response`. The action can be blocked using the `block` command, or if the targeted player does not wish to block, they should send the `allow` command.

In state `block-response`, the player in the `target` field is attempting to block the action using the role in the `blockingRole` field. Any player may challenge the block using the `challenge` command. Players who do not which to challenge should send the `allow` command.

In state `reveal-influence`, the player in the `playerToReveal` field must reveal an influence using the `reveal` command. This occurs after a coup, a successful assassination, or a challenge.

The `exchange` state occurs after playing the exchange action; the player whose turn it is must choose which of their cards to exchange. The player chooses their roles from those in the `exchangeOptions` field, and then sends an `exchange` command with the `roles` field containing their choice in the form of an array.

When a player wins, the game returns to `waiting-for-players`, and the `state.winnerIdx` field is set to the winner.

Commands sent to the server look like this:

    {
        command: "",        // The name of the command: play-action, block, challenge, allow, reveal, exchange
        action: "",         // For the play-action command, the action to play
        target: 0,          // When playing an action which targets another player, the index of the player to target
        blockingRole: "",   // For the block command, the role to block with
        role: "",           // For the reveal command, the role to reveal
        roles: [""],        // For the exchange command, the role(s) you wish to keep
        stateId: 1          // Must match the stateId from the latest game state
    }
