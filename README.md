# Sword - A Discord library for Swift

As the effort of Sword is gearing towards 1.0, I've decided the push forward the development of the rewrite branch as the main branch.

# Sword - A Discord Library for Swift

[![Swift Version](https://img.shields.io/badge/Swift-4.0-orange.svg?style=flat-square)](https://swift.org) [![Build Status](https://img.shields.io/travis/Azoy/Sword/master.svg?style=flat-square)](https://travis-ci.org/Azoy/Sword) [![Tag](https://img.shields.io/github/tag/Azoy/Sword.svg?style=flat-square&label=release&colorB=)](https://github.com/Azoy/Sword/releases)

## Requirements
1. macOS, Linux, iOS, watchOS, tvOS (no voice for iOS, watchOS, or tvOS)
2. Swift 4.0
3. libsodium (if on macOS or Linux)

## Installing libsodium
### macOS
Installing libsodium is really easy on mac as long as you have [homebrew](https://brew.sh). After that is installed, all you have to do is `brew install libsodium`. That's it!

### Linux
This depends on the version of Ubuntu you are running, so I made a nice table here:

| Ubuntu 14.04 | Ubuntu 16.04 |
|:-----------------------------------------------------------------------------------------------------------------------:|:-----------------------------------------------------------:|
| `sudo -E add-apt-repository -y ppa:chris-lea/libsodium && sudo apt-get update && sudo apt-get install -y libsodium-dev` | `sudo apt-get update && sudo apt-get install -y libsodium-dev` |

It's easier to copy and paste that command right into shell, and follow any on screen instructions if needed so.

## Adding Sword
### Swift Package Manager
In order to add Sword as a dependency, you must first create a Swift executable in a designated folder, like so `swift package init --type executable`. Then in the newly created Package.swift, open it and add Sword as a dependency

```swift
// swift-tools-version: 4.0

import PackageDescription

let package = Package(
    name: "YOUR_BOT_NAME_HERE",
    dependencies: [
        .package(url: "https://github.com/Azoy/Sword", .branch("master"))
    ],
    targets: [
      .target(
        name: "YOUR_BOT_NAME_HERE",
        dependencies: ["Sword"]
      )
    ]
)
```

Before you run the bot, you'll want to take steps to protect your "token" aka your bot's unique key to login to Discord. If this key is leaked, people can cause irreparable damage to any servers the bot has joined. 
To prevent this from happening, we will use a .json file to store our token and tell the bot to access it. 
 
First locate your bot's executable directory. This should be the same as your build directory in a folder like /Debug. If you're not sure where it is, use the commented lines below to find it or ask the dev where this is.

Once inside, create a file called config.json.

Next, write this in the file:```json
{
    "token": "YOUR_BOT'S_UNIQUE_TOKEN_HERE",
    "cmdPrefix": "YOUR_DESIRED_COMMAND_PREFIX_HERE",
}```

After that, open Sources/main.swift and remove everything and replace it with the example below.

```swift
import Sword
import Foundation

//DEFINING A STRUCT TO INTERPRET CONFIG.JSON FILE

struct Config: Codable {
    let token: String
    let cmdPrefix: String
}
//ERROR HANDLING
enum ConfigGetError: Error {
    case cannotMakeURL
}

//GETTING THE BOT TOKEN AND CMD PREFIX FROM CONFIG.JSON

func getBotConfig() throws -> Config {

//COMMENTED LINES TO PRINT YOUR EXECUTABLE'S DIRECTORY
//    print(Bundle.main.executablePath!)
//    print(Bundle.main.bundlePath)
    guard let configFileURL = Bundle.main.url(forResource: "config", withExtension: "json") else {
        print("Can't make URL.")

        throw ConfigGetError.cannotMakeURL
    }
//    print(configFileURL)
    let configFileData = try Data(contentsOf: configFileURL)
    return try JSONDecoder().decode(Config.self, from: configFileData)
}


//GETTING THE BOT ONLINE

let config = try getBotConfig()
let myCommandPrefix = config.cmdPrefix
let options = ShieldOptions(
    prefixes: ["\(cmdPrefix)"]
)
let myBot = Shield(token: config.token, shieldOptions: options)

myBot.editStatus(to: "online", playing: "with Sword!")


//BASIC TEST COMMANDS

myBot.register("ping", message: "Pong!")

myBot.register("echo") { msg, args in
    msg.reply(with: args.joined(separator: " "))
}

myBot.connect()

```


### CocoaPods
Adding Sword to your iOS, watchOS, or tvOS application is easier than ever with CocoaPods. All you have to do is add Sword as a dependency to your Podfile, something like this:

```ruby
target 'yourappnamehere' do
  use_frameworks!
  pod 'Sword'
end
```

Then all you have to do is `pod install` and you're ready to go.

## Running the bot (SPM)
Build the libraries with `swift build`, then type `swift run`

## Running the bot in Xcode (SPM)
To run the bot in Xcode, you first have to compile the libraries with `swift build`. Then to build the xcode project, type `swift package generate-xcodeproj`. Finally, type `open yourswiftexecutablehere.xcodeproj`, look at the top and follow the steps below

![Step 1](images/step1.png)

![Step 2](images/step2.png)

![Step 3](images/step3.png)

Then click the play button!

## Links
[Documentation](http://sword.azoy.me) - (created with [Jazzy](https://github.com/Realm/Jazzy))

Join the [API Channel](https://discord.gg/99a3xNk) to ask questions!

