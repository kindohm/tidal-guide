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
