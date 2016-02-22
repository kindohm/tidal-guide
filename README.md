> NOTE to Tidal documentation contributors:
> This is a draft update to the "Creating Patterns"
> section of the existing tidal.lurk.org site. e.g. this is the guide that
> nearly all new users will use to learn Tidal.
>
> This newer draft was created after receiving feedback from new users about
> not knowing that many basic live coding and performance possibilities
> existed with Tidal. The draft is meant to read like a book, chapter by chapter
> (or section by section in this case).
>
> This content will need to be ported to Jekyll chapter format if used.

# Tidal: the guide

You've installed Dirt and Tidal, maybe even made a few sounds, but now you're
ready to get to business and start really learning Tidal. This guide will help you get started with simple patterns
and walk you through all the way to complex compositions.

> NOTE: please _play_ with the code as you read. Run your own experiments
> by making slight modification to these examples!


## Creating Rhythmic Sequences

### Play a Single Sample

Tidal starts with nine connections to the dirt synthesiser, named from d1 to d9.
Here's a minimal example, that plays a bass drum every cycle:

`d1 $ sound "bd"`

In the code above, `sound` tells us we're making a pattern of sound samples, and
`"bd"` is a pattern that contains a single sound. `bd` is a sample of a bass drum.
Samples live inside the `/samples` folder which came with Dirt, and
each sub-folder under `/samples` corresponds to a sample name (like `bd`).

We can pick a different sample in the `bd` folder by adding a colon (`:`) then
a number. For example this picks the fourth bass drum (it counts from zero,
so `:3` gives you the fourth sound in the folder):

`d1 $ sound "bd:3"`

If you specify a number greater than the number of samples in a folder, then
Tidal just "wraps" around back to the first sample again (e.g. in a folder
with five samples, "bd:5" would play "bd:0").

### Sequences From Multiple Samples

Putting things in quotes actually defines a sequence. For example, the following
gives you a pattern of bass drum then snare:

`d1 $ sound "bd sn"`

When you run the code above, you are replacing the previous pattern with another
one on-the-fly. Congratulations, you're live coding.

> You can find (and if you like, add to) the samples in the samples folder
> inside the Dirt folder. They're in 'wav' format.

### What is a Cycle?

A cycle is the main "loop" of time in Tidal. The cycle repeats forever in the
background, even when you've stopped samples from playing. The cycle's duration
always stays the same unless you modify it with `cps` or `bps`.

> We'll cover `cps` and `bps` later.

All of the samples inside of a pattern get squashed into a single cycle.
The patterns below all loop over the same amount of time:

```
d1 $ sound "bd sd"
d1 $ sound "bd sd hh cp mt arpy drum"
d1 $ sound "bd sd hh cp mt arpy drum odx bd arpy bass2 feel future"
```

No matter how many samples you put in a pattern, they will always be
distributed evenly within a single cycle.

## (Groups of) Patterns Within Patterns

Use Tidal's _square braces_ syntax to create a pattern grouping:

`d1 $ sound "[bd sd] cp"`

Square braces allow several events to be played inside of a single event.
Practically, this means you can create denser sub-divisions of samples:

```
d1 $ sound "bd [sd sd]"
d1 $ sound "bd [sd sd sd]"
d1 $ sound "bd [sd sd sd sd]"
d1 $ sound "[bd bd] [sd sd sd sd]"
d1 $ sound "[bd bd bd] [sd sd]"
d1 $ sound "[bd bd bd bd] [sd]"
```

You can even nest groups inside groups to create very dense and complex
patterns:

`d1 $ sound "[bd bd] [bd [sn [sn sn] sn] sn]"`

### Layering (Polyrhythms) Instead of Grouping

You can also layer up several loops, by using commas to separate the different parts:

`d1 $ sound "[bd bd bd, sn cp sn cp]"`

This would play the sequence `bd bd bd` at the same time as `sn cp sn cp`.
Note that the first sequence only has three events, and the second one has four.
Because tidal ensures both loops fit inside same duration, you end up with
a polyrhythm.

