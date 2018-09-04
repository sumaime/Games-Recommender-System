# Games Recommender System Background


## Summary
The game industry belongs to entertainment industry, which is growing rapidly. Video games ranked as the 4th most spending field according to PWC US outlook for entertainment & media segments. Steam is the largest digital distribution platform for PC gaming and has over 100m active users on platform. 

In this project, we scraped the game and users’ information through Steam API and using different recommendation models to create lists games the users most likely to play. 


## Models
We consider three recommendation models here.

### 1.	Collaborative filtering: Find the most relative product through grouping the purchase activities with users.

•	Input: usage data(rating, score, purchase or not, download or not, time spend on the product, click rate)

•	Rationale: It can be user-based, which recommend based on what similar users play, or item-based, which recommend based on similarity between products. 

•	Advantage: only need usage data, do not need domain knowledge, do not need to consider products’ feature.

•	Disadvantage: Model can only use usage data. However, new users do not have usage data so that cannot define preference(cold start). Also, some products need to be standardized. For example, all Iphone6 are the same but different on colors. Moreover, if there is not enough product to recommend or one product is purchased by too many users(popularity bias), the model does not work well.


### 2.	Content-based filter: Recommend through description and commends. 

•	Input: text, description.

•	Rationale: TF-IDF (frequency weight)

•	Advantage: Solve the problem of cold start and popularity bias. Only need users provide metadata.

•	Disadvantage: The language of  description needs to be readable. The model is very easy overly recommend similar products and lead to a pigeonhole and make users get bored.


### 3.	Popularity approach: Recommend the most played or recommend products to users

•	Advantage: easy to use.

•	Disadvantage: update extremely slow if there is not enough products.


## Process

### Data ETL

•	User IDs: Load the text file of steam users ID, using ‘with open’.

•	GetOwnedGames: Using users ID to crawl user owned games’ information, which contains the game user played and how long the user played, from steam API. Store the info as user_inventory.txt.

•	APP IDs: Get as much app ids as possible from http://steamwebapi.azurewebsites.net/. Using http://api.steampowered.com/ISteamApps/GetAppList/v2/ to crawl app IDs. 

•	Detailed app info: Using appIDs to scrape detailed app information, including price, pictures, name, score, type, release date, etc. http://store.steampowered.com/api/appdetails?appids=

•	App descriptions: Using appIDs to scrape detailed app information. http://store.steampowered.com/api/appdetails?appids=

•	App Using Details: using app IDs to scrape using details from SteamSpy http://steamspy.com/api.php?request=appdetails&appid=


### Model Building

#### •	Model 1: Popularity / Recommend based on most usage

o	Get each game’s owner number, the most popular game has the largest number of users using info of App Using Details.

o	Rank by owners so that get the most popular games

#### •	Model2: Content-based / Recommend based on description

o	Get the description of each game, using TfidfVectorizer to count the frequency of each word and add weight transformer to make the description more balanced.

o	Use linear_kernel to calculate the similarity between each description（each row） and the whole description bag.

o	Choose the most similar 10 for each description and save to df.

#### •	Model3: Item-based / Recommend product based on the similarity between products.

o	Extract all games user played from user_inventory, if played, marked as 1.

o	Calculate the linear similarity between each game and the whole games according to which has been played by users


### Validation

Since we cannot have the users data in the coming several months and it takes time to test whether the model is correct, here we extract some users and check if they played similar games as a validation.

Now we have 3 tables of recommendation: 

• popularity_based_result

• content_based_result

• item_based_result

Based on users'favorite game, we can make recommendation.

#### Test example
For example, if the user 76561197960422789's most played game is 730 (Counter-Strike: Global Offensive, a shooter game), we recommend

•	Top 5 popular: Team Fortress 2, Dota 2, Counter-Strike: Global Offensive, Warframe, PLAYERUNKNOWN'S BATTLEGROUNDS

•	Content_based: Counter-Strike: Condition Zero, Counter-Strike: Source, Counter-Strike Nexon: Zombies, DARIUSBURST Chronicle Saviours

•	item_based: Left 4 Dead 2, Garry's Mod, PAYDAY 2, Warframe, Dead Realm

We can from the example that people who play CS may be willing to play:

•	similar games like CS (other series) based on content-based recommendation

•	or other shooter games but with some cartoon or fantacy elements based on item-based recommendation

Noted that the item-based recommendation also recommends games from popularity tables, which indicates that people are willing to give a try on the most popular games.
Combining the three models together can avoid problems such as cold start (no data to make recommendation) and pigeon holes (keep recommending the similar games) so that increasing users loyalty to platform.


Citation

https://www.analyticsvidhya.com/blog/2016/06/quick-guide-build-recommendation-engine-python/
https://www.analyticsvidhya.com/blog/2018/06/comprehensive-guide-recommendation-engine-python/
https://www.kaggle.com/gspmoreira/recommender-systems-in-python-101

