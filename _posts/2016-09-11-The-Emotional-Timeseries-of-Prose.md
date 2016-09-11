---
title: The Emotional Timeseries of Prose
layout: post
date: 2016-09-11 10:35:00 +GMT
author: Sudeep Agarwal
---

Nearly twenty years ago, Kurt Vonnengut, an American author perhaps most famously known for his satirical novel Slaughterhouse-Five, gave a lecture that would change the way we think about stories. Standing in front of a blackboard, chalk in hand, he proclaims, "There's no reason why the simple shapes of stories can't be fed into computers; they are beautiful shapes." He then proceeds to plot a cosine curve, and amidst applause and laughter, playfully declares, "People love this story!".

### Those Who Tell the Stories Rule the World

The notion Vonnengut explores is an interesting one - can we quantitatively look at writing to understand how it is emotionally structured? When we read, we feel emotionally connected to the writing. We get so 'lost' in the fictional world and fall so deep into it that our own emotions become mapped to the narrative. In fact, narrative transportation theory in psychology studies exactly this. The quantitative meta-analysis by Van Laer, De Ruyter, Visconti and Wetzels on the effects of narrative transportation allude to readers 'mentally enter(ing) a world that a story evokes'. We feel what we read, and being able to understand how these emotions vary over the course of a story is an important and very interesting intellectual pursuit. 