You can layer any number of patterns to create many polyrhythms:

`d1 $ sound "[bd bd bd, sn cp sn cp, arpy arpy, odx]"`

And of course you can use groupings inside of the layers:

`d1 $ sound "[bd bd bd, [sn sn] cp, arpy [arpy [arpy arpy] arpy arpy], odx]"`

## Repetition and Pattern Speed

There are two short-hand symbols you can use inside patterns to speed things up
or slow things down: `*` and `/`. You could think of these like multiplication
and division.

Use the `*` symbol to make a pattern, or part of a pattern, repeat as many times
as you'd like:

`d1 $ sound "bd*2"`

> This is the same as doing `d1 $ sound "bd bd"`

The code above uses `*2` to make a sample play twice.

You can use the `/` symbol to make a part of a pattern slow down, or occur
less often:

`d1 $ sound "bd/2"`

The code above uses `/2` to make a sample play half as often, or once every
2nd cycle.

Using different numbers works as you'd expect:

```
d1 $ sound "bd*3" -- plays the bd sample three times each cycle
d1 $ sound "bd/3" -- plays the bd samples only once each third cycle
```

### Using * and / on Groups

You can apply the `*` and `/` symbols on groups of patterns:

```
d1 $ sound "[bd sd]*2 cp"
d1 $ sound "[bd sd] cp/2"
d1 $ sound "[[bd sd] cp]*2" -- speeds up the entire pattern by 2
d1 $ sound "[[bd sd] cp]/2" -- slows down the entire pattern by 2
```

You can also use the symbols on nested groups to create more complex
symbol logic:

```
d1 $ sound "[bd sn sn*3]/2 [bd sn*3 bd*4]/3"
d1 $ sound "[bd [sn sn]*2]/2 [bd [sn bd]/2]*2"
```

## Modifying Sequences With Functions

Tidal comes into its own when you start building things up with functions which
transform the patterns in various ways.

For example, rev reverses a pattern:

```
d1 $ rev (sound "[bd bd] [bd [sn [sn sn] sn] sn]")
```

That's not so exciting, but things get more interesting when this is used in
combination with another function. For example every takes two parameters, a
number, a function and a pattern to apply the function to. The number specifies
how often the function is applied to the pattern. For example, the following
reverses the pattern every fourth repetition:

```
d1 $ every 4 (rev) (sound "bd*2 [bd [sn sn*2 sn] sn]")
```

You can also slow down or speed up the playback of a pattern, this makes it a
quarter of the speed:

```
d1 $ slow 4 $ sound "bd*2 [bd [sn sn*2 sn] sn]"
```

And this four times the speed:

```
d1 $ density 4 $ sound "bd*2 [bd [sn sn*2 sn] sn]"
```

Note that slow 0.25 would do exactly the same as density 4.

Again, this can be applied selectively:

```
d1 $ every 4 (density 4) $ sound "bd*2 [bd [sn sn*2 sn] sn]"
```

Note the use of parenthesis around (density 4), this is needed, to group
together the function `density` with its parameter 4, before being passed as a
parameter to the function `every`.

Instead of putting transformations up front, separated by the pattern by the $
symbol, you can put them inside the pattern, for example:

```
d1 $ sound (every 4 (density 4) "bd*2 [bd [sn sn*2 sn] sn]")
```

### Where are all the functions?

There are many types of functions that help you change patterns. Some of them
re-order sequences, some alter time, some provide conditional logic, and some
can help compose more complex patterns.

All of the functions available in Tidal can be found on the Reference page.

## Changing Sounds with Effects

Tidal has a number of effects that you can apply to sounds. Some of them do
simple things like change volume, and others do more complex things like
change sample rate or add delay.

You use an effect by adding the `#` operator between your sound pattern and
the effect:

```
d1 $ sound "bd*4" # gain "0.5"
```

The above code decreases the volume of the "bd" sample by 50%.

