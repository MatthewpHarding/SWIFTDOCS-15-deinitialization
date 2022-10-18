![Swift](readme-images/swift-logo.png)

Swift v5.7 | [Swift versions](find-my-swift-version.md) | [Swift.org](https://docs.swift.org).

Taken from the [official Swift documentation](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html).

![Xcode Playground](readme-images/xcode-icon.png)
![Swift Playground Icon](readme-images/playground-file.png)

👉 You can [view this document in Xcode](https://github.com/MatthewpHarding/SWIFTDOCS-15-deinitialization/archive/refs/heads/main.zip) to run and edit each example.
## Run This File In Xcode

**Step 1:** Clone this repo or [download the files](https://github.com/MatthewpHarding/SWIFTDOCS-15-deinitialization/archive/refs/heads/main.zip).

**Step 2:** In Xcode, ensure you have selected **Editor/Show Rendered Markup** to view the formatted instructions.

**Step 3:** You can edit the code within Xcode!  🎉

🤩 *..let's live a better life, by learning Swift* 🛠

```Swift
let myLife = [learning, coding, happiness] 
```
### 🧕🏻👨🏿‍💼👩🏼‍💼👩🏻‍💻👨🏼‍💼🧛🏻‍♀️👩🏼‍💻💁🏽‍♂️🕵🏻‍♂️🧝🏼‍♀️🦹🏼‍♀🧕🏾🧟‍♂️
-----------
# *Page 15* → Deinitialization

A deinitializer is called immediately before a class instance is deallocated. You write deinitializers with the deinit keyword, similar to how initializers are written with the init keyword. Deinitializers are only available on class types.

## How Deinitialization Works

Swift automatically deallocates your instances when they’re no longer needed, to free up resources. Swift handles the memory management of instances through automatic reference counting (ARC), as described in Automatic Reference Counting. Typically you don’t need to perform manual cleanup when your instances are deallocated. However, when you are working with your own resources, you might need to perform some additional cleanup yourself. For example, if you create a custom class to open a file and write some data to it, you might need to close the file before the class instance is deallocated.

Class definitions can have at most one deinitializer per class. The deinitializer doesn’t take any parameters and is written without parentheses:

```Swift
class TestingDeinitialization {
    deinit {
        // perform the deinitialization
    }
}
```
Deinitializers are called automatically, just before instance deallocation takes place. You aren’t allowed to call a deinitializer yourself. Superclass deinitializers are inherited by their subclasses, and the superclass deinitializer is called automatically at the end of a subclass deinitializer implementation. Superclass deinitializers are always called, even if a subclass doesn’t provide its own deinitializer.

Because an instance isn’t deallocated until after its deinitializer is called, a deinitializer can access all properties of the instance it’s called on and can modify its behavior based on those properties (such as looking up the name of a file that needs to be closed).

## Deinitializers in Action

Here’s an example of a deinitializer in action. This example defines two new types, Bank and Player, for a simple game. The Bank class manages a made-up currency, which can never have more than 10,000 coins in circulation. There can only ever be one Bank in the game, and so the Bank is implemented as a class with type properties and methods to store and manage its current state:

```Swift
class Bank {
    static var coinsInBank = 10_000
    static func distribute(coins numberOfCoinsRequested: Int) -> Int {
        let numberOfCoinsToVend = min(numberOfCoinsRequested, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receive(coins: Int) {
        coinsInBank += coins
    }
}
```
Bank keeps track of the current number of coins it holds with its coinsInBank property. It also offers two methods—distribute(coins:) and receive(coins:)—to handle the distribution and collection of coins.

The distribute(coins:) method checks that there are enough coins in the bank before distributing them. If there aren’t enough coins, Bank returns a smaller number than the number that was requested (and returns zero if no coins are left in the bank). It returns an integer value to indicate the actual number of coins that were provided.

The receive(coins:) method simply adds the received number of coins back into the bank’s coin store.

The Player class describes a player in the game. Each player has a certain number of coins stored in their purse at any time. This is represented by the player’s coinsInPurse property:

```Swift
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.distribute(coins: coins)
    }
    func win(coins: Int) {
        coinsInPurse += Bank.distribute(coins: coins)
    }
    deinit {
        Bank.receive(coins: coinsInPurse)
    }
}
```
Each Player instance is initialized with a starting allowance of a specified number of coins from the bank during initialization, although a Player instance may receive fewer than that number if not enough coins are available.

The Player class defines a win(coins:) method, which retrieves a certain number of coins from the bank and adds them to the player’s purse. The Player class also implements a deinitializer, which is called just before a Player instance is deallocated. Here, the deinitializer simply returns all of the player’s coins to the bank:

```Swift
var playerOne: Player? = Player(coins: 100)
print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// Prints "A new player has joined the game with 100 coins"
print("There are now \(Bank.coinsInBank) coins left in the bank")
// Prints "There are now 9900 coins left in the bank"
```
A new Player instance is created, with a request for 100 coins if they’re available. This Player instance is stored in an optional Player variable called playerOne. An optional variable is used here, because players can leave the game at any point. The optional lets you track whether there’s currently a player in the game.

Because playerOne is an optional, it’s qualified with an exclamation point (!) when its coinsInPurse property is accessed to print its default number of coins, and whenever its win(coins:) method is called:

```Swift
playerOne!.win(coins: 2_000)
print("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// Prints "PlayerOne won 2000 coins & now has 2100 coins"
print("The bank now only has \(Bank.coinsInBank) coins left")
// Prints "The bank now only has 7900 coins left"
```
Here, the player has won 2,000 coins. The player’s purse now contains 2,100 coins, and the bank has only 7,900 coins left.

```Swift
playerOne = nil
print("PlayerOne has left the game")
// Prints "PlayerOne has left the game"
print("The bank now has \(Bank.coinsInBank) coins")
// Prints "The bank now has 10000 coins"
```
The player has now left the game. This is indicated by setting the optional playerOne variable to nil, meaning “no Player instance.” At the point that this happens, the playerOne variable’s reference to the Player instance is broken. No other properties or variables are still referring to the Player instance, and so it’s deallocated in order to free up its memory. Just before this happens, its deinitializer is called automatically, and its coins are returned to the bank.

## The End
Taken from **official Swift** documentation found [here ](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html).

- 🔍 We made the **official Swift documentation** [searchable](https://github.com/MatthewpHarding?tab=repositories). 

- 🕊 All official documentation begins with `SWIFTDOCS`. [Try it](https://github.com/MatthewpHarding?tab=repositories&q=SWIFTDOCS).

- 🕊 All simplified documentation begins with `SIMPLDOCS`. [Try it](https://github.com/MatthewpHarding?tab=repositories&q=SIMPLDOCS).

- Try our free mini-course:
👉 [A Guided Tour of Swift](https://github.com/MatthewpHarding/a-tour-of-swift) 

# 🤷🏼‍♂️



@[MatthewpHarding](https://github.com/MatthewpHarding) 🔗


🤩 *..let's live a better life, by learning Swift* 🛠

```Swift
let myLife = [learning, coding, happiness] 
```
### 🧕🏻👨🏿‍💼👩🏼‍💼👩🏻‍💻👨🏼‍💼🧛🏻‍♀️👩🏼‍💻💁🏽‍♂️🕵🏻‍♂️🧝🏼‍♀️🦹🏼‍♀🧕🏾🧟‍♂️
