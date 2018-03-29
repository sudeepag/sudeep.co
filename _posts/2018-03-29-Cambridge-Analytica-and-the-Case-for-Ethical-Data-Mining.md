---
title: Cambridge Analytica and the Case for Ethical Data Mining
layout: post
comments: true
date: 2018-03-29 00:00:00 +GMT
author: Sudeep Agarwal
categories: ['musings', 'data-science']
image: 'http://sudeep.co/images/post_images/2018-03-29-Cambridge-Analytica-and-the-Case-for-Ethical-Data-Mining/graphic.png'
hidden: true
---

<div align='center'>
<img width="700px" src="/images/post_images/2018-03-29-Cambridge-Analytica-and-the-Case-for-Ethical-Data-Mining/graphic.png">
</div>
<br>

It was recently made public that Cambridge Analytica, a political data firm hired by Trump's 2016 election campaign, gained access to the private information of more than *50 million* Facebook users. The data was collected through a personality survey app designed by Aleksandr Kogan, a psychology professor at Cambridge University, and scraped private information from users and even those of their friends. Using this data, the firm allegedly identified the personalities of American voters and influenced their behavior.

What's interesting here is that Facebook claims this was not a data breach. In their [press release](https://newsroom.fb.com/news/2018/03/suspending-cambridge-analytica/) suspending the Cambridge Analytica from Facebook, VP Paul Grewal wrote

> The claim that this is a data breach is completely false. Aleksandr Kogan requested and gained access to information from users who chose to sign up to his app, and everyone involved gave their consent. People knowingly provided their information, no systems were infiltrated, and no passwords or sensitive pieces of information were stolen or hacked.

This raises several questions around the ethics of data mining and our willingness to share information in an increasingly online world. What does this mean for data scientists, whose very roles revolve around the collection and analysis of data? Moving forward in the wake of such events, it is interesting to consider how the reactions of various stakeholders will influence our society and affect how we view data. 

<!--more-->

