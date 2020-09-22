    Report By: MD Sami Uddin

    CS625-HW2

    22nd September 2020

# Part One: Cleaning

## Step 1: Clean "What kind of pet is this"

Initially, there are a total of 69 choices, checked with *Text Facet*. The list contains many duplicates and misspellings that needs to be fixed.

First, we do the following

    Edit Cell -> Cluster and Edit

And got this
![Picture 1](/screenShots/col-1_screenshot-1.png)

Merged with the values showed under the *New Cell*. Just used TitleCase while using *two-word-strings*. E.g. "Guinea Pig".

1635 cells are fixed after that and choices reduced to 60. Now I'll investigate on other 1783 - 1635 = 148 entries.

The idea of *Metaphone3* that similar sounding words will end up sharing the same key and thus being binned in the same cluster. After using **Method: key collision** & **Keying Function: metaphone3** I got these clusters
![Picture 2](/screenShots/col-1_screenshot-2.png)
and change it to these
![Picture 3](/screenShots/col-1_screenshot-3.png)
> Thoughts:
>
>>  (a) Remove plural forms
>>
>>  (b) Decided Ca be Cat. So I will change That one manually.
After the latest clustering and manually changing *Ca* and *Car* to *Cat*, now I am left with 51 choices.

### Some manual edits [part 1] (NB. Each of them are different individual cell)

> *Dog, dog, dog, cat* to *Dog*

Thoughts: I checked the breeds in Google and got confused. As the dog is mentioned thrice in the name, I went with Dog!

> *dlg* to *Dog*

Thoughts: Checked the breed and turns out to be a dog. As only one occurrence, manual edit did not hurt.

> *Card Board Poster* - Deleted

Thoughts: Pet's name is *Kendall Jenner* and the breed is *Human*. I believe it's a prank data and deleted it.

> *Server* - Deleted

Thoughts: I believe it is a prank too and deleted it. CPU can't be a pet.

> *Luna* - Deleted

Thoughts: There's no pet with this name or breed name.

> *Roomba* - Deleted

Thoughts: It's a robot, not a pet!

> Made an empty cell to *Dog* | Deleted another

Thoughts: Checked with breed *Golden Retriever*. Deleted other row for lack of data

> *Virus* to *Other*

Thoughts: Didn't find any clue from breed due to no data.

> *Spiney leaf insect* to *Spiney Leaf Insect*

> *Pit bull* to *Pit Bull* 

Thoughts: Standardizing the same practice of naming. Should have used GREL but didn't click the idea that time.

> *Sog* to *Dog*

Thoughts: Checked the breeds and made the changes for all *Sog*

> *Puppy* to *Dog*

Thoughts: Puppy is a dog.

> *Phoebe* to *Pit Bull*

> *Mona* to *Dog*

Thoughts: Checked the breeds.

> *Kitten* & *Kitty Meow* to *Cat*

> *Indoor goldfish* to *GoldFish*

> *Chinchilla (Other)* to *Chinchilla*

> *ptifish* to *ptifish*. This had unusual character.

Up to now, there are 34 choices. Big win!

### Again check with clustering

The Nearest Neighbor methods (also known as kNN) provide a parameter (the radius, or k) which represents a distance threshold: any pair of strings that is closer than a certain value will be binned together. Now using nearest neighbor with ppm where Radius is 8.0 and Block Chars is 5. ppm is widely applied in string comparison. And as I am dealing for *Other* I choose 5 as Block Char.

![Picture 4](/screenShots/col-1_screenshot-4.png)

Made them all as *Other*.


Reduced the choice to 31!

### Some manual edits [part 2]

> *Leopard Gecko* to *Gecko*


**Finally 30 choices.** I could have reduced it more. I could make Pit Bull to Dog or Goldfish to Fish, but I decided to keep it as it is.


## Step 2: Clean "Pet's Full Name"

People can be very creative with the names, especially pet names. I have skimmed through the names using text facets and clustering and made few manual changes that I found that can't be a name. Also handled cases that involved characters like *()-/,* inside names with regex. To be honest, I was very lenient while fixing the names.

Used a common transformation *titleCase* often after the edits.

### Fixed with clustering
Cluster *keying function: metaphone3*

> Junebug to June bug

### Some manual edits [part 1] 

These require manual attention as they do not fall into patterns

