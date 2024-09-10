# grapevine-notation-and-terminology
suggested notation and terminology for the grapevine. Precursor to formalization with typscript.

## main goal

Primary objective is to build the function: **calculateGrapeRankScorecards** 
- inputs: G_in, R, P
- output: G_out

G_out = calculateGrapeRankScorecards(G_in, R, P)

The following [figure](https://i.nostr.build/Q3pw3GFEfO1rb4k1.png) illustrates how this function is represented on a Worldview:

![](https://i.nostr.build/Q3pw3GFEfO1rb4k1.png)

Note that the interpretation step, which produces the array R of ratings r, is completely external to the calculateGrapeRankScorecards function.

# Variables

## major variables: 
- r: oGrapeRankRating, a.k.a "grapevine-formatted rating," "grapevine rating" or simply "rating"
- R: aGrapeRankRatings, an array R of ratings r
- S: oGrapeRankScorecard, a.k.a "scorecard"
- G: aGrapeRankScorecards, an array G of scorecards S
- P: oGrapeRankParameters

## minor variables:
- context: string, which may be human readable e.g. "product quality", nostr event id or naddr, etc.
- ratingType: string. Examples: ratingType = 5-star; ratingType = boolean0-1
- rateeType: string. Examples: rateeType = nostr pubkey; rateeType = movie title.
- raterType: string. (In nostr, maybe raterType = pubkey always?)
- rating (aka score): number
- confidence: number in [0, 1]
- input: a number in [0, inf)
- average: number
  
## variable: oGrapeRankRating (r)

- rater: pubkey
- ratee: may be a pubkey or some other string that serves as a unique identifier, e.g. an event id, naddr, human-readable string like a movie name
- rating, a.k.a. score: a number between min and max, which are specified by ratingType
- confidence: a number between 0 and 1
- context (optional if element in R and context is held constant in R). 
- ratingType (optional if an element in R and ratingType is held constant in R)
- rateeType (optional if an element in R and rateeType is held constant in R)

## variable: aGrapeRankRatings (R) 

and array of r but where there is no need for ratingType or rateeType (or context?) because they are constant throughout R. 

## variable: oGrapeRankScorecard (S)

- observer: pubkey
- observee: same type as ratee (above)
- influence: number, and always equals average * confidence
- average: a number between min and max (depending on ratingType)
- confidence: a number between 0 and 1
- input: a nonnegative number; an element in [0, inf)
- ~~importance: (optional because usually importance = influence, although there may be instances where this is not the case)~~
- context: string
- ratingType: string (optional)
- rateeType: string (optional)

We should envision that at some point, there will be a THRIVING MARKET for individual scorecards S. It will be an easy matter to "interpret" a scorecard S by converting it into a rating r. 
- r.rater = S.observer
- r.ratee = S.observee
- r.score = S.average (or S.influence ??)
- r.confidence = S.confidence
- r.context = S.context

Note: the end user may want in some cases to alter the above identities. In particular, the context. This is one reason we call it *interpretation*! Another example: suppose the scorecard uses movie title, _The Godfather_, but the end user prefers to use the imdb database ID to represent ratee. In that case, the interpretation of S will involve converting S.observee (movie title) into the imdb ID.

## variable: aGrapeRankScorecards (G)

a table or array of S but where there is no need for observer or ratingType columns because they should be assumed to be identical for every scorecard in the table

## variable: oGrapeRankParameters (P)

A list of parameters 

- seed user. For now, there is only one seed user. In theory, there could be more than one. So this should be an array of strings.
- rigor: a number
- attenuation factor: a number between 0 and 1. This is a factor in the weight
- default rater influence: should be zero but in theory doesn't have to be
- default ratee average: should be zero but in theory doesn't have to be
- default ratee confidence: a number between 0 and 1
- ratingType / scoreType: includes whether the rating (or average) is integer or decimal, min score, max score. A question to consider is whether it makes sense for the rating to be one var type (e.g. integer) while allowing the average to be another (e.g. decimal). I'm thinking they need to be the same.

default ratee average and confidence define a single, additional rating that is automatically applied to every ratee, where the author is the seed user. Setting default ratee confidence = 0 is the same effect as completely omitting this rating. 

# Functions

## function: interpretationOfFollowsAndMutes

input: seedUserPubkey

output: R_0, G_init

Step 1: add seed user pubkey to G_init, with influence = confidence = average = 1, input = inifnity or 9999 or n/a

Step 2: fetch follows and mutes of seed user

- for each follow, add r to R_0, where r = { rater: seedUser, ratee: followedUser, score = 1, confidence = 0.05, context: botOrNot }
- for each mute, add r to R_0, where r = { rater: seedUser, ratee: mutedUser, score = 0, confidence = 0.1, context: botOrNot }
- add each followed pubkey to G_init, with influence = input = confidence = average = 0

Step 3: fetch follows and mutes of each pk in G_init whose follows and mutes have not already been fetched, and process them as per step 2, but replace rater: seedUser with rater: pk

Step 4: repeat step 3 until either all available follows and mutes have been processed, or a predetermined number N times, where N = the max hop distance from the seedUser

return G_init, R_0

## function: calculateGrapeRankScorecards

This is one of the cornerstone functions of the grapevine

- inputs: G_in, R, P
- output: G_out

Step 1: Iterate through every rating in R and establish two variables: 
- aRatees, an array of all ratees 
- oRaters, a lookup table of all raters and their influence scores which will be used in subsequent steps

~~Step 2: initialize G_out with one row for each item in aRatees.~~
- For all users except the seed user, set influence = average = confidence = input = 0
- For the seed user, these variables will be something else. The parameters will define what exactly to do in this case; not sure how exactly that will be encoded in P. For the baseline Grapevine WoT Score as implemented in brainstorm, for seed user we have: influence = average = confidence = 1, and input = infinity (in theory) or 9999 (in practice) or n/a.

Step 2: Iterate through each ratee in aRatees
- call calculateScorecard which returns: oScorecard = { average, input, confidence, influence }
- add a row to G_out with these results; G_out[ratee] = oScorecard

Maybe wrap Step 2 in a function: calculateScorecard

Step 3: return G_out

## function: calculateScorecard

- inputs: ratee, oRaters, R
- outputs: oScorecard.influence, oScorecard.average, oScorecard.confidence, oScorecard.input

step 1: initialize totalProduct = totalInput = 0
- call processRating to process the default rating 
- Iterate through each rating r in R; if r.ratee == ratee, then call processRating to process r

step 2:
- average = totalProduct / totalInput
- input = totalInput
- confidence = calculateConfidence(input, rigor)
- influence = calculateInfluence(average, confidence)

Step 3: return oScorecard = { influence, average, confidence, input }

## function: processRating

This function calculates the product and the input for a given rating; product and input are then used to increment totalProduct and totalInput

- input: rating, ...
- output: { product, input }

Step 1: 
- weight = calculateWeight( attenuationFactor, raterInfluence, ratingConfidence, rater, seedUser )
- input = weight

Step 2: product = weight * score

Step 3: return { product, input }

## function: calculateWeight

This is the weight of an individual rating r. It is called once for each r by the *calculate weighted average* function. The attenuationFactor is only included in the weight if the rater != seed user.

- inputs: r, raterInfluence, attenuationFactor, seedUser
- output: weight, a number between 0 and 1

if seed user != rater:
- weight = attenuationFactor * raterInfluence * r.confidence

if seed user == rater:
- weight = r.confidence (because raterInfluence = 1 by definition, and also attenuationFactor = 1 by definition for the seed user)

## function: calculateConfidence

- inputs: rigor, input
- output: confidence, a number between 0 and 1 (might be exceptions to this??)

alpha = (a function of rigor)

(probably it will make more sense to calculate alpha only once and pass alpha into this function rather than pass in rigor and do the same calculation of alpha over and over)

confidence = (1 - e^(- alpha * input) )

## function: calculateInfluence

- inputs: average, confidence
- output: influence

return influence = average * confidence

This is simple enough equation that it maybe doesn't need it's own function; but might there be instances where we want to alter it somehow? Perhaps. In which case it will be useful for it to have a dedicated function.