More importantly, this discussion leads to some interesting questions that we can now address through data analysis of big datasets. How does this emotional structure vary over generations of writing, from early 16th century Shakespeare to modern day Pratchett? How do these trends differ between cultures - how similar or different is Indian and Japanese literature in its emotional structure? Do certain authors have an emotional *signature* - a unique structure to their stories, a formula to their writing? Given an emotional structure, can we predict what kind of story it is (or even better - can we predict it's ending?)

Many of these questions were inspired by the research of the Andrew Reagan and the Computatational Story Lab at the University of Vermont, where they used sentiment analysis to analyze the 'emotional arcs' of 1,700 stories to reveal the most common ones. Their findings are fascinating - according to the research, all stories conform to one of six basic emotional arcs. 

![arcs](https://cloud.githubusercontent.com/assets/11940172/18407707/db6cd24a-76e6-11e6-930c-f75eadf7b059.png)

As an avid reader, this research really fascinates me. In this multi-part blog series, I will try to understand the concept of an emotional timeseries in a piece of literature, and how it is affected by various factors. In future posts, I may attempt to address some of the more interesting questions brought up earlier. 

### Breaking Down Emotional Structure

To fully understand the emotional structure of a piece of text, we need to break it down. We need to analyze the sentiment of words in the text, their relationships with the words around them, and the overall shape of this relationship through the course of the text. Let's define a few things.

// Todo
A *sentiment score* describes how positive or negative the word is.

An *emotional timeseries* describes the sentiment score of a text plotted against time. Time, in this sense of the definition, is the current position of linear progression into the text. 

Now that these terms are defined, let's move on to the methodology.

#### Gathering the Textual Data
I decided to analyze text from Project Gutenberg, a public repository of over 50,000 ebooks, due to easy of access and quality of data. Specifically, I made use of a manually cleaned corpus (with metadata, license information, transcribers' notes, etc removed) with 3,036 books by 142 authors provided [here](http://web.eecs.umich.edu/~lahiri/gutenberg_dataset.html) by Lahiri from University of Michigan.

#### Retrieving the Sentiment Information
I used [LabMT](http://journals.plos.org/plosone/article/asset?unique&id=info:doi/10.1371/journal.pone.0026752.s001), a scored word list dataset for language assessment and sentiment analysis, to retrive the *average happiness* of a word. The dataset, which has 10,222 entries, also has other information for each word like happiness rank, happiness standard deviation, Twitter rank, Google rank, NYT rank and lyrics rank. This is what it looks like.

| word  | happiness_rank | happiness_average | happiness_standard_deviation | twitter_rank | google_rank | nyt_rank | lyrics_rank |
|-------|----------------|-------------------|------------------------------|--------------|-------------|----------|-------------|
| happy | 4              | 8.30              | 0.9949                       | 65           | 1372        | 1313     | 375         |

We can easily load all of this information by reading it into a `pandas` dataframe for later reference and converting the average happiness column it to a dictionary for quickly looking it up when we're building our scoring. 

```python
import pandas as pd

url = 'http://www.plosone.org/article/fetchSingleRepresentation.action?uri=info:doi/10.1371/journal.pone.0026752.s001'
labmt = pd.read_csv(url, skiprows=2, sep='\t', index_col=0)
labmt_dict = labmt.happiness_average.to_dict()
```

#### Creating the Emotional Timeseries
The methodology for creating the sentiment score was inspired by Reagan's paper. A sliding window approach is used to obtain the emotional content of 10,000 word windows. The text is broken up into segments of uniform length. This length is appropriately calculated to generate a hundred points of data by sliding the window across the text, incrementing by the segment length in every iteration. 

![slidingwindow](https://cloud.githubusercontent.com/assets/11940172/18415905/bd8f0ae6-77d0-11e6-8380-793ffa8088f9.png)

To obtain the appropriately segmented blocks of text, we first read in the text file, clean it up a bit and split it into a list of words.

```python
import re

with open('Lord Of the Flies.txt', 'r') as f:
	text = re.sub('[^a-z\ \']+', " ",  f.read().lower())
	words = list(text.split())
```
We can then identify an appropriate segment length (`Ns`) that will give us a hundred data points for the timeseries.

```python
N = len(words)
Nw = 10000 # window size
n = 100 # no. of data points
Ns = int((N - (Nw + 1)) / n) # size of each segment
```
This segment length allows us to create our sliding window that iterates across segments. In this process, we also calculate the average happiness score of each 10,000 word window by taking into account both the happiness scores of the individual words as indicated by the LabMT dataset, as well as their normalized frequencies within the word window. 

![formula](https://cloud.githubusercontent.com/assets/11940172/18415976/5c8348a4-77d3-11e6-8033-e1fc15d89e10.png)

This can be represented by the function above, where T is the text being analyzed, N is the total number of words in the text. The average happiness score of each word is obtained from the LabMT data, and is multiplied with the frequency of the word in the text, normalized over the sum of all its occurences. This measures the text's overall *average happiness*, also known as *valence*. 

With this knowledge, we can define a scoring function that takes in a list of words and returns the average sentiment ( the data point in the timeseries) for each window. 

```python
from nltk.corpus import stopwords
from nltk.probability import FreqDist

def score(words):
    total_freq = 0.0
    ave_sentiment = 0.0
    filtered_words = [word for word in words if word not in stopwords.words('english')]
    freq = FreqDist(filtered_words)
    total_freq = sum([freq[word] for word in filtered_words])
    for word in words:
    	sentiment_score = labmt_dict.get(word, 0)
      	norm_freq = freq[word] / total_freq
      	ave_sentiment += sentiment_score * norm_freq
    return ave_sentiment
```

We first analyze the set of words to remove any *stopwords* that might skew our results. Stopwords are words that are commonly removed from input terms when processing natural language data as they do not contain important significance to the text. Some examples are *the*, *and*, *me*, etc. NLTK, a great library for natural language processing in Python, has a handy collection of common stopwords.

We can then create a frequency distribution of the filtered words to calculate the normalized frequence, or the frequency of the word as a fraction of its total frequency within the input text. By summing up the products of the normalized frequency and the sentiment score that we retrieve from the `labmt_dict` for each word, we can find the average sentiment.

With the sliding windows and average happiness scores, we can construct an emotional timeseries that describes the average sentiment of the book as it progresses. 

```python
timeseries = []
for idx, i in enumerate(range(Nw, N, Ns)):
       window = words[i-Nw:i]
       timeseries.append(score(window))
       
timeseries = np.array(timeseries)
relative_ts = timeseries - timeseries.mean()
```
By subtracting the mean of the timeseries, we can find the progression of relative sentiment scores and we can understand more clearly how the different sections of the text relate positively or negatively to each other.

Let's see how this looks like when we plot it for a very popular book - Lord of the Flies.

### Lord of the Flies
#### A Case Study

When we plot the raw average sentiment scores, we get something like this. 

<iframe width="900" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/144.embed"></iframe>

While we can see some changes in sentiment over the course of story, perhaps this approach is not the most effective way to describe the variation in sentiment. What if we plotted the relatiive sentiment scores by subtracting the mean, as mentioned earlier?

<iframe width="900" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/130.embed"></iframe>

This looks much more promising. We can now see that there are certain well-defined regions in the book that are positive and are above average, and others that are negative and below average. This leads to another interesting question - can we break apart the story by chapters, analyze the key events in each chapter, and correlate that with the average sentiment? Let's do exactly that.

To find the start and end positions of each chapter, I search through the text for occurrences of *Chapter One*, *Chapter Two* and so on, and mark the boundaries for each chapter. 

```python
from num2words import num2words
import itertools

def get_chapter_idxs(words):
	chapters = []
	numwords = [num2words(i) for i in range(1,100)]
	for i, word in enumerate(words):
		if words[i] == 'chapter' and words[i+1] in numwords :
		    chapters.append(i+2)
	chapters.append(len(words))
	a, b = itertools.tee(chapters)
	next(b, None)
	return zip(a, b)
```

`num2words` allows me to put this logic into a loop and convert the numbers to equivalent words. With `itertools` and `zip`, I generate a list of `(start, end)` tuples that demarcate the boundaries of each chapter. Let's use this information to mark out the sections in the earlier plot and label them accordingly.

<iframe width="1000" height="600" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/126.embed"></iframe>

I then created a summary of key events in each chapter of the book. **SPOILERS** follow if you have not read Lord of the Flies (read it, and come back to this post again!)

**Chapter 1:** Plane gets shot down. The boys elect a chief. They attempt to establish order.
**Chapter 2:** The boys call an assembly. Ralph decides to make a fire. Piggy and Jack have an argument.
**Chapter 3:** Jack starts to become savage. Some of the boys work on huts, the others play.
**Chapter 4:** The littluns are bullied. Jack and Piggy fight. 
**Chapter 5:** Ralph tries to set things in order. The boys discuss the beast. 
**Chapter 6:** Plane gets shot down and the boys encounter a dead parachutist. 
**Chapter 7:** The boys act out a pig hunt. They continue hunting for the beast. They encouter the parachutist.
**Chapter 8:** Jack insults Ralph and asks him to leave. Simon has a conversation with the pig's head.
**Chapter 9:** A storm is brewing. Simon is killed.
**Chapter 10:** Jack and his tribe are transformed into savages. They steal Piggy's glasses. 
**Chapter 11:** Piggy confronts Jack. The conch gets smashed. Piggy is killed.
**Chapter 12:** Ralph is hunted. He runs till he finds a naval officer who rescues them.

As you can see, the comparision works *pretty* well. Especially so when you consider the fact that all we're analyzing here is sentiment and effectively just the choice of words in the writing. We see peaks at events like the first creation of the fire, and the acting out of the pig hunt which brings the boys closer together. The troughs are at places where we'd expect, such as when key characters like Simon and Piggy are killed off. What's interesting is that we can also see some pattern around where the tone of the story starts to change - like when we being seeing visible differences to Jack's character, and when Simon has the conversation with the pig's head.

### Wrapping Up

I think that what's been done here is conceptually very interesting. We've managed to quantitatively analyze the emotional structure of a story and how it changes as the story progresses. By creating a 'sentiment score', we've attached a value and magnitude to emotion - an objective outlook on a subjective feeling. It's not perfect. The way I feel while reading a book will never be exactly the same as the way you feel while reading it. There are millions of other factors involved in reading, like my views and beliefs, the books I've read previously, preferences for writing style and so on. But this *does* give us an interesting outlook on storytelling in general, and on trends that show how stories tend to progress. This kind of analysis allows us to find patterns within stories and answer interesting questions like how a certain text differs across time and space, and history and culture. This, I think, is just the tip of the iceberg in understanding the ancient art of storytelling.
