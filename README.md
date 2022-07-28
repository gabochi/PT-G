# PT-G
Bash text generator.

## Separating all words

We'll use a dataset of Cioran quotes...

```bash
grep -Eo "[^ ]+ " dataset.txt
```

With grep you can filter texts using REGEX. In this case, we use extended REGEX (-E) and print only matches (-o).
```"[^ ]+ "``` Selects a group `[]+` of any characters excluding space `^ ` followed by a space ` "`.
The result is all words (including their punctuation). 

## Counting occurrences

`grep -Eo "[^ ]+ " dataset.txt | sort | uniq -c`

`grep -Eo "[^ ]+ " dataset.txt | head -n 1`

`grep -Eo "[^ ]+ " dataset.txt | shuf`

`grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 3`

`grep -Eo "[^ ]+ " dataset.txt | shuf | head -n 3 | tr -d "\n"`

`grep -Eo "[^ ]+ " dataset.txt | shuf | tr -d "\n"`

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
