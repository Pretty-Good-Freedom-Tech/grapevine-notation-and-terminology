# grapevine-notation-and-terminology
suggested notation and terminology for the grapevine. Precursor to formalization with typscript.

# Datasets

## datasets: GrapeRankRatings, GrapeRankRatings, GrapeRankScorecards

These datasets may take the form of sql tables or objects

## dataset: GrapeRankRatings (R)

- rater: pubkey
- ratee: may be a pubkey or some other string that serves as a unique identifier, e.g. an event id, naddr, human-readable string like a movie name
- score: usually a number between min and max, which are specified by ratingType
- confidence: a number between 0 and 1
- context: string (optional)
- ratingType (optional)
- rateeType (optional)

Context, ratingType, and rateeType are optional and may be omitted IF every entry in the dataset would have the same value

## dataset: GrapeRankScorecard

- observer: pubkey
- observee: same type as ratee (above)
- influence: number, and always equals average * confidence
- average: a number between min and max (depending on ratingType)
- confidence: a number between 0 and 1
- input: a nonnegative number; an element in [0, inf)
- importance: (optional because usually importance = influence, although there may be instances where this is not the case
- ratingType: (optional)

## dataset: GrapeRankScorecards (G)

a list of GrapeRankScorecards

## dataset: parameters (P)

A list of parameters 

- rigor:
- attenutation factor: a number between 0 and 1
- default average: should be zero but in theory doesn't have to be
- default confidence

(more?)

# Functions

## function: TheReallyImportantGrapeRankFunction

This is one of the cornerstone functions of the grapevine

- inputs: G_in, R, P
- output: G_out

Step 1: Iterate through every rating in R and establish an array aRatees of all ratees ~~(and all contexts if the optional context col is provided)~~

Step 2: initialize G_out with one row for each item in aRatee

Step 2: Iterate through each ratee in aRatees, calculate: average, input, confidence, influence, and update G_out accordingly

Step 3: return G_out

## function: calculate weighted average

## function: calculate input

## function: calculate confidence

- inputs: rigor, input
- output: confidence, a number between 0 and 1

alpha = (a function of rigor)

(probably it will make more sense to calculate alpha only once and pass alpha into this function rather than pass in rigor and do the same calculation of alpha over and over)

confidence = (1 - e^(- alpha * input) )

## function: calculate influence

- inputs: average, confidence
- output: influence

influence = average * confidence