You can chain multiple effects together, separating them again with the `#`
operator:

```
d1 $ sound "bd*4" # gain "0.5" # delay "0.5"
```

The code above decreases the volume by 50% and also applies a "delay" effect
at a level of 0.5.

### Effects are patterns too

You may notice that the values of effects are specified in double quotes. This
means that you can put a pattern of effect values inside the quotes:

```
d1 $ sound "bd*4" # gain "1 0.8 0.5 0.7"
```

Effect patterns follow all the same grouping rules as sound patterns:

```
d1 $ sound "bd*4 sn*4" # gain "[[1 0.8]*2 [0.5 0.7]]/2"
```

And you can also apply functions to effect patterns:

```
d1 $ sound "bd*4" # gain (every 3 (rev) $ "1 0.8 0.5 0.7")
```

> Like with the `sound` example earlier, you must use parenthesis after `gain`
> in order to specify a function on the `gain` pattern.

### Modifying effect values

The `#` operator is just a shortcut to a longer form of operator called `|=|`.
The `|=|` operator means something special about combining patterns, but we'll
cover that later. All you need to know right now is that `|=|` will set an
effect's value equal to a pattern.

However, you can also change an effect value on the other side of a pattern:

```
d1 $ (|=| speed "2") $ sound "arpy*4" |=| speed "1"
```

In the code above, the left-most effect overrides the original effect
that was specified on the right. In this case, `speed` will always equal 2.

You can do this conditionally:

```
d1 $ every 2 (|=| speed "2") $ sound "arpy*4" |=| speed "1"
```

There are other types of operators that allow you to perform arithmetic:

```
|+|
|-|
|*|
|/|
```

For example, using `|+|` will perform an _addition_ operation and _add_ to
an original value:

```
d1 $ every 2 (|+| speed "1") $ sound "arpy*4" |=| speed "1"
```

> The code above results in a speed of "2" every other cycle.

The following will multiply values:

```
d1 $ every 2 (|*| speed "1.5") $ sound "arpy*4" |=| speed "1"
```

More complex patterns and chaining can be done, and with any effect, of course:

```
d1 $ every 3 (|-| up "3") $ every 2 (|+| up "5") $ sound "arpy*4" |=| up "0 2 4 5"
```


### Some Common Effects

Here is a quick list of some effects you can use in Tidal (the full list is
available in the Reference section):

- gain (changes volume, values from 0 to 1)
- pan (pans sound right and left, values from 0 to 1)
- shape (a type of amplifier, values from 0 to 1)
- vowel (a vowel formant filter, values include a, e, i, o, and u)
- speed (changes playback speed of a sample, see below)

## Changing Sample Playback Speed (and Pitch)

You can change the playback speed of a sample in Tidal using the `speed` effect.
You can use `speed` to change pitches, to create a weird effect, or to match
the length of a sample to a specific period of the cycle time.

You can set a sample's speed by using the `speed` effect with a number.

- `speed "1"` plays a sample at its original speed
- `speed "0.5"` plays a sample at half of its original speed
- `speed "2"` plays a sample at double its original speed

```
d1 $ sound "arpy" # speed "1"
d1 $ sound "arpy" # speed "0.5"
d1 $ sound "arpy" # speed "2"
```

Just like other effects, you can specify a pattern for speed:

```
d1 $ sound "arpy*4" # speed "1 0.5 2 1.5"
```

You can also reverse a sample by specifying negative values:

```
d1 $ sound "arpy*4" # speed "-1 -0.5 -2 -1.5"
```

### Play a sample at multiple speeds simultaneously

Use the pattern grouping syntax with a comma to cause `speed` to play
a sample back at multiple speeds at the same time:

```
d1 $ sound "arpy" # speed "[1, 1.5]"
d1 $ sound "arpy*4" # speed "[1 0.5, 1.5 2 3 4]"
```

### 12-tone scale speeds

