> NOTE to Tidal documentation contributors:
> This is a rough outline and idea for an updated version to the "Creating Patterns"
> section of the existing tidal.lurk.org site. e.g. this is the guide that
> newer folks will use to learn Tidal.

# Tidal: the guide

You've installed Dirt and Tidal, maybe even made a few sounds, but now you're
ready to get to business and really learn Tidal. This guide is for beginners
who are learning Tidal. It will help you get started with simple patterns
and walk you through all the way to complex compositions.

> NOTE: please _play_ with the code as you read. Run your own experiments
> by making slight modification to these samples!


## Creating Rhythmic Sequences

### Play a Single Sample

Tidal starts with nine connections to the dirt synthesiser, named from d1 to d9.
Here's a minimal example, that plays a bass drum every loop:

`d1 $ sound "bd"`

In the code above, sound tells us we're making a pattern of sound samples, and
"bd" is a pattern that contains a single sound. bd is a sample of a bass drum.
Samples live inside the /samples folder which came with the Dirt code, and
each sub-folder under /samples corresponds to a sample name (like "bd").

We can pick a different sample in the "bd" folder by adding a colon (:) then
a number. For example this picks the fourth bass drum (it counts from 0,
so :3 gives you the fourth sound in the folder):

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


## Shifting Time Backwards and Forwards

## Introducing Randomness

## Creating Variation In Patterns

## Creating "Fills"

## Composing Multi-Part Patterns

## Truncating Samples with "cut"

## Transitions To New Patterns

## Solos

## Lambdas
