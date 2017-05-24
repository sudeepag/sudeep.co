---
title: The Death of Language
layout: post
comments: true
date: 2016-12-29 10:00:00 +GMT
author: Sudeep Agarwal
category: 'data-visualization'
---

*"The wisdom of humanity is coded in language."*

Lyle Campbell, an American scholar and linguist known for his work on indegenous American languages, hints at a problem much graver than most would care to admit. Even as someone who can speak English fluently, there are times where it just seems more natural to speak in my native language, Hindi. It's not that it's any easier or more practical, it's just that it seems more fitting in that particular situation. This is perhaps difficult to convey in words (isn't that ironic?) but any bilingual speaker can confirm that instant connection you feel when you talk to someone in a native tongue, especially if it's in a place where few people speak that language.

There is a Portugese word *saudade*, a term commonly used in Galician literature and heard in the music of Brazil. What strikes me most about *saudade*, and many other such words is that they are untranslatable to other languages, yet so undeniable potent. The concept of *saudade* portrays a meloncholy nostalgia for something that perhaps has not even happened. Take for example *mamihlapinatapai*, that is derived from the Yaghan language of Tierra del Fuego and known to be one of the hardest words to translate. It refers to an expressive and meaningful silence, the look that is shared across the table by two people where each understands the other and is in agreement with what is being expressed. I could go on with more examples of elaborate words in foreign tongues, but the point here is that there is a vast expanse of emotion captured in languages that is difficult to convey otherwise.
<!--more-->
Campbell goes on to state that when a language dies, so does the knowledge it carries. And in many ways, he's not wrong. I was born in Ahmedabad, where a majority of the people speak Gujarati. Yet today, having grown up in Singapore, I can barely follow a conversation in Gujurati, let alone construct a single sentence. One less Gujurati speaker in the world might not be a big deal, but when 90% of the world's approximately 7,000 languages are expected to become extinct, one speaker could make all the difference.

The data enthusiast in me wanted to see for myself just how widespread this problem was by taking an analytical approach to understanding and visualizing where exactly these endangered languages were spoken, and which regions were most at risk.

To get the data for this exercise, I used The Guardian's *Extinct Languages* dataset on [Kaggle](https://www.kaggle.com/the-guardian/extinct-languages). The detailed dataset contains the list of languages at risk, along with the number of speakers, names of countries where the language is still spoken, and the degree of endangerment. It follows the UNESCO language endangerment [classification](http://www.unesco.org/new/en/culture/themes/endangered-languages/atlas-of-languages-in-danger/), which is as follows:

- **Vulnerable**: most children speak the language, but it may be restricted to certain domains (e.g., home)
- **Definitely endangered**: children no longer learn the language as a 'mother tongue' in the home
- **Severely endangered**: language is spoken by grandparents and older generations; while the parent generation may understand it, they do not speak it to children or among themselves
- **Critically endangered**: the youngest speakers are grandparents and older, and they speak the language partially and infrequently
- **Extinct**: there are no speakers left

After downloading the dataset, I loaded it up using [pandas](http://pandas.pydata.org/) in a [Jupyter notebook](http://jupyter.org/), and started to explore and visualize it using [Plotly](https://plot.ly/). All the data is sourced from The Guardian's [dataset](https://www.theguardian.com/news/datablog/2011/apr/15/language-extinct-endangered#data), and all plots are my own.

The first thing I was interested in looking at was a visualization of all the endangered languages plotted on the world map by their degree of endangerment.

<iframe width="800" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/334.embed"></iframe>

<br>
Looking at the geospatial plot, we can see that the majority of endangered languages seem to be strewn across the continents of North America, South America, Africa and Asia. One interesting trend within North and South America is that the regions on the West coast seem to have a much higher number of languages at risk than those on the East coast. In Africa, the majority of language loss seems to be occuring over the regions in the vertical center of the continent. The positioning of endangered languages is an interesting phenemomen, and especially so in the case of the Americas, is something that is too obvious too be simply a coincidence.

To understand more clearly which countries were contributing the most, I first categorized each language by the countries it is still spoken in, and collated the list of languages for each country. Using the raw count of endangered languages as a benchmark, I created a chloropleth plot which would allow the countries with the highest number of languages at risk to stand out the most, and presented the plot in an interactive orthographic projection.

<iframe width="800" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/500.embed"></iframe>

<br>
The top five countries turned out to be the United States of America, with 227 languages, India with 199 languages, Brazil with 190 languages, Indonesia with 149 languages and Russia with 148 languages. A large proportion of the American languages turn about to be indegenous Native American languages. According to UNESCO, most of these indegenous languages are critically endangered, and many are already extinct. This corroborates with what we're looking at in this plot.

The next step, naturally, was to look at the other end of the spectrum, to find out about languages that are on the *brink* of extinction, and had only a single speaker left in the world.

<iframe width="800" height="480" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/484.embed"></iframe>

<br>
There turned out to be eighteen languages with just a single speaker left in the world. It would not be surprising if any of these languages have become extinct since the dataset was last updated.  According to [Campbell](http://rosettaproject.org/blog/02013/mar/28/new-estimates-on-rate-of-language-loss/), the rate of extinction of languagues currently stands at one language around every three months. It is also interesting to note that many of these high risk languages are from the North and South American regions, and this is representative of the larger proportion of North and South American languages which are at risk of endangerment in general, as mentioned above. This just goes to show how fragile a language is - all you need is a single person who speaks a language from an entire population of 7.4 billion people to save a language from extinction.

The fragility is precisely what makes the preservation of language and culture so important. Pamela Serota Cote, whose work focuses on Breton, a Celtic language spoken in Brittany, France, argues, "Because language discloses cultural and historical meaning, the loss of language is a loss of that link to the past. Without a link to the past, people in a culture lose a sense of place, purpose and path; one must know where one came from to know where one is going." She goes on to make the case that the loss of a language has a lasting impact on a people's sense of identity and belonging and could potentially even have the power to uproot an entire community.

This almost evokes a feeling of *saudade*, of a meloncholic nostalgia for the imminent death of a language, for the memory of the final word yet to be uttered, for the meaning of the whisper no one will ever remember.