You can also use the `up` function to change playback speed. `up` is a shortcut
effect that matches speeds to half steps on a 12-tone scale. For example, the
following plays a chromatic scale:

```
d1 $ sound "arpy*12" # up "0 1 2 3 4 5 6 7 8 9 10 11"
```

> You can also use the `run` function to create an incrementing pattern of
> integers: `d1 $ sound "arpy*12" # up (run 12)`. The `run` function will be
> discussed later.

## Euclidean Sequences

If you give two numbers in parenthesis after an element in a pattern, then Tidal
will distribute the first number of sounds equally across the second number of
steps:

```
d1 $ sound "bd(5,8)"
```

You can also use the `e` function to do this. `e` takes the same two arguments
as what is used in the parenthesis above:

```
d1 $ e 5 8 $ sound "bd"
```

You can use the parenthesis notation within a single element of a pattern:

```
d1 $ sound "bd(3,8) sn*2"
d1 $ sound "bd(3,8) sn(5,8)"
```

You can also use the `e` function to apply a Euclidean algorithm over a
complex pattern, although the results are a little difficult to describe:

```
d1 $ e 3 8 $ sound "bd*2 [sn cp]"
```

As a bonus, it is possible to pattern the parameters, for example to
alternate between 3 and 5 elements:

```
d1 $ sound "bd([5 3]/2,8)"
```


