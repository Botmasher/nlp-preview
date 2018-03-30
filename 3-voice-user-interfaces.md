# Voice User Interfaces

## 1. Welcome to Voice User Interfaces!
- VUI: speech platform enabling humans to communicate with machines by voice
- once the stuff of sci-fi, with spaceship crews talking to computers
- voice-enabled agents now common
- easy to forget how much is going on
	1. convert sound waves to text using ML and probabilistic models
	2. reason over text using AI logic for meaning
	3. formulate response
	4. response text converted back into speech
- general pipeline for building end-to-end voice-enabled app
- focus on conversational AI apps
- reasons why voice interfaces require different approach than other interfaces

## 2. VUI Overview
- three piece pipeline
1. voice to text
	- speech recognition
	- human voice converted into audio signal
	- signal sampled at some rate and converted into vectors of component frequencies
	- vectors as features of sound in dataset (visible on spectrogram), so kind of **feature extraction**
	- decode or recognize vectors as word or sentence
	- acoustic model: probabilistic models that work with time series data for sound patterns
	- now have best guess of what words are
	- **language model**: added to decoder to consider likelihood of what was said
		- humans have language model in head 
		- decoder may also need **accent model**
	- acoustic model + language model + accent model
		- if well trained on many representative examples, highly probable to produce correct text
	- there's more: think about probabilistic models that work well with time series data
		- HMMs were used in course to decode series of gestures
		- RNNs were used to train time series data
		- both of them used successfully in speech recognition
2. text to text (input reasoning to output)
	- whew! now we need to do the "thinking" part
	- you give many answers to a simple question like "how's the weather?"
	- hard for computer to understand what we want and what we mean
	- NLP requires processing large datasets and overcoming so many challenges
	- smaller problem: get weather report from VUI device that has weather info in response to text request
		- shortcut: map most probable phrases to a `GetWeather` process
		- app would understand most requests that way
		- won't work if request isn't premapped as possible choice
		- BUT effective for limited apps
		- improved over time
3. text to speech
	- last task in pipeline: speech synthesis or TTS
	- train a model to provide probable pronunciation components of spoken words
	- complexity varies lots from monotonic robotic to human voice with realistic inflection and warmth
	- deep learning techniques have yielded some of the most realistic examples

## 3. VUI Applications
- why are VUI apps becoming so common?
	- voice is natural and effortless for humans
	- voice is fast, 3 times faster than typing
	- visual interfaces are distracting, at times too distracting (walking or driving)
- examples of applications
	- voice interfaces in cars: answer calls, give nav commands, receive text and email
	- dictation apps: putting thoughts into words
	- translation apps: speech recognition and speech synthesis plus reasoning logic between them
	- conversational AI: talk with cloud-based systems, an early step towards more general AI
		- home assistant devices here are flexible
		- run search, give weather, interface with other devices
- cloud-based assistant APIs
	- heavy lifting of speech recognition and synthesis already done and turned into APIs
	- new field to come up with any voice-enabled app you can dream up

## 4. Conversational AI with Alexa
- conversational AI expert Jeff Blankenberg
- learn how to build own Alexa skill
- first, how did Echo and Alexa start?
	- inspired by Star Trek computer
	- computer in cloud activated by and responding to your voice
	- trying to get towards how humans do things
	- conversational computing becoming more popular because it's accessible and fun
- what about those old phone interfaces that step you through the menu?
	- those used **Graph-based interaction** because they lacked rich conversational UI of today
		- Graph-based interaction asks pointed questions in prescribed order
		- only accepted specific terms as responses
		- ordered responses, so e.g. can't specify destination until you give your starting location
	- Jeff asks Alexa to open Graph Travel Agent as an example
		- even if give whole travel plan (from, to, on this specific flight)
		- still have to stop and break down origin, then destination
		- even then it needs you to repeat the airline and it will try and find you a flight!
- Instead, **Frame-based interaction** allows user to guide interaction instead
	- say words that make sense to you
	- jump to where you want to be
	- no need to memorize lists of options
	- Jeff asks Alexa to open Frame Travel Agent as an example
		- says wants to fly from A to B on date
		- Alexa responds with complete understood sentence
	- this gives flexibility when designing your own Alexa skills
- how do we know what to have Alexa listen for?
- how does that translate to what we want in response?
- **intents**: VUI allows defining interactions user can perform
	- e.g. DVD player VUI skill: "play", "pause", "stop", "eject" intents
- **utterances**: set of sample statements to help Alexa understand which intent to use when user speaks
	- "play it!", "start the movie!", "go!", "begin!", "showtime!" to help Alexa do "play" intent call
- how to model what user will say
	- one of most common questions when dev building first or second skill
	- ask friends, colleagues, whoever to naturally request the outcome they need
	- what would other people say?
	- every person approaches this differently
	- your skill should accomodate all of them
	- consider weather, with hundreds of ways to ask for it without explicitly saying it
- crowdsource lists of possible utterances