> *Mephistopheles, Der Leibhaftige Teufel, Lord Of The Underworld, Bringer Of Darkness* to *Felix*

> *Mr Doctor Professor Constable Detective Brick* to *Brick*

> *Vitale Male The Hot Tamale Who Has A Cooking Show With Mario Batale* to *Vitale*

> *Walker Texas Ranger* to *Willy*

> *Xena Badass Mcawesome Princess Flufflepuffs Of The Ashen Fields Of Mordor* to *Xena*

> *Alex’s Fiesty Girl Bailey Balogh* to *Bailey Balogh*

> *Baldrick Of Blackadder Bob* to *Baldrick Bob*

> *Beauregard Montgomery Cat* to *Beauregard Montgomery*. Changing lengthy names after looking up the everyday name.

### Fixed with regex
    
**Issue No 1:** Names with number 
    
    Solution: Filter out numbers from text
```python
import re 
m = re.search(r"((\D+)\ ?(\D+?))", value) 
return m.group(1)
```

Thoughts: 7 cells got fixed. "\D" essentially avoids any number in the string.

**Issue No 2:** Words inside () 
    
    Solution: Remove everything inside and including ()

```python
import re 
removeableSubString = re.search(r"\(.*?\)", value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

Thoughts: Wrote regex to match everything inside *()*. Then replaced the string from the original value and returned the remaining.

**Issue No 3:** Names that have - in it

    Solution: Remove everything on the right side, including -
    eg. sky-sky will be sky. Myles - His Roman Name Is Squirtacus Mylonius will be just Myles.

```python
import re 
removeableSubString = re.search(r"-.*", value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

**Issue No 4:** Names that have , in it

    Solution: Remove everything after the comma
    
    eg. Wyatt Daniel, The Wild Corgo will be Wyatt Daniel

```python
import re 
removeableSubString = re.search(r'(,\ ?((\w+)?\ ?(\w+)?)*)', value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

Thoughts: Finding the content after the comma and Replacing  that from the original

**Issue No 5:** Names that are black

    Solution: Changed them with last-name via manual edits.

**Issue No 6:** Names that have ; in it
    Solution: Some gave multiple pet names using ; inside the name
    eg. Bella Nina; Thomas James; Archie

    To clean it, I am keeping only one.
 
```python
import re 
removeableSubString = re.search(r'(;\ ?((\w+)?\ ?(\w+)?;?\ ?)*)', value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

**Issue No 7:** Names that haves "" in it
    
    Solution: Remove everything inside and including ""
    
    eg. Phillipe Robert Jean Luc Tugboat “toot! Toot!” will become Phillipe Robert Jean Luc Tugboat

```python
import re 
removeableSubString = re.search(r'((\w+)\ ?(\w+?))+\ ?', value) 
return removeableSubString.group(0)
```

Thoughts: This takes the text and will keep the string up to ". This will also clear up any texts that have any sort of special characters that I have missed to notice and keep the string up to that character.

## Step 3: Clean "Pet's Everyday Name"

As these columns are also names, I followed what I did with the full names and edited this leniently.

Made common transformation *titlecase* before starting to work on this column. My target is to keep the *everyday name* as one worded name.

### Fixed manual edits [Part 1]

> *B or B-Dog* to *Baillie* to make more sense

> *Petey Poopoo Head* to *Petey*

> *Jakey Poo Poo Head* to *Jakey*

### Fixed with clusters

Fixed names that came using *Keying Function: fingerprint* like this:
![Picture](/screenShots/col-3_screenshot-1.png)

Using *ngram* with *size:2*
![Picture](/screenShots/col-3_screenshot-2.png)

Made several changes here  using *Levenshtein* with *Radius:3 and Block Char:6* and made one-word names from multiple word names:
![Picture](/screenShots/col-3_screenshot-3.png)

Did some more using *Levenshtein* with *Radius:6 and Block Char:6*

*ppm Radius:1.0 and Block Char:4*
![Picture](/screenShots/col-3_screenshot-4.png)

*ppm Radius:2.0 and Block Char:4*
![Picture](/screenShots/col-3_screenshot-5.png)

*ppm Radius:3.0 and Block Char:4*
![Picture](/screenShots/col-3_screenshot-5.png)

*ppm Radius:4.0 and Block Char:6*
![Picture](/screenShots/col-3_screenshot-5.png)

Tried other combinations and tried to fix the name into one word. For those, which I could not fix with clustering, I shall use regex.

> Here are some examples that I shall fix with regex and make it one word, found them while skimming through the names during clustering
>
>Case:
>
>> 1. Mr. Mosley ; Mr. Peaf ; Mr. Baby Dwayne, Dwaniel, Bub.
>>
>> 2. Abi (Abby)
>>
>> 3. Algy, Gu
>>
>> 4. Baby / Sushi Burger
>>
>> 5. Sammich, Dumbledork  ; Louie, Loulou, Kahula
>>
>> 6. Fluffernutter
>>
>> 7. Emma-zoo  ; Chicky-chix
>>
>> 8. Sian Or Sisi
>>
>> 9. Peenie Paneenie ; Buffy And/or Buffalo ; The Commander

### Fixed with regex

Type 1: Remove Mr. from names. Will deal with other issues on later types.

```python
import re 
removeableSubString = re.search(r'Mr.\ ?', value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

Type 2: Remove parentheses and texts inside parentheses

```python
import re 
removeableSubString = re.search(r'\(.*?\)', value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

Type 3-9: Take the only first word from the texts as the name. This will fix any other cases that I have missed.

```python
import re 
matchedString = re.search(r'(\w+)', value) 
return matchedString.group(0)
```


There are 8 blank cells.
I will just take the Full names to fill up the everyday names

```
if(value == null, cells["Pet\'s Full Name (you don\'t have to include your last name. Think \"Winston Churchill\" and not \"Winston Churchill MYLASTNAME\")"].value,value)
```
Fortunately, all of them are worded names.

## Step 4: Clean "Pet's age"

We assume the age in years and only work on *Non-numeric values*. Initially, there are 277 non-numeric values that we will work on. Made the cells into a number before starting.

First, edit the cells with years.

    Edit Cells -> Trasform -> value.replace(" years","")

Used text facet and chose *Non-numeric*. Convert all values to lower-case text.

Then edit the cell with months and convert them into the year. I used python for this task as I was struggling with GREL syntax.
`return round(float(value.replace(" months","")) / 12, 2)`

Now I have got 168 cells to edit. I shall convert the weeks to year now. Considering 1 year = 52 week roughly-
`return round(float(value.replace(" weeks","")) / 52, 2)`

10 more cells are fixed. Now I have to investigate what is the issues with the other 158 cells.

Remove *?* and *ish* from the values: 
`value.replace("?","").replace("ish","")`

Left with 157 values with 113 different choices. I'll check with clusters now.

Using *Method: key collision* and *Keying Function: fingerprint* I have got this-
![Picture 1 Pet Age](/screenShots/col-4_Age_screenshot-1.png)
I have decided to put 7 for the *dead* values and rounding up other ages.

Using *Keying Function: ngram* with size = 2:
![Picture 2 Pet Age](/screenShots/col-4_Age_screenshot-2.png)
Changed 6 months to 0.5 year.

Which came up with this:
![Picture 3 Pet Age](/screenShots/col-4_Age_screenshot-3.png)

Used common transform to the number so that newly converted numbers can be considered as *Numeric*

Got several patterns using *Keying Function: metaphone3*
![Picture 4 Pet Age](/screenShots/col-4_Age_screenshot-4.png)
Will fix them using text-transform.

Replacing several tail and head texts at once:
`value.replace(" old","").replace(" yesrs","").replace(" 1/2","").replace(" ½","").replace("almost ","").replace(" (est.)","").replace(" year","")`

Found some more patterns using *Beiden-Morse*. Considering *deceased* as 12 years(just my decision).
![Picture 5 Pet Age](/screenShots/col-4_Age_screenshot-5.png)

### Some manual edits [part 1]

I could not think of GREL code for editing these, so I choose to edit them manually.

> two to 2

> six to 6

> seven to 7

> eight to 8

> 10monthd to 10 months

> unknown to 5 (assuming random)

Thoughts: will be easy to convert later on batch

> *died in 2008 at 12* to *12*

Thoughts: It will mess my regex logic

Replacing few more:

`value.replace("about ","").replace(" yo","").replace("y","").replace("yrs","")`

Replacing remaining months and mos:

`return round(float(value.replace(" mos","")) / 12, 2)`

There still exist 49 *non-numeric* values. I will just scan the cells and pick up the first number from them. If no number exists the cell will be blank. It'll not be a perfect solution but it's not practical to edit them manually one by one.

```python
import re 
m = re.search(r"((\d\d?)\.?(\d\d?)?)", value) 
return m.group(1)
```
Basically, I am searching for numbers that can be 1 or 2 digits that may also have decimal values up to two-digit. And then taking the first match.

There are 6 choices left.
![Picture 6 Pet Age](/screenShots/col-4_Age_screenshot-6.png)
Made the chages as following
![Picture 7 Pet Age](/screenShots/col-4_Age_screenshot-7.png)

Handling blank cells, making it 1 just to give a value.
```
if(or(value == '',value == null),1,value)
```

## Step 5: Clean "Pet's breed"

### Fixed with clustering

Googled and checked the validity before merging.

Using *key collision* and *fingerprint*:
![Picture 1 breed](/screenShots/col-5_screenshot-1.png)

Using *key collision* and *ngram with size:2*:
![Picture 2 breed](/screenShots/col-5_screenshot-2.png)

Using *nearest neighbor* and *levenshtein with Radius:1.0 & block-char:6*:
![Picture 3 breed](/screenShots/col-5_screenshot-3.png)

Using *nearest neighbor* and *levenshtein with Radius:3.0 & block-char:6*:
![Picture 4 breed](/screenShots/col-5_screenshot-4.png)

Using *nearest neighbor* and *levenshtein with Radius:4.0 & block-char:6*:
![Picture 5 breed](/screenShots/col-5_screenshot-5.png)

Using *nearest neighbor* and *ppm with Radius:2.0 & block-char:6*:
![Picture 6 breed](/screenShots/col-5_screenshot-6.png)
#### Fixed with manual edits [part 1]

These are the unusual breed names that I choose to edit manually. I found them while skimming through the list for while using clustering.

> *A Tabby And A Grey Cat* to *American Shorthair*

> Gr to Golden Retriever

> *Smol Terrier* to *Small Terrier*

> *Border Collie/pit Mix* to *Border Collie Pit Mix*

> *Great Pyr* to *Great Pyrenees*

> *Great Pyrenees/lab* to *Lab Great Pyrenees*

> *Chihuahua-lab* to *Labrador Chihuahua*

> *Unknown Unknown Bali Dog Mix And Tabby Cat* to *Unknown*

> *Yes* to *Unknown*. I really don't know what resulted yes.

> *Carin TerrierKerry Blue TerrierKeeshond* to *Cairn Terrier Kerry Blue*

> *Mutt: Half Terrific(most Likely Golden Retriever German Shepherd* to *German Shepherd*

> *8 Years* to *Unknown*

> *A Mess* to *Unknown*

> Labrador & Great Pieraneese to Labrador

> *Terrier Something Or Other* to *Terrier*

> *Beta* to *Betta*

> *Beagle Jack Russell Cow ;)* to *Beagle Jack Russel*

> *Border CollieChinese Sharpei* to *Border Collie Chinese Shar Pei*

> There Hermit Crab with breed *Australian*. As I don't know, changed it to *Unknown*

> A cat with *Fuzzball*, changed it to *Unknown*

> *Dmh* to *Domestic Medium Hair*

> *Dsh* to *Domestic Short Hair*

> *Paint* to *Unknown*

> *Spinx* to *Sphynx*

> *Jrt Miniature Pincher* to *Jack Russell Miniature Pinscher*

> *Who Knows* *Unknown*

> Deleted 3 rows that has breed as *Heinz*. Those data appeared fishy to me.

There are some breeds with *Lab*, *Orange* and *Mix*. I kept them as it is without converting them to *Unknown*

Also made changes to those cells that has '/' & '-' inside the text. Just not mentioning here as I didn't change any major thing rather then a slash.
```
value.replace('/',' ')

value.replace('-',' ')
```

Changed *N A* and *None* to *N/a*
```
value.replace('N A','N/a').replace('None','N/a')
```

261 cells are blank. It'd be great if I would go through each cell and try to come up with something with google. However, I'm putting **Unknown** as value to them.

```
if(value == null,'Unknown',value)
```

Made sure there's no double space in between the texts:
```
value.replace('  ','')
```

Removed ? from the texts:
```
value.replace('?','')
```

Fixing any miss-spelling of Shepherd as well as Shih Tzu:
```
value.replace('Shepard','Shepherd')

value.replace('Shi Tzu','Shih Tzu')
```

Make Pitbull to Pit Bull:
```
value.replace('Pitbull','Pit Bull')
```

Change Guineapig to Guinea Pig to make the text standard across the column. Also for Longhair/Shorthair/Mediumhair:
```
value.replace('Guineapig','Guinea Pig')

value.replace('Shorthair','Short Hair')
value.replace('Mediumhair','Medium Hair')
value.replace('Longhair','Long Hair')
```

Some texts were like expressing opinions or guesses of breeding in full sentences. I have removed them and only kept the breeding name. Also, some have no hints of the breeding name in the text like this(*Cat's A Cat, We Found Him On My Dad's Tire... I Think You Can Guess What Brand Tires My Dad Had*). So I put them as *Unknown*. I have changed many of them and did not mention here some.

### Fixed with regex
1. Fixing breeds with (). I have done that already for other columns.

```python
import re 
removeableSubString = re.search(r"\(.*?\)", value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

2. Again removing everything after ,

```python
import re 
removeableSubString = re.search(r'(,\ ?((\w+)?\ ?(\w+)?)*)', value) 
modifyValue = value.replace(removeableSubString.group(0),'')
return modifyValue
```

### Cluster again:

Using *key collision* and *fingerprint* to merge anything with the same text value:
![Picture 7 breed](/screenShots/col-5_screenshot-7.png)

Fixed more breeds and merged similar texts using *metaphone3*
![Picture 8 breed](/screenShots/col-5_screenshot-8.png)

Checked with google and fixed some more using *cologne-phonetic*. The idea of *cologne-phonetic* is that similar sounding words will end up sharing the same key and thus being binned in the same cluster.
![Picture 9 breed](/screenShots/col-5_screenshot-9.png)

# Part 2: Question/Answer

1. How many types (kinds) of pets are there?

    Used text facet on *what kind* column and found 30 types.

2. How many dogs?

    After using text facet on *what kind* column, I chose *Dog* from the list. Thus it showed me the number of rows with dogs and there are 1122 dogs among all pets.

3. How many breeds of dogs?

    First I chose Dog from the text facet of *What kind of pet* column. Then chose the text facet of the *Pet's Breed* column from the resulting table. It gives me 335 choices.

4. What's the most popular dog breed?

    By selecting *count* I found that **Golden Retriever** is the most popular dog breed.

5. What's the age range of the dogs?

    I have converted weeks and months to years. Using sorting I found the range of dogs lies in (0.13 - 22) years. 0.13 is the result of conversion to years.

6. What's the age range of the guinea pigs?

    I found 8 Guinea Pigs. The age range is 1-5 years.

7. What is the oldest pet?

    Using the reverse sort on Pet's age, I found the oldest pet's age is 24. Interestingly that's a cat!

8. Which are more popular, betta fish or goldfish? How many of each?

    After cleaning the dataset I have found 6 Goldfishes and 2 Betta Fish. So I can conclude that Goldfish is certainly more popular.

9. What's the most popular everyday name for a cat?

    After using text-facet on *What kind of pet* and choosing Cat, I used text-facet on *Pet's everyday name*. Using count I found that **Kitty** is the most popular everyday name for a cat.

10. What's the most popular full name for a dog?

    Followed the procedure the same as Q9 and found that **Maggie** is the most popular full name for a dog. You may expect a longer full name. But since I ran a rigorous cleaning on special characters like (),;"" that might cause the shortened value for the names.

# Reference

- GREL Doc: https://github.com/OpenRefine/OpenRefine/wiki/General-Refine-Expression-Language

- Online Regex: https://regex101.com/
- Regex tutorial: https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285
- Regex tutorial that I found very helpful on youtube: https://www.youtube.com/watch?v=rhzKDrUiJVk
- Python and regex: https://docs.python.org/3/library/re.html#re-objects
- More on Cluster and Edit: https://github.com/OpenRefine/OpenRefine/wiki/Clustering-In-Depth
- Python convert data type: https://www.digitalocean.com/community/tutorials/how-to-convert-data-types-in-python-3