These types of sequences use "Bjorklund's algorithm", which wasn't made for
music but for an application in nuclear physics, which is exciting. More
exciting still is that it is very similar in structure to the one of the first
known algorithms written in Euclid's book of elements in 300 BC. You can read
more about this in the paper
[The Euclidean Algorithm Generates Traditional Musical Rhythms](http://cgm.cs.mcgill.ca/~godfried/publications/banff.pdf)
by Toussaint. Some examples from this paper are included below,
including rotation in some cases.

- (2,5) : A thirteenth century Persian rhythm called Khafif-e-ramal.
- (3,4) : The archetypal pattern of the Cumbia from Colombia, as well as a Calypso rhythm from Trinidad.
- (3,5,2) : Another thirteenth century Persian rhythm by the name of Khafif-e-ramal, as well as a Rumanian folk-dance rhythm.
- (3,7) : A Ruchenitza rhythm used in a Bulgarian folk-dance.
- (3,8) : The Cuban tresillo pattern.
- (4,7) : Another Ruchenitza Bulgarian folk-dance rhythm.
- (4,9) : The Aksak rhythm of Turkey.
- (4,11) : The metric pattern used by Frank Zappa in his piece titled Outside Now.
- (5,6) : Yields the York-Samai pattern, a popular Arab rhythm.
- (5,7) : The Nawakhat pattern, another popular Arab rhythm.
- (5,8) : The Cuban cinquillo pattern.
- (5,9) : A popular Arab rhythm called Agsag-Samai.
- (5,11) : The metric pattern used by Moussorgsky in Pictures at an Exhibition.
- (5,12) : The Venda clapping pattern of a South African children’s song.
- (5,16) : The Bossa-Nova rhythm necklace of Brazil.
- (7,8) : A typical rhythm played on the Bendir (frame drum).
- (7,12) : A common West African bell pattern.  
- (7,16,14) : A Samba rhythm necklace from Brazil.
- (9,16) : A rhythm necklace used in the Central African Republic.  
- (11,24,14) : A rhythm necklace of the Aka Pygmies of Central Africa.
- (13,24,5) : Another rhythm necklace of the Aka Pygmies of the upper Sangha.


## Setting Tempo

If you've made it this far without changing the tempo in all these examples,
then you're probably ready to change it up.

Tidal's core unit of time is cycles per second. It can be set with the
`cps` function:

```
cps 1
```

You can execute `cps` just like a pattern (using Shift+Enter in your editor).

`cps` accepts a positive numeric value that can include a decimal:

```
cps 1.5
cps 0.75
cps 10
```

### Setting BPM

Tidal also includes a helper function called `bps` to set "beats per second".
To set beats-per-minute, call `bps` with your bpm value, divided by 120:

```
-- sets a tempo of 170 BPM:
bps (170/120)

-- sets a tempo of 100 BPM:
bps (100/120)
```

## The Run function

There is a special utility function called `run` which will return a pattern of
integers up to a specified maximum. You can use `run` with effects to aid in
automatically generating a linear pattern:

```
d1 $ sound "arpy*8" # up (run 8)
d1 $ sound "arpy*8" # speed (run 8)
```

Because `run` returns a pattern, you can apply functions to its result:

```
d1 $ sound "arpy*8" # up (every 2 (rev) $ run 8)
```

For a more practical example of using `run`, read below about
selecting samples from folders.

## (Algorithmically) Selecting Samples from Folders

Perhaps one of Tidal's biggest strengths is that it can select samples from
Dirt folders based on an algorithm. There is a special function called `samples`
that combines a pattern of samples with a pattern of sample indexes from a folder:

```
d1 $ sound $ samples "drum*4" "0 1 2 3"

-- the code above equals this:
d1 $ sound "drum:0 drum:1 drum:2 drum:3"
```

This allows us to separate the sample folder name from the index inside the
folder, possibly with surprising results!

Remember the `run` function? Since `run` generates a pattern of integers, it
can be used with `samples` to automatically "run" through the sample indices
of a folder:

```
d1 $ sound $ samples "drum*4" (run 4)
```

And of course you can specify a different pattern of sample names:

```
d1 $ sound $ samples "drum arpy cp hh" (run 10)
```

> NOTE: if you specify a run value that is greater than the number of
> samples in a folder, then the higher number index will "wrap" to the
> beginning of the samples in the folder (just like with the colon notation).

You might sometimes see the samples function wrapped in parenthesis:


```
d1 $ sound (samples "drum arpy cp hh" (run 10))
```


## Combining Different Types of Patterns Together

Ok, remember when we started adding effects:

```
d1 $ sound "bd sn drum arpy" |=| pan "0 1 0.25 0.75"
```

What we're actually doing in the code above is
_combining two patterns together_: the `sound` pattern, and the `pan` pattern.
The special pipe operators (`|=|, |+|, |-|, |*|, |/|`), allow us to combine
two patterns.

Using the code above, we can actually swap sides and it sounds the same:

```
d1 $ pan "0 1 0.25 0.75" |=| sound "bd sn drum arpy"
```

The main thing to know when combining patterns like this is that the left-most
pattern determines the rhythm. Removing one of the elements from the `pan`
pattern on the left results in a cycle with three samples played:

```
d1 $ pan "0 1 0.25" |=| sound "bd sn drum arpy"
```

In the code above, the `pan` pattern determines the rhythm because it is the
left-most pattern. The `sound` pattern now only determines what samples are
played at what time. The sound pattern gets _mapped_ onto the pan pattern.

This allows us to do some unique things:

```
d1 $ up "0 0*2 0*4 1" |=| sound "[arpy, bass2, bd]"
```

Above, `sound` is merely just specifying three samples to play on each note.
The note rhythm is defined by `up`, while also specifying pitches.

## Oscillation with Continuous Patterns

So far we've only been working with discrete values. Tidal has some helper
functions to create continuous patterns using sine, saw, triangle, and square
waves:

```
d1 $ sound "bd*16" # pan sine1
```

The code above uses the `sine1` function to generate a sine wave oscillation of
values between 0 and 1 for the `pan` values.

In addition to `sine1`, there is also a `sine` function. What is the difference?

- `sine` produces values between -1 and 1
- `sine1` produces values between 0 and 1

Thus, the "1" suffix means only positive values.

In addition to the `sine/sine1` functions, Tidal also has `saw/saw1',
`tri/tri1`, and `square/square1`.

You can control oscillation speed with `slow` or `density`:

```
d1 $ sound "bd*16" # pan (slow 8 $ saw1)
d1 $ sound "bd*8 sn*8" # pan (density 1.75 $ tri1)
d1 $ sound "bd*8 sn*8" # speed (density 2 $ tri)
```

### Scaling Oscillation

You can tell the oscillation functions to scale themselves and oscillate
between two positive values:

```
d1 $ sound "bd*8 sn*8" # speed (scale 1 3 $ tri1)
d1 $ sound "bd*8 sn*8" # speed (slow 4 $ scale 1 3 $ tri1)
```

This technique works well for a slow low-pass filter cutoff:

```
d1 $ sound "hh*32" # cutoff (scale 0.001 0.1 $ slow 4 $ sine1) # resonance "0.1"
```

> NOTE: `scale` only works with positive values, so it is not (yet?) possible
> to scale to negative ranges.

> NOTE: despite the fact that the oscillator functions produce continuous values,
> they are still only heard during discrete sound events.

## Rests

So far we have produced patterns that keep producing more and more sound. What
if you want a rest, or gap of silence, in your pattern? You can use the "tilde"
`~` character to do so:

```
d1 $ sound "bd bd ~ bd"
```

Think of the `~` as a special sample name that produces silence.

## Polymeters

We talked about _polyrhythms_ earlier, but Tidal can also produce
_polymeter_ sequences. A polymeter pattern is one where two patterns
have different sequence lengths, but share the same pulse or tempo.

You use curly brace syntax in Tidal to create a polymeter rhythm:

`d1 $ sound "{bd hh sn cp, arpy bass2 drum notes can}"`

The code above results in a five-note rhythm being played at the pulse of
a four-note rhythm. If you switch the groups around, it results in a
four-note rhythm over a five-note rhythm:

`d1 $ sound "{arpy bass2 drum notes can, bd hh sn cp}"`

Sometimes you might want to create an odd polymeter rhythm without having to
explicitly create a base rhythm. You _could_ do this with rests:

`d1 $ sound "{~ ~ ~ ~, arpy bass2 drum notes can}"`

But a more efficient way is to use the `%` symbol after the closing curly
brace to specify the number of notes in the base pulse:

```
d1 $ sound "{arpy bass2 drum notes can}%4"

-- the above is the same as this:
d1 $ sound "{~ ~ ~ ~, arpy bass2 drum notes can}"
```

If "polymeter" sounds a bit confusing, there's a good explanation here:
http://music.stackexchange.com/questions/10488/polymeter-vs-polyrhythm

## Shifting Time Backwards and Forwards

You can use the `<~` and `~>` functions to shift time to the left and right.
With each of these functions, you can specify an amount, in cycle units.

```
d1 $ (0.25 <~) $ sound "bd*2 cp*2 hh sn"
d1 $ (0.25 ~>) $ sound "bd*2 cp*2 hh sn"
```

The above code shifts the patterns over by 0.25 cycles.

However, it's easier (and more practical?) to hear the shifting effect when
applying it conditionally every few cycles:

```
d1 $ every 3 (0.25 <~) $ sound "bd*2 cp*2 hh sn"
d1 $ every 3 (0.25 ~>) $ sound "bd*2 cp*2 hh sn"
```

You can shift patterns as little or as much as you'd like:

```
d1 $ every 3 (0.0625 <~) $ sound "bd*2 cp*2 hh sn"
d1 $ every 3 (1000 ~>) $ sound "bd*2 cp*2 hh sn"
d1 $ every 3 (1000.125 ~>) $ sound "bd*2 cp*2 hh sn"
```

## Introducing Randomness

Tidal can produce random patterns of integers and decimals. It can also
introduce randomness into patterns by removing random events.

### Random Decimal Patterns

Use the `rand` function to create a random value between 0 and 1 (useful for
effects):

`d1 $ sound "arpy*4" # pan (rand)`

Similar to `run`, `rand` can also be scaled:

`d1 $ sound "arpy*4" # pan (scale 0.25 0.75 $ rand)`

### Random Integer Patterns

Use the `irand` function to create a random integer up to a given maximum.
Perhaps the most common usage of `irand` is to produce a random pattern of
sample indices (similar to `run`):

`d1 $ sound $ samples "arpy*8" (irand 30)`

> The code above randomly chooses from 30 samples in the "arpy" folder.

### Removing or "Degrading" Pattern events

Tidal has a few ways to randomly remove events from patterns. You can use the
shorthand `?` symbol if you want to give an event a 50/50 chance of happening
or not on every cycle:

`d1 $ sound "bd?"`

In the code above, the "bd" sample has a 50% chance of being played on every
cycle.

You can add the `?` after the completion of any event or group in a pattern:

```
d1 $ sound "bd*16?"
d1 $ sound "bd sn? cp hh?"
d1 $ sound "[bd sn cp hh]?"
```

The `?` symbol is shorthand for the `degrade` function. The two lines below
are equivalent:

```
d1 $ sound "bd*16?"
d1 $ degrade $ sound "bd*16"
```

Related to `degrade` is the `degradeBy` function, where you can specify the
probability (from 0 to 1) that events will be removed from a pattern:

```
d1 $ degradeBy 0.25 $ sound "bd*16"
```

There is also `sometimesBy`, which executes a function based on a probability:

```
d1 $ sometimesBy 0.75 (slow 2) $ sound "bd*16"
```

The code above has a 75% chance of calling `slow 2` on every event in the
pattern.

There are other aliases for `sometimesBy`:

```
sometimes = sometimesBy 0.5
often = sometimesBy 0.75
rarely = sometimesBy 0.25
almostNever = sometimesBy 0.1
almostAlways = sometimesBy 0.9

d1 $ sometimes (density 2) $ sound "bd*8"
d1 $ rarely (density 2) $ sound "bd*8"
```

## Creating More Variation In Patterns

You can create a lot of cyclic variations in patterns by layering conditional
logic:

```
d1 $ every 5 (|+| speed "0.5") $ every 4 (0.25 <~) $ every 3 (rev) $
   sound "bd sn arpy*2 cp"
   # speed "[1 1.25 0.75 -1.5]/3"
```

In addition to `every` you can also use the `whenmod` conditional function.
`whenmod` takes two parameters; it executes a function when the remainder of
the current loop number divided by the first parameter is less than the
second parameter.

For example, the following will play a pattern normally for cycles 1-6, then
play it in reverse for cycles 7-8. Then normally again for six cycles, then
in reverse for two, and so on:

```
d1 $ whenmod 8 6 (rev) $ sound "bd*2 arpy*2 cp hh*22"
```

## Creating "Fills" and replacing patterns with const

You can think of a "fill" as a change to a regular pattern that happens
regularly. e.g. every 4 cycles do "xya", or every 8 cycles do "abc".

We've already been using `every` and `whenmod` to do pattern function fills:

```
d1 $ every 8 (rev) $ every 4 (density 2) $ sound "bd hh sn cp"
d1 $ whenmod 16 14 (# speed "2") $ sound "bd arpy*2 cp bass2"
```

However, what if you wanted to conditionally replace the pattern with a new one?
You can use the `const` function to completely replace a playing pattern.

Let's start with a trivial example where we use `const` to replace an entire
pattern all the time:

`d1 $ const (sound "arpy*3") $ sound "bd sn cp hh"`

In the code above, we've completely replaced the "bd sn cp hh" pattern with
an "arpy" pattern. `const` specifies the new pattern.

We can conditionally apply `const` using `every` or `whenmod`:

```
d1 $ whenmod 8 6 (const $ sound "arpy(3,8) bd*4") $ sound "bd sn bass2 sn"
d1 $ every 12 (const $ sound "bd*4 sn*2") $ sound "bd sn bass2 sn"
```

## Composing Multi-Part Patterns

There are a few ways that you can compose new patterns from multiple other
patterns. You can concatenate or "append" patterns in serial, or you can
"stack" them and play them together in parallel.

### Concatenating patterns in serial

You can use the `cat` function to add patterns one after another:

```
d1 $ cat [sound "bd sn:2" # vowel "[a o]/2",
          sound "casio casio:1 casio:2*2"
         ]
```

The `cat` function squeezes all the patterns into the space of one.
The more patterns you add to the list, the faster each pattern will be played so
that all patterns can fit into a single cycle.

```
d1 $ cat [sound "bd sn:2" # vowel "[a o]/2",
          sound "casio casio:1 casio:2*2",
          sound "drum drum:2 drum:3 drum:4*2"
         ]
```

`slowcat` will maintain the original playback speed of the patterns:

```
d1 $ slowcat [sound "bd sn:2" # vowel "[a o]/2",
              sound "casio casio:1 casio:2*2",
              sound "drum drum:2 drum:3 drum:4*2"
             ]
```

`slowcat` is a great way to create a linear sequence of patterns (a sequence
of sequences), giving a larger form to multiple patterns.

There's also `randcat', which will play a random pattern from the list.

### Playing patterns together in parallel

The `stack` function takes a list of patterns and combines them into a new
pattern by playing all of the patterns in the list simultaneously.

```
d1 $ stack [
  sound "bd bd*2",
  sound "hh*2 [sn cp] cp future*4",
  sound (samples "arpy*8" (run 16))
]
```

This is useful if you want to apply functions or effects on the entire stack:

```
d1 $ every 4 (slow 2) $ whenmod 5 3 (# speed "0.75 1.5") $ stack [
  sound "bd bd*2",
  sound "hh*2 [sn cp] cp future*4",
  sound (samples "arpy*8" (run 16))
] # speed "[[1 0.8], [1.5 2]*2]/3"
```

## Truncating Samples with "cut"

So far, all of our examples have used short samples. However, maybe you've
experimented with some long samples. Maybe you've noticed that really long
samples can cause a lot of bleed and unwanted sound.

With Tidal's `cut` effect, you can "choke" a sound and stop it from playing
when a new sample is triggered.

Consider the following example where we have a pattern of "arpy" sounds,
played at a low speed, so there is a lot of bleed into each sample:

`d1 $ sound $ samples "arpy*8" (run 8) # speed "0.25"`

We can stop this bleed by using `cut` and assigning the pattern a cut group of
"1":

`d1 $ sound $ samples "arpy*8" (run 8) # speed "0.25" # cut "1"`

No more bleed!

You can use any number for the cut group.

Cut groups are global, to the Tidal process, so if you have two Dirt connections,
use two different cut group values to make sure the patterns don't choke
each other:

```
d1 $ sound $ samples "arpy*8" (run 8) # speed "0.25" # cut "1"
d2 $ sound $ samples "bass2*6" (run 6) # speed "0.5" # cut "2"
```

This also works in a `stack`:

```
d1 $ stack [
   sound $ samples "arpy*8" (run 8) # speed "0.25" # cut "1",
   sound $ samples "bass2*6" (run 6) # speed "0.5" # cut "2" ]
```

## Transitions To New Patterns

Changing the pattern on a channel takes effect (almost) immediately.
This may not be what you want, especially when performing live!

That's why Tidal allows you to choose a transition that will introduce another
pattern, eventually replacing the current one.

For every Dirt channel, there's a transition channel that accepts a transition
function and a new pattern.

So instead of directly sending the new pattern to d1, we'll send it to the
corresponding transition channel t1 and give it a nice transition function:

```
d1 $ sound (samples "hc*8" (iter 4 $ run 4))
t1 anticipate $ sound (samples "bd(3,8)" (run 3))
```

To transition from here, simply change the pattern within t1, and in this case
also change the transition function:

```
t1 (xfadeIn 16) $ sound "bd(5,8)"
```

The above will fade over 16 cycles from the former pattern to the given new one.

Apart from anticipate and xfadeIn there are a lot more transition functions
e.g. some that will force you to keep changing your patterns to avoid repetitive
performances…
