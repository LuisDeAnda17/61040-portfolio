# Problem Set 1

## Exercise 1: Reading a Concept

1.  One invariant is that the difference in count number in a set of Purchases from the count number of a Request related to the same number remains greater than or equal to zero (requests.count - purchases.count >= 0). The other invariant is that every purchase must correspond to a request. Despite both being crucial for the management of the concept, the invariant about the aggregation of counts is more important because more items can be bought than was requested which could lead to other problems outside the scope of the concept. The purchase action is most affected by this concept invariant and preserves it by making sure that no more of the object is purchased than the count that remains.
2.  RemoveItem could break this invariant due to the fact that another user may have made a purchase for the item that then gets removed. If the item is removed, its count essentially becomes zero in requests. However, if someone already bought a certain quantity of the item, then we're left with a negative aggregation, breaking the invariant. This action can be fixed by requiring the request of the item to have no purchases attached to it before removing it. Otherwise, it should just decrement the original request count such that the sum of both counts becomes zero to prevent further purchases.
3.  Based on the actions **open** and **close**, a registry can be repeatedly opened and closed. This allows users to inform other users that they have no desire to recieve purchases at this time and if reopened, allow for the opposite to be true. All of this allows for users to avoid having to recreate a registry if they want to use the concept again.
4.  Not necessarily. Concept wise, Not having a deleteRegistry action makes no difference since the removeItem action would allow a user to empty their registry as if making a new one. Implementation wise, it may allow for the reuse of memory space, but that's not a consideration for the concept stage.

5.  A query made by the owner would be what items have been purchased from their registry, and another query made by the gift giver would be what items can still be purchased from the registry.

6.  The state for a registry would contain a flag to decide whether or not the one who made a purchase is revealed to the registry's owner, revealBuyer Flag.

7.  It is easier to visualize the concept by using the Item type rather than using the parts that define the Item type.

## Exercise 2: Extending a Camiliar Concept

1.  **state**
    a set of Users with
    - a username String
    - a password String
2.  **actions**
    - register (username: String, password: String): (user: User)
      - requires: A username and password of which neither has been taken before
      - effects: A new User is created with the same username and password
    - authenticate (username: String, password: String): (user: User)
      - requires: the username and password both correspond to the same, existing User
      - effects: The User is allowed to enter
3. Every User has to have a unique username is the invariant. It is preserved with our current actions: register will enforce the precondition and authenticate does not modify it.
4.  New/Modified actions:
  - register (username: String, password: String): (user: User, secretToken: Token)
      - requires: A username and password of which neither has been taken before
      - effects: A new User is created with the same username and password
  - register (username: String, password: String): (user: User)
      - requires: A username and password of which neither has been taken before
      - effects: A new User is created with the same username and password
  I also added onto to the state to handle the new Token. I believe it can be added because its more of a small feature for this concept than an independent concept.
    a set of Tokens with
    - a user User
    - a confirmation Flag
    - a expiration date Date

  The user and confirmation aspects are to help in the email state who is logging in and confirm the log in. The expiration date is to prevent access to be extended past the first log in.


## Exercise 3: Comparing Concepts
- Concept: PersonalAccessToken
- Purpose: An alternative to a password to authenticate for GitHub
- Principle: 
        A User can generate a token under their account. The user copies and saves the token, allowing for authentication on a GitHub API or command line. 
- State: 
  - a set of Tokens with
    - a tokenString String
    - an owner User
    - an active Flag
    - an expirationDate Date
  - a set of Users with
    - a username String
    - a set of Tokens 
- Actions:
   - createToken(owner: User, expirationDate Date):(token: Token)
      - requires: an existing owner User
      - effects: creates a token Token with a unique tokenString, with the given User and expiration date.
   - deleteToken(token: Token)
      - requires: Requires that the token exists
      - effects: Deletes the token
   - authenticate(tokenString: String, username: String): (user:User)
      - requires: given tokenString matches with valid Token from User
      - effects: The user is allowed to access files from the User

The key differences PersonalAccessToken has from PasswordAuthentication is the ability to have multiple tokens for different Git APIs or terminals and has an auto-generated hard to decipher password instead of one password for all inputs.


## Exercise 4: Defining Familiar Concept


### Conference Room Booking
- concept RoomBooking
  purpose coordinate use of shared conference rooms to avoid conflicts

- principle
    a user selects a room, date, and time interval and requests a booking;
    the booking is allowed only if the room is free and the building is open during that interval. Also, bookings can later be cancelled.

- state
    - a set of Rooms with
      - a roomID String
      - an openingTime Time
      - a closingTime Time
    - a set of Bookings with
      - a room Room
      - a owner User
      - a startTime Time
      - an endTime Time

- actions
    - createRoom (roomID: String, openingTime: Time, closingTime: Time): (room: Room)
      - requires: That the roomID, openingTime, and closingTime be valid names and operating hours for the physical room being refrenced.
      - effects:creates a Room with the roomID, openingTime, and closingTime, valid to be used for bookings.

    - book (owner: User, room: Room, startTime: Time, endTime: Time): (booking: Booking)
      - requires: no existing booking for this room overlaps with between the startTime and endTime. Additionally, the startTime is on or after the openingTime and the endTime is on or before the closingTime of the room
      - effects: create and add a booking for this interval

    - cancel (booking: Booking, owner: User)
      - requires: booking and owner exists, and that the owner is the registered User of the booking
      - effects: remove booking from the set

    - lookupBookings (room: Room, date: Date): (set of Bookings)
      - requires: room exists
      - effects: return all bookings for that room on the given date




### Time-Based One-Time Password
- concept TimeBasedOneTimePassword
  purpose provide a second factor of authentication using time-sensitive codes

- principle
    when trying to log in, the system generates a secret key for the user and shares it with the user. the code is valid only within a short time window. Checks if code that was inputted matches the one given

- state
    - a set of KeyUsers with
      - a user User
      - a secretKey Numbers

- actions
    - enroll (user: User): (secretKey: Numbers)
      - requires: user exists and does not already have a secretKey
      - effects: generate a secret key, assign it to user

    - generateCode (user: KeyUser): (secretKey: Numbers)
      - requires: user exists
      - effects: compute a code made of multiple digits to act as the secretKey

    - authenticate (username: String, code: Numbers): (user: User)
      - requires: username match a user and code matches secretKey of said user
      - effects: return the authenticated user

This implies that the KeyUser is a User of another service and is using this concept as an extension. I excluded all mention of technical cryptography, so the exact process is not done, but I hope the main idea remains.


### URL Shortener
- concept URLShortener
  purpose provide short, easy-to-share links that redirect to longer URLs

- principle
    a user submits a long URL and may optionally request a specific short suffix.
    By default, with not given suffix, the system generates a unique one automatically.
    the system maintains a mapping from suffixes to long URLs.
    when someone accesses a short URL, the system looks up the suffix and redirects to the long URL.

- state
    - a set of Links with
      - a suffix String
      - a targetURL String

- actions
    - createLink (targetURL: String, suffix: String): (link: Link)
      - requires: if suffix is provided then it is not already in use
      - effects: create a new link mapping suffix to targetURL.
        if suffix is not provided, generate a unique one automatically

    - lookup (suffix: String): (targetURL: String)
      - requires: a link with this suffix exists
      - effects: return the targetURL for that suffix



