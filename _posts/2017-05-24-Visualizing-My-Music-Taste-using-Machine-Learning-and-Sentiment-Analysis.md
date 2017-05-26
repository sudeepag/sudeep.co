---
title: Visualizing My Music Taste using Machine Learning and Sentiment Analysis
layout: post
comments: true
date: 2017-05-24 01:00:00 +GMT
author: Sudeep Agarwal
categories: ['machine-learning', 'sentiment-analysis']
image: 'http://sudeep.co/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/df.png'
---

<div align='center'>
<a href='https://insights.spotify.com/es/2016/09/29/10-data-art-projects/'><img src="/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/gradients_peter.png"></a>
<sub><sup><b>Peter Margaritoff's Gradients</b>: <i>Created with the average color of album covers for the top 2,000 songs on Spotify</i></sup></sub>
</div>



<h3>Intro • <i>The xx</i></h3>

Music is a complicated thing. No one fully understands why we're drawn towards a certain song, or so frustrated by another. In the recent paper [*Musical Preferences are Linked to Cognitive Styles*](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0131151) by Greenberg et  al., studies showed that cognitive style, or how individuals process information, influences their preference of music. The first study showed the link between empathy levels and the genres of choice. The second studied the effect of E-S cognitive styles (based on the Empathizing-Systemizing theory) on musical preference. Subjects with a bias towards empathizing preferred music with low arousal (gentle or warm), negative valence (sad or depressing) and emotional depth (poetic or thoughtful). On the other hand, those with a bias for systemizing showed preference for music with high arousal (strong or thrilling), positive valence (lively) and cerebral depth (complex).

This connection between cognitive processing and musical preference is very interesting to me. Having never truly understood my affinity to certain genres of music, I was inspired to attempt to quantify my music taste using data, to really understand the similarities and differences across the music that I listen to. I was also excited to apply concepts from machine learning such as clustering and unsupervised learning, and from natural language processing such as sentiment analysis, to this project and understand what insights this might produce. I've also wanted to explore Spotify's API for some time now, and this proved to be the perfect opportunity to do so.

And yes, each title in this blog post will be a song from my dataset.

<!--more-->

<h3>I Feel It Coming • <i>The Weeknd, Daft Punk</i></h3>

<img src="/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/stack.jpeg" align='center'>

