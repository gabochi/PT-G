# PT-G
Markovian bash text generator.

## Separating all words

We'll use a dataset of Cioran quotes...

```bash
grep -Eo "[^ ]+ " dataset.txt
```

With grep you can filter texts using REGEX. In this case, we use extended REGEX (-E) and print only matches (-o).
```"[^ ]+ "``` Selects a group `[]+` of any characters excluding space `^ ` followed by a space `" "`.
The result is a word per line, *including punctuation*:

```
It
is 
not 
worth 
the 
bother 
of 
killing 
yourself,
...
```

## Counting occurrences

```bash
grep -Eo "[^ ]+ " dataset.txt | sort | uniq -c
```

We'll pipe the output to a `sort`:

```
- 
– 
— 
a 
a 
a 
...
your 
your 
yourself 
yourself 
yourself, 
zoologist 
```

This way, all occurrences will be together and `uniq` command fill filter repetitions and, also, count them `-c`.

We will not use the number of occurrences BUT if we pick a random word from a set which includes all repetitions, the probabilities of each word will depend on it's occurrences. **We're working with probabilities without doing any math!**

## Pick a random word

Take a look at this command:

```bash
grep -Eo "[^ ]+ " dataset.txt | shuf
```

We just shuffled all lines!

```
world, 
we 
goal! 
—That's 
living,
...
```

Let's pick just the first one with `head`:

```bash
grep -Eo "[^ ]+ " dataset.txt | head -n 1
```

## First generator

So far, we're ready to make the cheapest IA:

```bash
grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 3 # pick three random words
```

```
understand 
What 
not
```

And put them together in a "sentence":

```bash
grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 3 | tr -d "\n" # remove new lines
```

```
absolutely their nothing;
```

---

## First improove: where to stop?

That was nice but sentences can have more than three words and usualy end with a period. Let's look for endings in the dataset:

```bash
grep -Eo "[^ ]\." dataset.txt # Show groups of characters excluding spaces followed by a period
```
*The period is a REGEX reserved character, so we must escape it to match a litteral*

```
late.
postponed.
optimists.
dreams.
myself.
...
```

Great! Run this oneliner and see what happens:

```bash
while true; do WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1); echo -n "$WORD"; [ "$(echo "$WORD" | grep "\." )" != "" ] && break; done
```

```
and on a to when lives in metaphysically only and your roots.
```

It generates a text of random lenght because loops until a word with a period is found.

Here is the same code but clearer:

```bash
while true                                                   # starts a loop
do 
    WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1) # pick a random word
    echo -n "$WORD"                                          # print the word, no new line
    [ "$(echo "$WORD" | grep "\." )" != "" ] && break        # break the loop if there's a period in the word
done
```

---

## Second improove: How to start?

Now that we have an ending, we need a proper start. We can see all words starting with capital letters:

```bash
grep -Eo "[A-Z][^ ]+ " dataset.txt
```

```
It 
Only 
The 
Wouldn’t 
Then 
...
```

Incorporate this to the previous script:

```bash
echo -n "$(grep -Eo "[A-Z][^ ]+ " dataset.txt | shuf | head -n 1)" # start with a word with capital letter

while true                                                         # loop
do 
    WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1)       # pick random word
    echo -n "$WORD"                                                # print it, no new line
    [ "$(echo "$WORD" | grep "\." )" != "" ] && break              # break if it has a period
done
```

---

## The third is the winning: Better generation

We can do better than this. What if we filter words that follows a particular word:

```bash
grep -Eo "you [^ ]+ " dataset.txt #| sed "s/you //g"
```
*Uncomment the `sed` instruction to remove the first word*

```
you always 
you do 
you have 
you are 
you would 
you achieve 
you cry 
you are
```

### Markov chain
A Markov chain is a sequence of possible events that depend only on the previous state. If we could pick a random word but in the set of words that follow the last word, sentences will make a little more "sence".

Let's include all we learned in a final script:

```bash
#!/bin/bash

############################
# Markovian Text Generator #
############################

# Select init word with capital letter
WORD="$(grep -Eo "[A-Z][^ ]+ " dataset.txt | shuf | head -n 1)"
echo -n "$WORD"

# Generation loop

while true 
do 
	# Select next word in the set of words that follow previous word
	WORD=$(grep -Eo "$WORD[^ ]+ " dataset.txt | sed 's/'"$WORD"'//g' | shuf | head -n 1)

	# If no match, break	
	[ "$WORD" == "" ] && break    

	# Print word	
	echo -n "$WORD"

	# If string ends with period, break    
	[ "$(echo "$WORD" | grep "\." )" != "" ] && break
done

# New line
echo
```

## Final thoughts

I'll leave you with a final thought from out markovian Cioran:

`I'd like someone who observed gorillas in philosophical systems, fight for novelty is all too weak for weaknesses in everyday, in philosophical systems, fight for moral and dreams.`
