---
title: "Resolving a runtime variable into a static for use in generics"
tags: Nim statics generics DitakticBot
---

Problem: I'm coding a bot for a game that has an NxN board. The most efficient way to do this in terms of memory is to use arrays - which requires the program to know what size the array is at compile time or to use statics. This then translates to a lot of my other code which uses this as a generic parameter. The trouble is that in actuality, I don't know this number until runtime. For example, when creating a match, I set the board size at that point. So, how do I convert between what's a runtime and a static?

Fortunately, for me, there are only so many values that N can be: 3-8. This means that there should be a fairly easy way to choose a static given a runtime. 

Consider the following code:

```nim
const size3 = 3'u
const size4 = 4'u
const size5 = 5'u
const size6 = 6'u
const size7 = 7'u
const size8 = 8'u

case size:
of size3:
    do_this(size3)
of size4:
    do_this(size4)
of size5:
    do_this(size5)
of size6:
    do_this(size6)
of size7:
    do_this(size7)
of size8:
    do_this(size8)
else:
    do_this(size9)
```

A bit basic, but this basic framework will allow the program to select which specific do_this to activate based on the runtime value. With a bit more work, we can make this more generic and usable for any function:

```nim
template chooseSizeWithRes*[T](size: uint, toApply: proc, args: varargs[untyped]): (T, Error) =
    const size3 = 3'u
    const size4 = 4'u
    const size5 = 5'u
    const size6 = 6'u
    const size7 = 7'u
    const size8 = 8'u

    case size:
    of size3:
       toApply(size3, args)
    of size4:
        toApply(size4, args)
    of size5:
        toApply(size5, args)
    of size6:
        toApply(size6, args)
    of size7:
        toApply(size7, args)
    of size8:
        toApply(size8, args)
    else:
        (default(typeof(T)), newError("Invalid size"))
```

Don't worry too much about the return values - this is just a way I used this so it could return a result and an optional error with text.

Now this can be called on any function with a statically sized argument:

```nim
let (gConfig, err) = chooseSizeWithRes[GameConfig](gameSize, newGameConfigBySize, gameNumber, myColor, opponent, komi, flats, caps)
```

That function - newGameConfigBySize is defined elsewhere as a way to create a new game with an NxN board, where the N is a static uint:

```nim
proc newGameConfigBySize(size: static uint, gameNumber: string, myColor: Color, opponent: string, komi: int8, flats: uint8, caps: uint8, swap: bool = true): (GameConfig, Error) =
    let (game, err) = newGame(size, komi, swap)
    if ?err:
        return (default(GameConfig), newError("Could not instantiate game"))

    var emptyTps = @[game.toTps]

    return (GameConfig(gameNumber: gameNumber, gameSize: size, myColor: myColor, opponent: opponent, komi: komi, flats: flats, caps: caps, tpsHistory: emptyTps), default(Error))
```

The biggest downside to this method is that it relies on the fact that there are few options. But if there really were a large enough pool, then having the variable as a static to begin with is probably the wrong approach, and a sequence should be used instead of an array.