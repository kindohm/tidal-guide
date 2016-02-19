# Tidal

You've installed Dirt and Tidal, maybe even made a few sounds, but now you're
ready to get to business and really learn Tidal. This guide is for beginners
who are learning Tidal. It will help you get started with simple patterns
and walk you through all the way to complex compositions.

## TOC

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

`d1 $ sound "bd sd"`

`d1 $ sound "bd sd hh cp mt arpy drum"`

`d1 $ sound "bd sd hh cp mt arpy drum odx bd arpy bass2 feel future"`

No matter how many samples you put in a pattern, they will always be
distributed evenly within a single cycle.

## (Groups of) Patterns Within Patterns

Use Tidal's _square braces_ syntax to create a pattern grouping:

`d1 $ sound "[bd sd] cp"`

Square braces allow several events to be played inside of a single event.
Practically, this means you can create denser sub-divisions of samples:

`d1 $ sound "bd [sd sd]"`

`d1 $ sound "bd [sd sd sd]"`

`d1 $ sound "bd [sd sd sd sd]"`

`d1 $ sound "[bd bd] [sd sd sd sd]"`

`d1 $ sound "[bd bd bd] [sd sd]"`

`d1 $ sound "[bd bd bd bd] [sd]"`

You can even nest groups inside groups to create very dense and complex
patterns:

`d1 $ sound "[bd bd] [bd [sn [sn sn] sn] sn]"`

### Layering Instead of Grouping

You can also layer up several loops, by using commas to separate the different parts:

`d1 $ sound "[bd bd bd, sn cp sn cp]"`

This would play the sequence `bd bd bd` at the same time as `sn cp sn cp`.
Note that the first sequence only has three events, and the second one has four.
Because tidal ensures both loops fit inside same duration, you end up with
a polyrhythm.

You can layer any number of patterns:

`d1 $ sound "[bd bd bd, sn cp sn cp, arpy arpy, odx]"`

And of course use groupings inside of the layers:

`d1 $ sound "[bd bd bd, [sn sn] cp, arpy [arpy [arpy arpy] arpy arpy], odx]"`


## Modifying Sequences With Functions

## Changing the Sounds of your Samples

## Setting Tempo

## (Automatically) Selecting Samples from Folders

## Combining Different Patterns Together

## Oscillation with Continuous Patterns

## Rests

## Polyrhythms

## Shifting Time

## Introducing Randomness

## Creating Variation In Patterns

## Creating "Fills"

## Composing Multi-Part Patterns

## Truncating Samples with "cut"

## Transitions To New Patterns

## Solos
