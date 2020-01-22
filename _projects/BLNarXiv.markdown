---
layout: post
title:  "BLNarXiv"
categories: jekyll update
---

Our society, and me in particular, think very seldom about the needs of certain people.
Recently I have come across the need of visually impaired people to use certain web services.  
In particular, for visually impaired scientists it is crucial to be able to check [arXiv](https://arxiv.org) on a daily basis.

Since I have some background scraping webs, and a lot of scripting in my fingers... I took the challenge of making a basic system to read [arXiv](https://arxiv.org) using a Text To Speech system (TTS).

Looking through the repositories in Ubuntu I found a few options, but none really good. The best options that I have tried are:
 - **espeak**: Is the go-to option even when the default voices are quite robotic and the enunciation is far from great.
 - **festival**: (or flite for its lightest version) In my opinion this is the best option, with some acceptable stating voices, although there is plenty of room for improvement.

It cannot be denied that in this case MacOS is the clear winner. The "**say**" command, has a very human voice and the enunciation of long sentences is quite good. This OS also provides some specific accessibility software that works pretty well.  
It is really a pity that this software (VoiceOver, say...) is private and not available for people to use, or expand on it. I cannot imagine at all the line of thought of someone who achieves such an important tool for people in need and decides to keep it private.

## The challenges
There are three main challenges for this project.
 1. Get the daily updates. Everyday there are anywhere between 20-70 papers to check. In our case of interest there are ~70 papers every day, from which you want to read ~10 abstracts and usually download and read the whole thing for a couple of them. This issue requires a system that connects to arXiv's server, **download and parse** the html extracting the title, abstract, authors...
 1. **Process the text**, taking into account that it is not uncommon to have mathematical formulas in the title or abstract.
 1. Get the computer to read it out loud and maybe save the pdf for later reading. This brings back the second point, since software-reading a paper presents the huge challenge of **translating mathematical formulas to text**.

### 1- Crawl the web
I should make a scraping tutorial someday.
In this case the website is fairly simple and very well written, so I didn't find the need for anything more sophisticated than [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/). Using the "Inspect Element" tool that Firefox provides, it is easy to follow the "cascade" of html tags until each of the elements of interest.  
I find it cleaner to explicitly select each of the elements. For instance, the title of the papers are inside a number of `<div>` containers some description lists (`<dl>`)... so instead of looking for them using the methods `find/findAll('div',class_="list-title mathjax")` I think it is more elegant and easier to debug to go selecting elements one by one. In this case the full path to the title is:  
`body>div[5]>div>dl[1]>dd[1]>div>div[1]`

In pseudo-python-code:  
<pre><code>html = get_html(url)
S = BeautifulSoup(html)
body = S.find('body')
content = body.find('div',{id='content'})  # or  body.findAll('div')[5]
...
all_papers = container_div.findAll('dd')</code></pre>

This should provide a list of chunks of html code containing title, author, arXivID, abstract and subjects. How to store/process the information is a matter of personal taste. I made a class `ArXivEntry` so I can set the relevant properties as attributes and add useful functions (clean math, save file,...) as methods.


### 2- Process and parse
Work in process


### 3- Read the text and further processing
This point brought some other problems with it, more on that later.
The reason for doing all this, rather than using the systems VoiceOver to read the web and the pdfs, is that this a very specific task where the information is easily accessible.
It is not far fetched to think of a custom system that makes it easier to read this content as opposed to a generic software, able to read "anything" but with a steep learning curve.

The naive solution is very simple. Once the arXiv has been parsed, we have in the computer a list of objects which contain the text to be read. One can simply read one piece of text after the other until we are through, but that is a very limited solution since there are nearly 100 papers a day... it would be impossible to get to the last papers!!

The next naive implementation (current) is to have a skip mechanism that allows to break the current audio and continue with the next one. In addition I set up a couple of steps since we do not need to read every abstract and paper. Right now the read-flow is as follows:

1. Read title. Ask if continue reading abstract.
1. If not, skip to next entry and start the cycle. If yes, proceed to read the abstract, if the `<Esc>` key is pressed, then stop reading and head for the next entry.
1. If the abstract has been read, then ask if download the paper. Give the choice to read at that moment or save for later.
1. Once all the entries have been checked, proceed to read the "saved for later" papers.


## Next Steps
It has become clear that it is necessary to have a "navigation" system that allows switching between *browse mode* or *full read mode*, or search quickly through the entries...