The Spotify playlist is the modern day mixtape that you can carry with you wherever you go and share with the world. I enjoy putting together songs that share a common theme, and Spotify makes it really easy to do that. To build up my dataset, I included the songs in all the playlists I've ever created. [Spotipy](https://github.com/plamere/spotipy), which is a lightweight Python library for Spotify's [Web API](https://developer.spotify.com/web-api/), makes it really simple to query all of the tracks from my playlists. It also allowed me to query the top 50 tracks I've listened to over the past couple of months, which I also included, resulting in a total dataset of 278 songs.

A really cool aspect of the Spotify API is that you can also request the audio features for each track. This was brought about by Spotify's 2014 acquisition of the [Echo Nest](http://the.echonest.com), a music intelligence platform that analyzes music and forms the brains behind much of Spotify's music recommendation system that we see today. Here's a short description of some of these features, as described on Spotify's [documentation](https://developer.spotify.com/web-api/get-audio-features/) for the Web API.

`acousticness`: describes how *acoustic* the track is<br>
`danceability`: represents how suitable a track is for *dancing*, a combination of other elements like tempo and beat strength<br>
`energy`: represents a perceptual measure of *intensity* (metal is likely to have high energy and classical likely to have low energy)<br>
`instrumentalness`: predicts whether a track contains *vocals* ('ooh' and 'aah' sounds are treated as instrumental)<br>
`loudness`: describes the overall average loudness of a track<br>
`speechiness`: detects the presence of *spoken word* (rap music is likely to have a higher speechiness)<br>
`tempo`: overall estimated *pace* of the track in beats per minute (BPM)<br>
`valence`: describes the *positiveness* of a track (tracks with high valence sound happy, and those with low valence sound sad)<br>

I used Spotipy to query all of this information for each of the songs in my dataset, loading it up into a Pandas DataFrame to clean and process. Here's an example of five of the entries of the final DataFrame.

<div style ="font-size:10px">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>artist</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>energy</th>
      <th>instrumentalness</th>
      <th>loudness</th>
      <th>speechiness</th>
      <th>tempo</th>
      <th>valence</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>World Restart (feat. Kelela &amp; Ade)</td>
      <td>Kindness</td>
      <td>0.00881</td>
      <td>0.645</td>
      <td>0.664</td>
      <td>0.000074</td>
      <td>-10.073</td>
      <td>0.0775</td>
      <td>90.965</td>
      <td>0.396</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Back To You</td>
      <td>Twin Forks</td>
      <td>0.00594</td>
      <td>0.547</td>
      <td>0.848</td>
      <td>0.000000</td>
      <td>-5.622</td>
      <td>0.0680</td>
      <td>128.987</td>
      <td>0.391</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Anywhere We Go - Stripped Down</td>
      <td>Kaptan</td>
      <td>0.46600</td>
      <td>0.562</td>
      <td>0.403</td>
      <td>0.000003</td>
      <td>-10.255</td>
      <td>0.0258</td>
      <td>145.014</td>
      <td>0.291</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Talk Is Cheap</td>
      <td>Chet Faker</td>
      <td>0.47500</td>
      <td>0.640</td>
      <td>0.577</td>
      <td>0.000124</td>
      <td>-8.595</td>
      <td>0.1430</td>
      <td>140.086</td>
      <td>0.513</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Sound of Silence</td>
      <td>Simon &amp; Garfunkel</td>
      <td>0.13600</td>
      <td>0.435</td>
      <td>0.456</td>
      <td>0.000002</td>
      <td>-10.305</td>
      <td>0.0279</td>
      <td>107.529</td>
      <td>0.529</td>
    </tr>
  </tbody>
</table>
</div>

The next step, naturally, was to conduct an exploratory analysis with all of this data. How is the data distributed for my favorite songs across each of these audio features? How do these distributions compare to those of songs that are popular across the world right now?

<h3>Up We Go • <i>LIGHTS</i></h3>

![Distribution](/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/df.png)

Each subplot above describes the distribution of the labelled feature in my dataset. Valence, tempo and danceability seem to be mostly normally distributed, which means that I seem to have a preference for songs that are not overly depressing or cheerful, and that I prefer a good spread of music that is both upbeat and slow. This makes sense, because I generally listen to both indie folk (which often tends to be slow and mellow) and alternative rock or electronic (which tends to be much more fast-paced).

This trend is corroborated by the distribution of acousticness, which shows a preference for songs with low acousticness and with high acousticness, as evident by the two peaks. Energy, however, seems to be skewed right, which indicates that I prefer songs with higher energy. Since energy is an predictor of intensity, perhaps I am more drawn towards more intense indie tracks, as opposed to the softer, quieter ones. This, again, is supported by the loudness distribution, which shows a strong preference for louder music.

I very rarely listen to rap music, and this preference is supported by the distribution of speechiness, which seems to be skewed heavily to the left. Instrumentalness also shares a similar distribution, possibly due to the infrequency of classical music in my dataset. I do, however, have some instrumental music (soundtrack, electronic, etc) in the dataset, and this is reflected by the small spikes across higher instrumentalness values.

The next question I wanted to answer was how my music taste compared to what the rest of the world was listening to. I repeated the same process with songs from the [Today's Top Hits](https://open.spotify.com/user/spotify/playlist/37i9dQZF1DXcBWIGoYBM5M) playlist on Spotify to see how the distributions for the various audio features compared to the ones for my own top tracks.

![Distribution](/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/top_df.png)

The distributions of my top tracks, as shown in the previous plot, are represented by the grey histogram and curve, whereas the distributions of the tracks from the Spotify top hits are represented in blue. The differences that these audio features highlight are very interesting.

For acousticness, there seems to be only one peak that is skewed left, indicating the lack of acoustic top hits. Not surprising - many of the top hits today tend to be formulaic, overproduced pop. Energy seems to be more normally distributed for the top hits, which could mean that I have a slight preference for energetic music. The trend for loudness and tempo seem to be rather similar, with the same skew to the right and left correspondingly. The mean tempo for the top hits, however, seems to be slightly lower, which could support the observation about energy. Danceability and valence again, have a similar distribution for the top hits, but seem to have a higher mean, whereas instrumentalness and speechiness remain rather similar.

What does this mean? To summarize,

* I have a preference for both **acoustic** and **non-acoustic** music
* I prefer **more energetic** songs with **higher tempo**
* The top hits today tend to be **more danceable** and **happier** than what I generally listen to

Next, let's see if we can apply some machine learning to our data.

<h3>Another Story • <i>The Head and the Heart</i></h3>

Given the multi-dimensional nature of our data (each feature represents a dimension of the data point), it is difficult to visualize or concisely describe each data point. We often perform [dimensionality reduction](http://www.math.uwaterloo.ca/~aghodsib/courses/f06stat890/readings/tutorial_stat890.pdf) to reduce the number of features while maintaining the reproducibility of the data. There are several approaches to doing this, like feature selection (such as the ID3 algorithm which uses information gain) and feature transformation (which creates new features). [Principal Component Analysis](http://setosa.io/ev/principal-component-analysis/) (PCA) is a method of feature transformation that finds orthogonal dimensions with the highest variance i.e. the first principal component will have the largest variance, the second will have the second largest and so on. In this context, PCA will allow us to summarize our data into two dimensions so that we can plot it on an x-y scale.

![PCA Plot](/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/plot1.png)

As you can see from the annotated scatter plot above, my music taste is largely consistent within the two dimensions that capture the largest variance amongst the audio features. However, there are some outliers which don't fall within the cluster of points. Some of the songs that stand out the most include [X by LVNDSCAPE](https://open.spotify.com/track/2kJjD8d6Ygn3jKuuAhI0CI), [Vladimir's Blues by Max Richter](https://open.spotify.com/track/370TxU3tPIvVLmCrGd2EZ6) and [Beyond This Moment by Patrick O'Hearn](https://open.spotify.com/track/54QFGiOthVqXeJtiXdlLYa). Interestingly, most of these outliers happen to be from a playlist I created called Focus, which I listen to when I'm studying and contains only soft, instrumental music.

That's pretty amazing - if you recall, the original data contained no information about the playlist that it came from, and only had the audio features like danceability and acousticness. PCA condensed all that information into two dimensions to show how different these songs are from something like [Indian Summer by Jai Wolf](https://open.spotify.com/track/42nkVBjWYVhiijbof5zySm), which is closer to the origin. Indian Summer, while instrumental as well, is electronic and much more upbeat, and it's really cool how analyzing these features and using machine learning allowed us to visually see the difference.

We can take this one step further to construct a model that finds these outliers. There is a class of machine learning called novelty detection, which is the task of recognizing data points that differ from the available data. A commonly used approach in novelty detection is the [One Class Support Vector Machine](http://scikit-learn.org/stable/modules/outlier_detection.html) (SVM), which determines anomolies in a dataset where our anomalies are not labelled (another use case of this would be to detect fraudulent transactions when we don't have many examples of fraud). After classifying the points with the SVM, we can replicate the plot above to show the anomalies in the data.

![SVM Plot](/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/plot2.png)

The anomalies highlighted by the SVM are in red, while the remaining data points are in green. This type of model can be really powerful, and can be used to provide more accurate music recommendations. If a song falls within the green cluster, I'm probably more likely to enjoy it.

But so far we've only been analyzing the audio features of songs, and we already see that we can gain so much insight. However a song that sounds happy doesn't always contain cheerful lyrics, and the lyrics of a song also play a large role in determining our preference for the song. Could we perhaps use natural language processing, and in particular sentiment analysis, to reveal the general sentiment of a song, and apply this to reveal more about my musical preferences?

<h3>Lights Out, Words Gone • <i>Bombay Bicycle Club</i></h3>

In 2005, Russell, et al. published a paper titled [*The circumplex model of affect: An integrative approach to affective neuroscience, cognitive development, and psychopathology*](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2367156/), which introduced the four quadrants of emotional affect across the dimensions of arousal and valence - happy and content, sad and anxious.

<div align='center'>
	<img width='400px' src ='/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/quadrants.jpg'>
</div>

In March 2017, Çano and Morisio published [*MoodyLyrics: A Sentiment Annotated Lyrics Dataset*](http://dl.acm.org/citation.cfm?id=3059340&dl=ACM&coll=DL&CFID=766606185&CFTOKEN=87457547), which analyzed the arousal and valence of the words in song lyrics to label them with one of the four categories from Russell's model - happy, sad, relaxed and angry. The [MoodyLyrics dataset](http://softeng.polito.it/erion/MoodyLyrics.zip) contains 2,000 labelled songs and is processed to be balanced (each class has approximately the same number of instances) to avoid bias. To assess the sentiment of my own music, I trained a model using this labelled data, and tested it on the dataset constructed earlier. Henceforth, I will refer to the MoodyLyrics data as the *training* dataset and the earlier dataset with 287 songs as the *testing* dataset.

To retrieve the lyrics for the songs in both datasets, I used a library called [pylyrics3](https://github.com/jameswenzel/pylyrics3), which scrapes lyrics off [LyricWikia](http://lyrics.wikia.com) given an input of a song title and artist. I then cleaned and processed the lyrics to remove any stopwords (like *a*, *the*, etc), non-letter characters, and transformed all the words into lower case characters.

Once I had two clean datasets, I used a [Bag of Words](https://ongspxm.github.io/blog/2014/12/bag-of-words-natural-language-processing/) approach to create a numeric representation of the data. The Bag of Words model is commonly used in NLP to learn the vocabulary of documents and count the frequency of each word. By using [scikit-learn's](http://scikit-learn.org/stable/) vectorizer, I transformed both the datasets into matrices of shape `(x, y)`, where `x` represents the number of instances or songs, and `y` represents the number of features or words created by the vectorizer.

To train the model, I used [random forests](https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm), which is an ensemble learning method for classification, passing in the vectorized data from the training dataset and the corresponding emotion labels to the model to train over. Random forests are known for their high accuracy on large datasets and their low likelihood of overfitting, both of which are desirable in any form of machine learning. Once the model had been trained, I passed in the testing data to retreive the predictions of the emotion labels for my music, resulting in a table like the one below.

<div align='center' style ="font-size:10px">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>artist</th>
      <th>lyrics</th>
      <th>mood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Re: Stacks</td>
      <td>Bon Iver</td>
      <td>excavation today kumran everything happens pou...</td>
      <td>sad</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Home</td>
      <td>Edward Sharpe &amp; The Magnetic Zeros</td>
      <td>jade alabama arkansas love pa way love alex we...</td>
      <td>relaxed</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Escapee</td>
      <td>Architecture In Helsinki</td>
      <td>escapee never gonna stay unfamiliar building r...</td>
      <td>angry</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Shape of Love (feat. Boy &amp; Bear)</td>
      <td>Passenger</td>
      <td>came inside get rain looks things may well don...</td>
      <td>happy</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Mykonos</td>
      <td>Fleet Foxes</td>
      <td>whoa oh oh oh oh oh oh oh oh oh oh oh oh oh oh...</td>
      <td>relaxed</td>
    </tr>
  </tbody>
</table>
</div>

This prediction is probabilistic, which means that each song has a certain probability of having a certain mood (and consequently, all of these probabilities add up to 1.0). The moods you see assigned above are simply a `max` function over these probabilities, which just means that for example, Re: Stacks by Bon Iver is *most likely* to be sad. We can see how these probabilities are distributed across each mood, and how the counts for the final assignments vary.

![NLP](/images/post_images/2017-05-25-Visualizing-My-Music-Taste-using-Machine-Learning-and-Sentiment-Analysis/nlp.png)

The jittered scatterplot on the left allows us to see the distribution of probabilities for each mood. Each data point here represents the probability of a song having that corresponding mood (denoted by the color). We can see that there are a few data points for *happy* and *relaxed* with a high probability, which means that the model is confident that these songs are strongly happy and relaxing. *Sad*, on the other hand, seems to be concentrated at low probabilities, which means the model is not as confident in predicting that emotion in the dataset. The plot on the right shows the number of songs for each mood. While *angry*, *happy* and *sad* are approximately the same, *relaxed* seems to be much higher, which might indicate that I have a preference for songs with relaxing lyrics.

To summarize,

* The happy songs that I like tend to be **strongly happy**
* I might have a preference for songs with **relaxing** lyrics

<h3>Le départ • <i>Damien Fleau</i></h3>

This has been one of my most interesting projects because I got the chance to interweave two starkly different concepts that I love, music and data science. When we listen to music, we don't think about the tempo, the valence, the sentiment. A good song just speaks to you. It beckons you to listen to it again and again. It's a subconscious process that we don't completely understand. But with the help of machine learning and natural language processing, we can begin to build a notion of musical preference and get a better understanding of the quantifiable features that make someone love a song. I think that we're at the tip of the iceberg in the field of music intelligence, and data science teams from companies like Spotify and the Echo Nest are leading the way in automated playlist creation and music recommendation. But we've still got a long way to go in teaching machines how to craft the perfect music listening experience.
<br>
<div align='center'>
<iframe src="https://open.spotify.com/embed/user/1178997413/playlist/2sVEFVSq8fWIZKLnOVi0Bk" width="800" height="400" frameborder="0" allowtransparency="true"></iframe><br>
<sub><sup><i>A playlist with all the songs featured in the titles</i></sup></sub>
</div>
