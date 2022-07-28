# PT-G
Bash text generator.

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
your 
yourself 
yourself 
yourself, 
zoologist 
```

This way, all occurrences will be together and `uniq` command fill filter repetitions and, also, count them `-c`.

## Pick a random word

Take a look at this command:

```
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
no be confessions
```

---

`grep "\." dataset.txt`

`while true; do WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1); echo -n "$WORD"; [ "$(echo "$WORD" | grep "\." )" != "" ] && break; done`

```bash
while true 
do 
    WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1)
    echo -n "$WORD"
    [ "$(echo "$WORD" | grep "\." )" != "" ] && break
done
```

`grep -Eo "[A-Z][^ ]+ " dataset.txt`

```bash
echo -n "$(grep -Eo "[A-Z][^ ]+ " dataset.txt | shuf | head -n 1)"
while true 
do 
    WORD=$(grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 1)
    echo -n "$WORD"
    [ "$(echo "$WORD" | grep "\." )" != "" ] && break
done
```

`grep -Eo "you [^ ]+ " dataset.txt | sed "s/you //g"`

```bash
#!/bin/bash

##########################
# Better PText Generator #
##########################

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
