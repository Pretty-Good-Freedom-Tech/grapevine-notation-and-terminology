# grapevine-notation-and-terminology
suggested notation and terminology for the grapevine. Precursor to formalization with typscript.

# Variables

## variables: 
- r: GrapeRankRating
- R: GrapeRankRatings, an array of ratings 
- S: GrapeRankScorecard
- G: GrapeRankScorecards, an array of S
- P: GrapeRankParameters

## variable: GrapeRankRating (r)

- rater: pubkey
- ratee: may be a pubkey or some other string that serves as a unique identifier, e.g. an event id, naddr, human-readable string like a movie name
- score: a number between min and max, which are specified by ratingType
- confidence: a number between 0 and 1
- context: string (optional if element in R and context is held constant in R). String may be human readable, nostr event id or naddr, etc.
- ratingType (optional if an element in R and ratingType is held constant in R). Examples: ratingType = 5-star; ratingType = boolean0-1
- rateeType (optional if an element in R and rateeType is held constant in R). Examples: rateeType = nostr pubkey; rateeType = movie title. 

## variable: GrapeRankRatings (R) 

and array of r but where there is no need for ratingType or rateeType (or context?) because they are constant throughout R. 

## variable: GrapeRankScorecard (S)

- observer: pubkey
- observee: same type as ratee (above)
- influence: number, and always equals average * confidence
- average: a number between min and max (depending on ratingType)
- confidence: a number between 0 and 1
- input: a nonnegative number; an element in [0, inf)
- importance: (optional because usually importance = influence, although there may be instances where this is not the case)
- ratingType: (optional)

We should envision that at some point, there will be a THRIVING MARKET for individual GrapeRankScorecards.

## variable: GrapeRankScorecards (G)

a table or array of S but where there is no need for observer or ratingType columns because they should be assumed to be identical for every scorecard in the table

## variable: GrapeRankParameters (P)

A list of parameters 

- rigor: a number
- attenuation factor: a number between 0 and 1
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



