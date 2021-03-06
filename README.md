# PageRank

Write an AI to rank web pages by importance.

## Background:
When search engines like Google display search results, they do so by placing more “important” and higher-quality pages higher in the search results than less important pages. But how does the search engine know which pages are more important than other pages?

One heuristic might be that an “important” page is one that many other pages link to, since it’s reasonable to imagine that more sites will link to a higher-quality webpage than a lower-quality webpage. We could therefore imagine a system where each page is given a rank according to the number of incoming links it has from other pages, and higher ranks would signal higher importance. But this definition isn’t perfect: if someone wants to make their page seem more important, then under this system, they could simply create many other pages that link to their desired page to artificially inflate its rank.

For that reason, the PageRank algorithm was created by Google’s co-founders (including Larry Page, for whom the algorithm was named). In PageRank’s algorithm, a website is more important if it is linked to by other important websites, and links from less important websites have their links weighted less. This definition seems a bit circular, but it turns out that there are multiple strategies for calculating these rankings.

### Random Surfer Model:
One way to calculate the PageRanks of a corpus of webpages is to use a random surfer model. This models the behaviour of a web surfer who will start on a random web page and with some probability d randomly click any link on the page, or with with probability 1-d randomly go to any other page in the corpus. This model can be interpreted as a Markov Chain, with each page representing a state, and each page having its own transition model for the probability of moving to any other page next. By sampling a large number states from the Markov Chain, a distribution of the number of visits to each page in the corpus can be obtained, and from it, an estimate of the relative page ranking for each page from 0 to 1 can be calculated.


#### Iterative Algorithm:
A page's PageRank can also be calculated using the following recursive mathematical expression:

![PR(p) = \frac{1-d}{N} \:+ \:d \sum_{i} \frac{PR(i)}{NumLinks(i)}]

where:
* PR(p) is the PageRank of a given page p
* d is the damping factor
* N is the number of pages in the corpus
* i is each possible page in the corpus
* PR(i) is the PageRank of page i
* NumLinks(i) is the number of links on page i

In order to calculate the PageRank for each page in the corpus, we can start with a basic PageRank for each page of 1 / N, and then iteratively calculate new PageRank values for each page. This continues until PageRank values converge (changes on an iteration are less than a threshold value).


##### Specification:
Complete the implementation of transition_model, sample_pagerank, and iterate_pagerank.

The transition_model should return a dictionary representing the probability distribution over which page a random surfer would visit next, given a corpus of pages, a current page, and a damping factor.

* The function accepts three arguments: corpus, page, and damping_factor.
  * The corpus is a Python dictionary mapping a page name to a set of all pages linked to by that page.
  * The page is a string representing which page the random surfer is currently on.
  * The damping_factor is a floating point number representing the damping factor to be used when generating the probabilities.
* The return value of the function should be a Python dictionary with one key for each page in the corpus. Each key should be mapped to a value representing the probability that a random surfer would choose that page next. The values in this returned probability distribution should sum to 1.
  * With probability damping_factor, the random surfer should randomly choose one of the links from page with equal probability.
  * With probability 1 - damping_factor, the random surfer should randomly choose one of all pages in the corpus with equal probability.
* For example, if the corpus were {"1.html": {"2.html", "3.html"}, "2.html": {"3.html"}, "3.html": {"2.html"}}, the page was "1.html", and the damping_factor was 0.85, then the output of transition_model should be {"1.html": 0.05, "2.html": 0.475, "3.html": 0.475}. This is because with probability 0.85, we choose randomly to go from page 1 to either page 2 or page 3 (so each of page 2 or page 3 has probability 0.425 to start), but every page gets an additional 0.05 because with probability 0.15 we choose randomly among all three of the pages.
* If page has no outgoing links, then transition_model should return a probability distribution that chooses randomly among all pages with equal probability. (In other words, if a page has no links, we can pretend it has links to all pages in the corpus, including itself.)

The sample_pagerank function should accept a corpus of web pages, a damping factor, and a number of samples, and return an estimated PageRank for each page.

* The function accepts three arguments: corpus, a damping_factor, and n.
  * The corpus is a Python dictionary mapping a page name to a set of all pages linked to by that page.
  * The damping_factor is a floating point number representing the damping factor to be used by the transition model.
  * n is an integer representing the number of samples that should be generated to estimate PageRank values.
* The return value of the function should be a Python dictionary with one key for each page in the corpus. Each key should be mapped to a value representing that page’s estimated PageRank (i.e., the proportion of all the samples that corresponded to that page). The values in this dictionary should sum to 1.
* The first sample should be generated by choosing from a page at random.
* For each of the remaining samples, the next sample should be generated from the previous sample based on the previous sample’s transition model.
  * You will likely want to pass the previous sample into your transition_model function, along with the corpus and the damping_factor, to get the probabilities for the next sample.
  * For example, if the transition probabilities are {"1.html": 0.05, "2.html": 0.475, "3.html": 0.475}, then 5% of the time the next sample generated should be "1.html", 47.5% of the time the next sample generated should be "2.html", and 47.5% of the time the next sample generated should be "3.html".
* You may assume that n will be at least 1.

The iterate_pagerank function should accept a corpus of web pages and a damping factor, calculate PageRanks based on the iteration formula described above, and return each page’s PageRank accurate to within 0.001.
* The function accepts two arguments: corpus and damping_factor.
  * The corpus is a Python dictionary mapping a page name to a set of all pages linked to by that page.
  * The damping_factor is a floating point number representing the damping factor to be used in the PageRank formula.
* The return value of the function should be a Python dictionary with one key for each page in the corpus. Each key should be mapped to a value representing that page’s PageRank. The values in this dictionary should sum to 1.
* The function should begin by assigning each page a rank of 1 / N, where N is the total number of pages in the corpus.
* The function should then repeatedly calculate new rank values based on all of the current rank values, according to the PageRank formula in the “Background” section. (i.e., calculating a page’s PageRank based on the PageRanks of all pages that link to it).
  * A page that has no links at all should be interpreted as having one link for every page in the corpus (including itself).
* This process should repeat until no PageRank value changes by more than 0.001 between the current rank values and the new rank values.
You should not modify anything else in pagerank.py other than the three functions the specification calls for you to implement, though you may write additional functions and/or import other Python standard library modules. You may also import numpy or pandas, if familiar with them, but you should not use any other third-party Python modules.

###### Usage:
Requires Python(3) to run:

python pagerank.py (corpus_directory)
