# Client-Server Communication Formats

This file will specify the standard format for data exchanged and stored between systems of the software. These formats will be used in the following system interactions:

* Clients to Server
* Server to Clients

The format for all data to be exchanged will be that of JSON objects (https://www.json.org/). This means that whenever clients and the server exchange information a JSON object will be sent with the required information. 

This document will be updated throughout the duration of the project as needed. Objects will be removed and added over the course of development.

There will be three types of objects sent between clients and the server. These will be `Update`, `Action`, and `ServerError`. Each of the four objects will have different purposes depending on which properties of the object are filled in or what values are filled in for those properties. 

Clients will send `Action` objects.
The server will send `Update` and `ServerError` objects.

An `Action` object will be sent by a client whenever a player wants to perform an action such as registering, logging in, inviting another player, moving a piece on the board, etc...

An `Update` object will be sent by the server whenever the server is responding to a client's action. These are things like updating the Game board, sending the client their acount information, etc...

A  `ServerError` object will be sent by the server whenever there is an error on the server side. These are things like the player attempted to perform an invalid set of moves, the login information was incorrect, etc...

Here is the structure for each of the four objects:

## Action

```javascript
{
  "objectType": "Action",
  "communicationType": "requestMoves",
  "communicationVersion": 1,
  "matchID": "",
  "playerName": "",
  "pieceID": "Z",
  "desiredMoves": [],
  "userName": "",
  "userPassword": "",
  "userEmail": "",
  "playerOneName": "",
  "playerTwoName": "",
  "invitationFrom": "",
  "invitationTo": "",
  "invitationTime": "",
  "playerQuitting": ""
}
```

## Update

```javascript
{
  "objectType": "Update",
  "communicationType": "updateBoard",
  "communicationVersion": 1,
  "matchID": "",
  "playerName": "",
  "pieceID": "m",
  "updatedBoard": [][],
  "whoseTurn": "",
  "statussMessage": "The player's move was valid and the board has been updated",
  "userName": "",
  "userEmail": "",
  "initialBoard": [][],
  "matchBeginTime": "",
  "invitationFrom": "",
  "invitationTo": "",
  "invitationTime": "",
  "endCondition": ["won", "quit"],
  "winnerName": "",
  "loserName": "",
  "matchEndTime": "",
  "userFound",
  "invitationsFrom",
  "invitationTimes",
  "matchesInProgress": [{"matchID: "", "gameBoard": [][], "opponentName": "", "whoseTurn": "", "matchBeginTime": ""}, {"matchID: "",                             "gameBoard": [][], "opponentName": "", "whoseTurn": "", "matchBeginTime": ""}, ...],
  "matchesCompleted": [{"matchID: "", "opponentName": "", "matchBeginTime": "", "matchWinner": "", "matchEndTime": ""}, {"matchID: "",
                            "opponentName": "", "matchBeginTime": "", "matchWinner": "", "matchEndTime": ""}, ...],
  "sentToNames": [],
  "sentToTimes": [],
  "receivedFromNames": [],
  "receivedFromTimes": []
}
```
## ServerError

```javascript
{
  "objectType":"ServerError",
  "errorCode": 100,
  "errorMessage": "Invalid password"
}
```
`objectType` is the type of communication object being sent (ServerError).
`errorCode` is the code for the type of error that occurred.
`errorMessage` is a message detailing the error that occurred.

List of error codes:

* 100 = Invalid password when attempting a login
* 101 = Username not found when attempting a login
* 102 = Invalid move
* 103 = Player not found in search (in search for who to invite)
* 104 = Player cannot abandon game (due to invalid matchID or player name)
* 105 = Game information cannot be retrieved

List of communication types for `Action`:

* requestMoves
* registerUser
* requestBeginNewMatch
* sendInvitation
* invitationResponse
* quitMatch
* unregisterUser
* attemptLogin
* searchUser
* searchGames
* requestGameLoad
* getUserInvsLists
* rejectInvite

List of communication types for `Update`

* updateBoard
* registrationSuccess
* beginNewMatch
* invitation
* endMatch
* loginSuccess
* searchResults
* searchGamesResults
* loadInvitations
* invitationSentStatus
* sendUserInvsLists
* inviteRejectStatus


List of communication types for `ServerError`:

* errorInvalidMove
* errorInvalidRegistration
* errorInvalidLogin

The following is a list of properties that should be filled in for each communication type.

## requestMoves

This communication type will be used to communicate to the server that a player wishes to move a piece to a certain position on the board. It will ultimately be up to the server to decide whether this move is valid or not. 

```javascript
{
  "communicationType": "requestMoves",
  "communicationVersion": 1,
  "matchID": "",
  "playerName": "",
  "pieceID": "C",
  "desiredMoves": []
}
```

* Player 1's pieces
   * g: Giraffe
   * m: Monkey
   * e: Elephant
   * l: Lion
   * c: Crocodile
   * z: Zebra
   * p: Pawn
* Player 2's pieces
   * G: Giraffe
   * M: Monkey
   * E: Elephant
   * L: Lion
   * C: Crocodile
   * Z: Zebra
   * P: Pawn
     
* `desiredMoves` will be a 1D array with the coordinates of the squares or positions on the board that the player would like to move to for their turn. The [row,column] coordinates of each square will be combined to for a single 2 digit number.  Thus the square [2,3] would be stored in the array as 23.  
The first value in the array must be the current location of the piece.  The next value in the array is the square the player wishes to move to.  So, desiredMoves[23,12] indicates the piece is currently at [2,3] and wishes to move to [1,2].  A monkey can indicate a sequence of jumps as follows:  [01,23,05].

## errorInvalidMove

This communication type will be sent by the server to a client after the server has determined that a requested move from the client is invalid. This will then indicate that the client must pick a different spot to move to.

```javascript
{
  "communicationType": "errorInvalidMove",
  "communicationVersion": 1,
  "matchID": "",
  "playerName": "",
  "pieceID": "e",
  "desiredMoves": [],
  "whoseTurn": "",
  "errorMessage": "The move requested by the player cannot be made."
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `matchID` is the identifier of the match that is object is regarding.
* `playerName` is a string and will be the name of the player who requested the invalid move.
* `pieceID` is a string and will be the ID of the piece that the player wished to move to an invalid space
* `desiredMoves` will be a 1D array with the coordinates of the invalid squares or positions on the board that the player wanted to move the piece to.
* `whoseTurn` is a string and is the name of the player who is allowed to make the next move. Because the previous move was invalid, the player who made the last move is try and move again.
* `errorMessage` is a string and will contain a message stating that the requested was was invalid and could not be made. This string
could also be altered to include the specifics of why the move was invalid such as it being out of bounds, conflicting with the rules
of the Game, etc..

## updateBoard

This communication type will be sent after the server has determined that a requested move by a client was in fact valid and so the move was made and the state of the board was updated. The server will
send the updated board to the clients so that they can display it so the players.

```javascript
{
  "communicationType": "updateBoard",
  "communicationVersion": 1,
  "matchID": "",
  "playerName": "",
  "pieceID": "G",
  "endCondition": "active"|"won"|"quit",
  "updatedBoard": [][],
  "playerOneName": "",
  "playerTwoName": "",
  "whoseTurn": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `matchID` is the identifier of the match that is object is regarding.
* `playerName` is a string and will be the name of the player who made the valid move that updated the board.
* `pieceID` is a string and will be the ID of the piece that the player moved.
* `updatedBoard` is a 2D array with the information containing the new state of the Game board after the piece was moved.
* `playerOneName` is a string containing the login name of the first player
* `playerTwoName` is a string containing the login name of the second player
* `whoseTurn` is a string and is the name of the player who is allowed to make the next move. In this case it will be the name of the player who did not make the most recent move.

## registerUser

This communication type will be sent by a client to the server whenever a user is registering to the site. The server will take the data in this object and use it to create a new entry with the user's informatino in the database.

```javascript
{
  "communicationType": "registerUser",
  "communicationVersion": 1,
  "userName": "",
  "userPassword": "",
  "userEmail": ""
}
```
* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userName` is a string and is the display name that the person creating the account would like to use.
* `userPassword` is a string and is the hash of the password that the person creating the account would like to use to secure their account.
* `userEmail` is a string and is the email address of the person creating the account.

## errorInvalidRegistration

This communication type will be sent by the server to a client when the client submits invalid information when trying to register a new account. It is up to the server to determine what information is invalid. Invalid information could be something like a duplicate user name, invalid email address, etc...

```javascript
{
  "communicationType": "errorInvalidRegistration",
  "communicationVersion": 1,
  "userName": "",
  "userPassword": "",
  "userEmail": "",
  "errorMessage": "Information that was used in the attempted registration is invalid"
}
```
* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userName` is a string and is the display name that the person attempting to create the account would like to use.
* `userPassword` is a string and is the password that the person attempting to create the account would like to use.
* `userEmail` is a string and is the email address of the person attemping to create the account would like to use.

## registrationSuccess

This communication type will be sent by the server to a client when the client submits valid information when trying to register a new account and so a new account has been created for the user. It is up to the server to determine if the information was valid in the account creation.

```javascript
{
  "communicationType": "registrationSuccess",
  "communicationVersion": 1,
  "userName": "",
  "userEmail": "",
  "successMessage": "User account has been successfully created."
}
```
* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userName` is a string and is the new display name of the person who created the account.
* `userEmail` is a string and is the email address of the person who has created a new account.
* `successMessage` is a string with a message stating that an account has been successfully created for the user.

## requestBeginNewMatch

This communication type will be sent to the server by a client whenever a user is creating a new match. This will tell the server to allocate resources for the match and begin the processes required for the clients to play it.

```javascipt
{
  "communicationType": "requestBeginNewMatch",
  "communicationVersion": 1,
  "playerOneName": "",
  "playerTwoName": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `playerOneName` is a string and is the username of the player who created the match by inviting playerTwo. They are the opponent of playerTwo.
* `playerTwoName` is a string and is the username of the player who was invited to the match by playerOne. They are the opponent of playerOne.

## beginNewMatch

This communication type will be sent by the server to the two clients who recently began a new match. It represents the beginning of a new match. 

```javascript
{
  "communicationType": beginNewMatch",
  "communicationVersion": 1,
  "matchID": "",
  "initialBoard": [][],
  "whoseTurn": "",
  "matchBeginTime": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `matchID` is a string and is the identifier of the new match that has just been created. 
* `initialBoard` is a 2D array and is the initial state of the board at the start of a new match.
* `whoseTurn` is a string and is the name of the player who is allowed to make the next move. At the start of the match this will be the player who created the match unless therwise stated in the rules.
* `matchBeginTime` is a string and contains the time that the match was created. 

## sendInvitation

This communication type will both be sent from clients to the server and from the server to the clients. A client who wishes to start a new match with a player will send the the invitation to the server. The server will then pass the invitation on to the addressed client who will then either accept or reject the invitation.
  
```javascript
{
  "communicationType": "sendInvitation",
  "invitationFrom": "",
  "invitationTo": "",
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `invitationFrom` is a string and is the name of the player who is sending the invitation.
* `invitationTo` is a string and is the name of the player who is receiving the invitation.

## invitationSentStatus

```javascript
{
  "communicationType": "invitationSentStatus",
  "invitationSent:" true,
  "statusMessage:" ""
}
```

* `communicationType` is a string and will specify that the purpose of the object is to communicate the status of a sent invitation.
* `invitationSent` will be true if the invitation was successfully sent and false otherwise.
* `statusMessage` is an optional string for conveying the status of the message (used for if the user attempted to send a duplicate invitation)

## loadInvitations

```javascript
{
  "communicationType": "loadInvitations",
  "userName": "name of user",
  "invitationsFrom": "person1,person2,person3",
  "invitationTimes": "time1,time2,time3"
}
```

* `communicationType` is a string and specifies that the purpose of the object is to load invitations
* `userName` is a string and specifies the name of the user whose invitations are being retrieved
* `invitationsFrom` is a string of user names who sent invitations to the user. They are separated by commas.
* `inviationTimes`: is a string and specifies the times that each invitation was sent. They are separated by commas and line up with invitationsFrom.

## invitationResponse

This communication type will be sent from clients to the server. It will be sent to notify the server of a client's response to an invitation to a new match. The server will then pass this object on to the client that sent the original invitation.

```javascript
{
  "communicationType": "invitationResponse",
  "communicationVersion": 1,
  "invitationFrom": "",
  "invitationTo": "",
  "invitationAccepted": true
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `invitationFrom` is a string and is the name of the player who sent the invitation.
* `invitationTo` is a string and is the name of the player who received the invitation.
* `invitationAccepted` is a boolean and indicates whether the player specified in `invitationTo` accepted or declined the invitation. `true` indicates that the client accepted the invitation while `false` indicates the client rejected the invitation.

## quitMatch

This communication type will be sent from clients to the server. It will be sent to notify the server that the client wishes to forfeit the match and leave, thus ending the Game. The client who sends this request will automatically lose the match and the opponent will win. 

```javasctipt
{
  "communicationType": "quitMatch",
  "communicationVersion": 1,
  "matchID": "",
  "playerQuitting": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `matchID` is a string and is the identifier of the match which the client wishes to leave. 
* `playerQuitting` is the username of the player who is quitting the match.

## endMatch

This communication type will be sent from the server to the clients. It will be sent when either a client's last move has won the match or a player has quit the match. It will also notify the clients of the results.

```javasctipt
{
  "communicationType": "endMatch",
  "communicationVersion": 1,
  "matchID": "",
  "endCondition": "won"|"quit",
  "winnerName": "",
  "loserName": "",
  "matchEndTime": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `matchID` is a string and is the identifier of the match that has ended.
* `endCondition` can be one of two string values. `won` indicates that a player has won the match by making a final move which won the Game. `quit` indicates that the Game has ended because one of the players has quit and left the match.
* `winnerName` is a string and is the name of player who has won the match either be making the winning move or remaining in the Game after the other player has quit.
* `loserName` is a string and is the name of the player who has lost the match either by the other player making the winning move or by quitting the match.
* `matchEndTime` is a string and contains the time that the match was ended. 

## unregisterUser

This communication type will be sent by clients when communicating with the server. It will be sent by a client to the server whenever a user would like to unregister from the site. The server will then remove the user's data associated with their account from the database.

```javascript
{
  "communicationType": "unregisterUser",
  "communicationVersion": 1,
  "userName": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userName` is a string and is the name of the user who would like to unregister from the site.

## attemptLogin

This communication type will be sent by clients when communicating with the server. It will be sent by a client to the server whenever a user is attempting to log into their account.

```javascript
{
  "communicationType": "attemptLogin",
  "communicationVersion": 1,
  "userEmail": "",
  "userPassword": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userEmail` is a string and is the email address of the user who is attempting to log into their account. This will be the primary identifier of the account.
* `userPassword` is a string and is the password of the user attempint to log into their account. It is the primary form of authentication for the user.  

## errorInvalidLogin

This communication type will be sent by the server when communicating with clients. It will be sent by the server to the client whenever the client attempts to login to their account with an incorrect password or the account does not exist.

```javascript
{
  "communicationType": "errorInvalidLogin",
  "communicationVersion": 1,
  "userEmail": "",
  "errorMessage": ""
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `userEmail` is a string and is the email address that the user has entered when trying to login. 
* `errorMessage` is a string containing the reason that the login attempt has failed. It could either state that the password was incorrect or that the account does not exist. 

## loginSuccess

This communication type will be sent by the server when communicating with clients. It will be sent by the server when a user has successfully logged in to their account. It will contain the user's account information so that it may be displayed to the user and interacted with.

```javascript
{
  "communicationType": "loginSuccess",
  "communicationVersion": 1,
  "invitations": [{"invitationFrom": "", "invitationTime": ""}, {"invitationFrom": "", "invitationTime": ""}, ...]
  "matchesInProgress": [{"matchID: "", "gameBoard": [][], "opponentName": "", "whoseTurn": "", "matchBeginTime": ""}, {"matchID: "",                             "gameBoard": [][], "opponentName": "", "whoseTurn": "", "matchBeginTime": ""}, ...],
  "matchesCompleted": [{"matchID: "", "opponentName": "", "matchBeginTime": "", "matchWinner": "", "matchEndTime": ""}, {"matchID: "",                          "opponentName": "", "matchBeginTime": "", "matchWinner": "", "matchEndTime": ""}, ...]
}
```

* `communicationType` is a string and will specify what the type of the JSON object is and so what information it should contain.
* `communicationVersion` is an int and will specify the version of this document that the object's structure is based on.
* `invitations` is an array of objects. These objects contain information regarding the open invitations sent to this user.
* `matchesInProgress` is an array of objects. These objects contain information regarding matches that the player is currently playing.
* `matchesCompleted` is an array of objects. These objects contain information regarding past matches that have ended. 

## searchUser

```javascript
{
  "communicationType": "searchUser",
  "userName": "name of user"
}
```

* `communicationType` is a string "searUsers" identifying the action type
* `playerName` is the name of the user who the client is searching for


## searchGames

This communication type will be sent from clients to the server. It will be sent by the client when a user wants to search and display information about their games.

```javascript
{
  "communicationType": "searchGames",
  "communicationVersion": 1,
  "userName": "name of user"
  "playerTwoName": "opponent's name"
}
```

* `communicationType` is a string "searchGames" identifying the action type to search for info on user's games
* `userName` is the name of the user who's games we are searching for
* `playerTwoName` is the optional name of the opponent.  If this string is empty, all of the user's game are returned.  If it contains a value, only games matching the opponent's name are returned.

## searchResult

```javascript
{
  "communicationType": "searchResult",
  "userName": "name of user",
  "userFound": true
}
```
* `communicationType` is a string "searchResults identifying the update type
* `userFound` is a boolean specifying whether or not the user was found in the database

## searchGamesResult

This communication type will be sent from the server to the client in response to a "searchGames" message initiated by the client.

```javascript
{
  "communicationType": "searchGamesResult",
  "communicationVersion": 1,
  "userName": "name of user",
  "searchResults": "array containing one entry with info per each game"
}
```
* `communicationType` is a string "searchResults identifying the update type
* `userName` is a string containing the name of player in all the games
* `searchResults` is an array of strings.  Each string represents a different game and contains 3 comma separated values.  The first is the matchID, the second is the opponent's name and the third is the start date of the game.  Example: ["22,fari,2019-11-30", "23,arictor,2019-11-30", "24,zach,2019-11-30"]

## requestGameLoad

This communication type will be used to communicate to the server that a player wishes to load a specific game.  The server will respond to this by returning an `updateBoard` communication.

```javascript
{
  "communicationType": "requestGameLoad",
  "communicationVersion": 1,
  "matchID": "",
}
```

## getUserInvsLists

```javascript

{
    "communicationType": "getUserInvsLists",
    "userName": "name of user"
}
````

## sendUserInvsLists

```javascript
{
  "communicationType": "sendUserInvsLists",
  "sentToNames": [],
  "sentToTimes": [],
  "receivedFromNames": [],
  "receivedFromTimes": []
}
````

## rejectInvite

```javascript
{
  "communicationType": "rejectInvite",
  "userName": "name of user",
  "invitationFrom": "name of user who sent invitation"
}
```

## inviteRejectStatus
```javascript
{
  "communicationType": "inviteRejectStatus",
  "statusMessage": "invitation rejection completed"
  
}
```
## acceptInvite

```javascript
{
  "communicationType": "acceptInvite",
  "userName": "name of user",
  "invitationFrom": "name of user who sent invitation"
}
````

## inviteAcceptStatus

```javascript
{
  "communicationType": "inviteAcceptStatus",
  "statusMessage": "invitation acceptance completed"
}
```

# Update History
* 9/16/2019 zachklau finished adding first set/version of GameIP objects. 
* 9/22/2019 zachklau renamed GameIP to ClientServerCommFormat.
* 9/22/2019 zachklau consolidated communication types into four objects to be sent and recieved: Action, Update, ClientError, and ServerError
* 9/22/2019 zachklau renamed `desiredPosition` to `desiredMoves` and made it into a 2D array in case a piece moves more than one time in a turn.
* 9/22/2019 zachklau edited the description of `userPassword` to specify that it will be a hash of the password rather than the the password itself
* 9/23/2019 zachklau removed ClientError object as there is no forseen use for it yet.
* 10/13/2019 mlnash2 proposed changes to pieceID and desiredMoves[].
* 10/26/2019 zachklau updated ServerError to remove unnecessary fields and add server error codes.
* 11/1/2019 zachklau added searchResults, searchUser, and added userFound field to Update.
* 11/1/2019 zahklau added searchResults, searchUser, and added userFound field to Update.
* 11/2/2019 zachklau removed invitation and added sendInvitation and loadInvitations
* 12/2/2019 zachklau added getInvsLists and sendUserInvsLists
* 12/3/2019 zachklau added rejectInvite, inviteRejectStatus
* 12/5/2019 zachklau added acceptInvite, inviteAcceptStatus

# Notes
* The intial set of objects is based off the user description of the desired system in P1.pdf. They are meant to represent interactions discussed in this description.