## 5. Lab: Space Geek
- using existing template from Alexa Skills Kit https://developer.amazon.com/alexa-skills-kit
- "space geek" gives a random fact about space when opened
- change the facts data if you ever wish to submit for publishing
- Alexa Skills pipeline
	1. from/to Echo Device or Echosim.io
	2. from/to Speech Recognition - Natural Language Understanding - Text to Speech
	3. from/to AWS Lambda Function to execute Node.js handler code
	4. from/to Alexa Skills Kit voice interaction model (Amazon Developer Portal)
	5. (to only) out to Alexa app "cards"
- your User Interaction Model
	- words and phrases for the skill defined through Amazon Developer Console
	- linked to request/response server function
	- use AWS Lambda function implemented in JS
	- Alexa Service takes care of speech recognition and synthesis
- overview guide to Amazon's steps at https://github.com/alexa/skill-sample-nodejs-fact/:
	1. set up the skill in Amazon Developer Portal
		- create a login
		- add a skill (Name: "My Fact Skill", Invocation Name: "space geek" if using starter Interaction Model)
		- review the Intent Schema
		- review the Sample Utterances
		- save
	2. create skill logic in AWS Lambda
		- create a login
		- create an AWS Lambda Function using blueprint _alexa-skill-kit-sdk-factskill_
		- configure the trigger and function following the skill sample instructions above
		- create the function `myFactSkill`
		- copy ARN number
	3. add lambda function to skill
		- return to configuration of "My Fact Skill"
		- add the ARN number for `myFactSkill`
	4. test skill
		- type "open space geek" in the "My Fact Skill" Service Simulator
		- view the Lambda Request and Lambda Response
		- click "Listen" to hear Alexa's response
	5. customize skill
		- view TODOs in Amazon instructions to make the skill unique
	6. publish your skill
		- first make sure your skill is unique

## 6. Challenges in ASR
- Continuous Speech Recognition's rocky history
	- 1970s DARPA challenge: recognizer for 1000 word vocabulary
	- goal achieved a few years later by CMU Harpy
	- prospects were disappointing and funding stopped
	- "AI winter"
	- 1980s and 1990s showed better models
	- recently fast computers make neural net modeling reality
- why so hard?
	1. audio signal itself
		- noise
		- ASR needs to be able to know how to distinguish signal
		- variability of pitch and volume: whisper, sing, etc.
		- variation between speakers speaking the same word
		- even think of these differences as kind of noise to filter out
	2. variability of word speed
		- align and match same number of letters with different speech rates
		- up to ASR to align sequences correctly
	3. word boundaries
		- spoken words aren't naturally broken by pauses
		- humans understand where word boundaries should be
	4. language knowledge
		- not just using ears
		- using domain knowledge to sort out ambiguity as we listen
		- words that sound alike
		- how context determines meaning
		- "recognize speech" vs "wreck a nice beach" tricky for a computer model
	5. spoken vs written language
		- hesitation, fragments, slips of tongue
		- listener filters
		- if computer only knows newspaper and audiobook language, this is hard to decode
- number of ways to solve these problems, starting at the level of the voice itself

## 7. Phonetics
- study of sound in human speech
- break down words into smallest segments
- **phonemes** define distinct sounds in a language
	- 39-44 in US English
- **graphemes** define smallest distinct written unit
	- 26 letters plus space in US English
- cannot directly map phonemes to graphemes
	- letters map to multiple phonemes
	- phonemes map to multiple letters or letter combinations
- ARPABET developed in 1971 for speech recognition
	- 39 phonemes
	- 15 vowel sounds
	- 24 consonants
	- each is a one or two letter symbol
- phoneme as intermediary between speech and text
	- speech > features > acoustic model > phonemes
	- from there, map phonemes to matching words
	- **lexical decoding** is based on lexicon of the dataset
- why this intermediary step?
	- some systems do just translate acoustic model (features) to words
	- design choice depends on problem dimensionality
	- with large vocabulary of words, translating to phonemes reduces number of comparisons to make overall

## 8. Quiz: Phonetics
- pangrams are sentences containing every letter in the alphabet
- distinguish regular pangrams from phonetic pangrams (ones that contain every phoneme)
- determine which sentences are both grapheme-based and phoneme-based pangrams
- me: I ended up building a separate project ("Pangrammatron") to evaluate both types of pangrams
	- "How vexingly quick daft zebras jump!"
	- "Crazy Fredrick bought many very exquisite opal jewels."
	- "The hungry purple dinosaur ate the kind, zingy fox, the jabbering crab, and the mad whale and started vending and quacking."
	- "The quick brown fox jumped over the lazy dog."
	- "The beige hue on the waters of the loch impressed all, including the French queen, before she heard that symphony again, just as young Arthur wanted."

## 9. Acoustic Models and the Trouble with Time
- feature extraction addressed noise from environment and variability
- phonetics gave model for mapping to sounds in language
- acoustic model maps sound to phonetic representation
- what about matching variable lengths of same word?
	- Dynamic Time Warping algorithm
	- DTW finds similar waves even when time lengths differ
	- DTW can align new words to most similar example in a dictionary
	- HMMs are suited to solving this kind of time sequence patterning as well
	- DNNs actually make the sequencing problem reappear
		- hybrid HMM/DNN approach
		- other ways of solving
	- solve the problem in DNNS with CTC (Connectionist Temporal Classification)
