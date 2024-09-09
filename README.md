# grapevine-notation-and-terminology
suggested notation and terminology for the grapevine. Precursor to formalization with typscript.

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

## function: TheReallyImportantGrapeRankFunction

This is one of the cornerstone functions of the grapevine

- inputs: G_in, R, P
- output: G_out