Will governments impose regulations on companies that collect any information on their users? With policy dictating the type of data that can be collected and how it must be handled, how do we then continue to drive innovation without being hindered? A regulated data economy could perhaps bring its own host of problems. An article called [*The World's Most Valuable Resource Is No Longer Oil, but Data*](https://www.economist.com/news/leaders/21721656-data-economy-demands-new-approach-antitrust-rules-worlds-most-valuable-resource) published on The Economist makes the case that the data economy necessitates a new approach to antitrust rules. It argues that because most of these services are free, these firms fail to "raise the alarm when standard antitrust tests are applied". If this were to happen, we would need to rethink how we approach antitrust laws for Internet giants.

Will companies attempt to find alternative means for data collection or even change their business models completely? For many web services like Facebook, digital advertising is at the crux of their business model. In fact, Facebook reported in 2017 that [98% of its quarterly revenue](http://fortune.com/2017/05/05/facebook-digital-advertising-business-model/) came from advertising. But this is where the problem lies - targeted advertising, which involves using user data to target ads to audiences with certain traits, lies at the heart of digital advertising. Without access to data, the very notion of targeted advertising falls apart and so does this business model. We could possibly see online services becoming pay-to-use in order to support a privacy-oriented business model.

Will users realize the true value of their data and refuse to interact with any platform that has the ability to collect and store their information? This raises an interesting paradox in which individuals would have to disregard the benefit they derive from the convenience of a split-second Google search or the recommendation of related products on Amazon in order to give these services up. For this to happen, the perceived importance of data privacy to any individual needs to outweigh the convenience of the service. But realistically, what proportion of people would actually stop using Facebook today if they were told that their data was being sold for revenue? 

These scenarios depict extremes of a data dystopia that is perhaps far too unimaginable for the comfort of a data scientist or machine learning engineer.

Anyone vaguely familiar with how machine learning algorithms work can tell you the importance of data. Without the presence of sufficient and good quality samples to train the algorithm, predictive models simply fail to achieve the level of accuracy necessary for the personalization algorithms that fuel services like Amazon or the ranking algorithms that run the Facebook news feed. The intelligence of these algorithms is grounded in the notion that they behave differently for me than they do for you. They understand how I interact with the system. They understand my likes and dislikes. They understand what I value the most. Without data collection, these services cease to be *personal*. They cease to be intelligent.

How do we then continue to build intelligent systems that benefit users and drive innovation, without undermining privacy? It almost seems like an impossible, and perhaps even paradoxical task. In 2012, Zeynep Tufekci, an assistant professor at the University of North Carolina wrote insightfully in the [MIT Technology Review](https://www.technologyreview.com/s/428210/data-dystopia/),

> I do not claim there are easy answers to the questions Facebook raises. Any algorithm for the news feed would have its downside. But the questions are important because Facebook occupies an important civic niche. Decisions about how it uses its hoard of data, what it makes public, and how much access it gives political and corporate campaigns will affect us all. We need to be talking not just about the potential of this awesome data store but about the power the company has and the ethics it upholds.

And it's not just Facebook, in today's society, who occupies this 'important civic niche', but every company that actively mines and stores user information. It is necessary, more so today than ever before, to have a conversation on the ethics of data mining and the responsibility involved in 'owning' user data. 

The solution, I think, is not to make decisions hindering our progress in creating innovative products that bring unquantifiable benefit to our society. Instead, it is to create an environment that is conscious of the importance of individual privacy. It is to focus our innovation on creating methodologies that ensure user data is protected and ethically handled. It is to design algorithms that prioritize not only the  anonymization of data, but also its protection from any potential de-identification.

In 2016, Craig Federighi, Apple's Senior VP of Software Engineering, took the stage at Apple's Worldwide Developers' Conference to make an important statement. While he acknowledged their need for collecting user information in making good software, he also underscored their attention in ensuring this was done ethically through something called *differential privacy*.

In his words, differential privacy is "a research topic in the areas of statistics and data analytics that uses hashing, subsampling and noise injection to enable crowdsourced learning while keeping the data of individual users completely private." Statistically speaking, the addition of random noise drawn from a Laplace or Gaussian distribution to the user data collected prevents third parties from reverse engineering any identifiers (if you're interested, there are some great slides on the statistics behind this and why it works [here](https://www.cs.cmu.edu/~yuxiangw/docs/Differential%20Privacy.pdf)). In theory, this means that even if hackers or intelligence agencies had access to private data, they would not be able to extract information on any single individual.

This approach, however, is far from perfect. The challenge with differential privacy is that there is an inherent tradeoff between accuracy and privacy. The larger the dataset, the more noise you inject. The more noise that is injected, the more robust your data privacy will be but the less accurate the machine learning model is going to be. There is certainly some research currently being done in the field of differential privacy. For example, in the paper [*Differentially private random decision forests using smooth sensitivity* (Fletcher, Islam)](https://www.sciencedirect.com/science/article/pii/S0957417417300428), the authors propose a random decision forest that outperforms the current state-of-the-art algorithm while respecting the principles of differential privacy. But more of such research is necessary if we wish to improve the accuracy of our models without compromising on user privacy.

Another approach used by Apple is to go one step further, and remove the risk of data being compromised in the cloud by not even sending it there in the first place. Since iOS 10, Apple has been using [on-device deep learning for facial detection](https://machinelearning.apple.com/2017/11/16/face-detection.html) in order to protect user privacy by developing more efficient memory optimization algorithms that perform the same tasks without the need for a centralized cloud. As our devices continue to become more powerful computationally, with rising privacy concerns we could perhaps see a paradigm shift to device-based algorithms for sensitive user data instead of cloud-based algorithms.

I think that it is time for not just Apple, but for all companies that collect and analyze user data to start a conversation and work together on tackling the issue of data privacy so that events like Cambridge Analytica do not repeat themselves. The role of a data scientist should go beyond simply deriving insights from data; it should involve being vocal about the ethics of data mining, designing privacy-aware algorithms and models, and rethinking our current approach to data science.