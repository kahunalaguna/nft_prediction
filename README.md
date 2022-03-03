NFT Madness
Kevin Gates
March 2022


### Overview

This project seeks to build on my skills in regression modeling and webscraping utilizing API's. I set out to investigate the the frenzy of activity booming in the nft space by applying my machine learning toolkit to analyze one NFT collection. The general overview of my worflow was as follows: scraping nft transaction data using the Rarible Ethereum Protocol [API](https://docs.rarible.org/ethereum/ethereum-overview/), which allows read access to the blockchain, scraping metadata at the collection and individual token level using the [opensea](https://docs.opensea.io/reference/api-overview) and rarible API, cleaning and processing the json data, feature engineering, fitting and tuning a variety of different regression models on the data, and evaluating and analyzing model performance against my problem statement. 

### Introduction

The focus of my work was the mfers collection that can be found [here].(https://opensea.io/collection/mfers). Mfers were created by twitter user Sartoshi who designed 10,000 absurd and one of a kind stick-figure characters based off a meme popular amongst crypto communities. They were minted on 11-30-21 and are sold primarily on OpenSea, one of the leading marketplacesfor NFTs. NFT trading volume has skyrocketted quarter over cover even though the lauded future applications for nft's are far from being realized. Though NFT's may one day disrupt the future of gaming in play to earn environments or revolutionizing in-game asset economies, or take over the fine art collection world as digital assets, most popular NFT collections at this time are merely speculative pieces of digital art or collectibles. 

The NFT market itself poses a unique set of dynamics that contrast with that of the other capital markets. While public stock markets are considered highly efficient, with numerous buyers and sellers and a high degree of competition constantly updating prrice through transactions and eliminating opportunity for arbitrage in theory (Efficient Market Hypothesis), NFT markets set themselves apart with a highly illiquid environment, few buyers and sellers, a lack of pulic information, and an unproven track record of historical performance. Though these dynamics contribute to a heightened degree of risk, they also provide in theory a greater degree for arbitrage for the dilligent trader.

This project seeks to investigate one such collection of NFTs to discover what can be learned from sparse transaction data and the asset class themselves to predict price and potentially uncover hidden alpha. 

### Problem Statement

The goal of this project is to identify the key drivers in price for a single NFT Collection.

### Data Science Process

Data Collection:

I used Rarible and OpenSea API's to scrape each of the nearly 20,000 transactions available for each of token in this collection. Using the requests library and hand-crafted functions I automated a scraping process that accessed the blockchain through the Rarible Protocol to sweep for transactions belonging to the mfers collection and pulled metadata containing information about the attributes of each token for use in engineering measurements of feature-rarity as price predictors. I also scraped collection level trading data, amounting to stats on volume traded and average prices at different short term horizons. 

Data Cleaning:

With so much data in json format, the labor in this project was primarily rooted sparsing and cleaning the data until I could create dataframes in pandas that I could actual use to conduct exploratory data analysis and fit models.

Preprocesssing:

After parsing the json data I set out to engineer numeric features from available categorical variables. The following list relates the categories of features for the tokens in this collection:

1. 1/1                   
2. 4:20 watch            
3. background            
4. beard                 
5. chain                
6. eyes                  
7. hat over headphones   
8. hat under headphones  
9. headphones            
10. long hair             
11. mouth                 
12. shirt                
13. short hair            
14. smoke                 
15. type          

Each category had a range of unique values and the combination of values for each feature category made each token unique from the other 9999. Having scraped information on the number of tokens in a collection that had a given value for each feature category I engineered two sets up features: feature rarities and rarity scores. Feature rarities was calculated as the number of tokens with a given value divided by the number of tokens in the collection. Rarity scores was an unweighted calculation taking 1 over the feature rarity. To calculate total rarity score I summed rarity scores for each token's features. Total rarity scores had a max value of 10000.  

Exploratory Data Analysis:

I focused my EDA and creation of visuals that could explain behavior of key predictor variables for the whole collection over the entire life of the project (11-30-21 to present). I investigated price relationship over time, mean price behavior, distributions of price and rarity score for all tokens in the collection, and correlations between predictor variables and price. I also attempted to visually fit the data into LINEM assumptions for multiple-linear regression and found that the data loosely fit into the assumptions' framework.

Modeling:

The baseline model for this example would be the model that predicts the average price for an nft in the collection (1.14 ETH) for each transaction. I was confident I could create a model that could more closely predict actual price. 

The first models I fit were linear regresssions using feature rarity and rarity scores as predictor variables. These preliminary models were highly innacurate with r2 scores below 0.20. I then fit a whole host of unconventional regression models on the data to see if there was something unique about the data, something intrinsic to the features of each token that a tree regressor for example might learn better than a linear regressor. In response I received a host of drasticly overfit models and/or negative r2 scores. Returning to the linear models I iterated by implementing nomalization, regularization and polynomial features, by testing different subsets of input data, and by including as uniform coefficients for each transaction colleection level stats like the 1, 7 and 30 day average sale price. The best r2 score was 0.25. 

I decided that more models or hyperparameter tuning wouldn't lead me in the right direction so I returned to the data. Though my data wasn't fit for time series modeling being. a collection of unique assets traded at irregular intervals, I decided to use time series preprocessing techniques to enginner a new set of features. These features prioritized the most recent transactions for each X in order to predict y. They included the rolling average sales price, the min, and the max for the last 10 and last 25 transactions, as well as the price of the most recently sold token in the collection.Using these features I was able to massively improve r2 scores and reduce rmse and mae accuracy metrics. 

Final Models:

Linear Regression
Train R2: 0.806
Test R2: 0.805
X_vars: (scaled data): rarity scores, collection stats, and recent transaction stats
RMSE: 2046 (USD)
MAE: 697 (USD)

Support Vector Regressor
Params:(C=0.01, gamma=1, kernel='poly')
Train R2: 0.862
Test R2: 0.863
X_vars: (scaled data): rarity scores, collection stats, and recent transaction stats
RMSE: 1789 (USD)
MAE: 901 (USD)

### Conclusions and Further Recommendations:

The newest features included in models initiated astounding improvements in performance. Intuitively, it was remarkable that the features that contribute to explaining the greatest portion of variance in nft price were simply the derived from the sales price of the most recent transactions. For these to mean more than any feature rarity or rarity score variable intuitively can be used to explain that the key driver of price in nft price is momentum.

Momentum trading is a strategy that buys into bullish trends and sells bearish trends. For those familiar with the lingo of crypto commmunities, momentum trading strategies aren't very different than the concept of "aping" into an nft, defi, or altcoin. Aping refers to a group of people rousing others to collectively pile into projects and takes place widely in twitter and discord communities. The risk in aping or momentum trading is not buying into a trend too late that you're paying a premium, and getting out before the trend is over and you get left "holding the bag" aka facing losses. 

Both my final models were generally strong predictors of price for the typical nft in the collection whereas highest residuals were relegated to those nfts that were the most rare and valuable and departed significantly from the central price tendency of the collection. 

To improve upon my work I would revise my rarity score calculations to implement a system of weights that adds significance to the most rare values for each feature or weights more heavily those features more closely correlated with price or with linear regression coefficients of greatest magnitude. Being that feature engineering was the most impactful method of model improvement I would also spend more time creating new and meaningful predictor variables. I could even go back to the data and try to address some limitations in my collection methods, with collection trading stats being only available to me for the present date, perhaps from alternate sources I could glean more accurate snapshots of price behavior at the time of each individual transaction.

To expand upon my work I would investigate how well these findings transfer to other nft collections. There are so many additional questions to be answered: If momentum explains so much in this case, can a similar analysis be undertaken at scale to predict price for any given collection? Is price for other nft collections driven by the intrinsic value of the digital artwork or future utility of ownership to a greater degree than in this sample case? Can something be learned through analyzing the images of nft's alone in a collection with a neural net, especially newer, cheaper collections that can define a nft-picking strategy that allows a trader to pick up new nft's cheaply based off the perceived relative value or image-classified percentile rarity? Is there an opportunity for arbitrage with a data-driven, momentum based trading strategy in the nft space?