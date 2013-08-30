---
layout: post
title: "Latent Semantic Analysis in Haskell"
date: 2012-04-04 02:41
comments: true
categories: 
---


Last weekend I decided to learn Haskell with [Francis Williams](http://fwilliams.org/). It was an epic adventure. Last night I implemented LSA - Latent Semantic Analysis using Haskell.

For those of you who are not enlightened: [LSA](http://en.wikipedia.org/wiki/Latent_semantic_analysis) takes a list of documents, extracts terms from them, builds a [tf-idf](http://en.wikipedia.org/wiki/Tfidf) matrix, then compresses the matrix to build a lower dimensional approximation, to improve the speed and accuracy of measuring the similarity between documents. Sometimes, this is used in combination with clustering to group the documents into categories.

I want to point anyone interested in this towards the following resources that helped me do this:

- [The best Haskell tutorial on the internet](http://learnyouahaskell.com/)
- [The best description of LSA I've seen (python)](http://www.puffinwarellc.com/index.php/news-and-articles/articles/33-latent-semantic-analysis-tutorial.html)
- [Example of how to do SVD (singular value decomposition) in Haskell](http://journal.batard.info/post/2009/04/08/svd-fun-profit): 

In case anyone wants to do something similar themselves, here is the code I ended up with. Please let me know (email?) if I'm doing something stupid and I'll fix this up. I suspect that I am, considering how new I am to Haskell.

``` hs
import Data.List
import Data.Char
import Numeric.LinearAlgebra

titles = ["The Neatest Little Guide to Stock Market Investing",
          "Investing For Dummies, 4th Edition",
          "The Little Book of Common Sense Investing: The Only Way to Guarantee Your Fair Share of Stock Market Returns",
          "The Little Book of Value Investing",
          "Value Investing: From Graham to Buffett and Beyond",
          "Rich Dad's Guide to Investing: What the Rich Invest in, That the Poor and the Middle Class Do Not!",
          "Investing in Real Estate, 5th Edition",
          "Stock Investing For Dummies",
          "Rich Dad's Advisors: The ABC's of Real Estate Investing: The Secrets of Finding Hidden Profits Most Investors Miss" ]

stopwords = ["and","edition","for","in","little","of","the","to"]

text = unlines titles

docs :: [[String]]
docs = map (filter (not . (`elem` stopwords))) $      -- stopwords filter
        map words $
          lines $
            filter (\x -> isAlpha x || isSpace x) $   -- discard everything except alpha and space characters
              map toLower text                        -- lowercase the input

tf :: [([Char], Int)]
tf = filter (\(_,f) -> f>1) $ map (\l@(x:xs) -> (x,length l)) . group . sort $ concat docs -- remove words that appear only once

doc_freq :: Int -> [Char] -> Int
doc_freq d t = length (filter (==t) (docs !! d))

mat :: Matrix Double
mat = buildMatrix (length tf) (length docs) ( \(term, doc) ->
          let occurances = fromIntegral $ doc_freq doc $ fst $ tf !! term -- occurance count
              docLength = genericLength $ docs !! doc                     -- words per doc
              numDocs = genericLength docs                                -- number of docs
              commonness = fromIntegral $ snd $ tf !! term                -- number of docs this word occurs in
          in (occurances / docLength * log (numDocs / commonness))
      )

compress k m = u_k <> sigma_k <> v_k where
    (u,sigma,v) = fullSVD m                         -- get SVD
    sigma_k = (takeColumns k . takeRows k) sigma    -- keep k values of Σ
    u_k = takeColumns k u                           -- keep k columns of U
    v_k = takeRows k $ trans v                      -- keep k rows of v

reduce_dim k m = v_k where
        (u,sigma,v) = fullSVD m                         -- mapping of documents to concept space
        v_k = takeRows k $ trans v                      -- keep k rows of v
```
