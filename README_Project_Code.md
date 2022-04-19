# Final Project : Evlauating the Movie Business for Microsoft's next big move!


## Overview
The goal of this project is to provide recommendations for Microsoft that will guide them toward the best types of films to make. Using descriptive analysis, this project will show what types of genres, film durations, and which directors are the most profitable. This knolwedge will allow Microsoft to make the best decisions in building their film studio.

# Business Problem
Microsoft is a storied, multi-billion dollar corporation but lacks any experience in the movie industry. Knowing the most profitable genres, film durations, and directors will give Microsoft a much needed advantage in a highly competitive industry.

# Data 
* IMDb (Internet Movie Datbase) contains thousands of films with information on budget, gross, and many other pieces of data.
* I am limiting the data to films from 2012 and after. Why? Most of the relevant industry players were already involved and it wouldn’t be useful to have data before the rise of streaming. 
* This means that we are still analyzing 14 films.

# Methods

* I will use ROI % as the measure of profitability. 
* ROI % is (Gross /Budget)*100.
* Example: A film that grosses $20 mil. on a $5 mil. budget  would have an ROI of 400%. 

# Results

Adventure, Sci-Fi and Comedy are the highest median ROI% among all genres.


![image](https://user-images.githubusercontent.com/101752113/164085999-fd69af05-4b9a-4c25-baf2-67e9b395f931.png)

# Runtime Results Criteria
* Short films=  Less than or equal to 93 minutes.
* 93 minutes= 25th percentile of this dataset.
* Medium Length Films=  Between 80 and 116 minutes.
* Median film length = 103 minutes.
*Long Films=  Over 116 minutes.
*116 minutes= 75th percentile of this dataset.

# Runtime Results

Long films have the highest  median ROI %.


![image](https://user-images.githubusercontent.com/101752113/164087252-e35fd9e7-7ba5-46e7-81d0-83b6701f2fd5.png)





#  Director Criteria

* Again, we are limiting the data to the last 10 years. Therefore, directors who have not made a film in the last 10 years will not appear. So, for example, James Cameron would not make the list since he hasn’t made a film since 2009. If we included films before 2012, we would get many directors who are inactive.

* According to https://www.studiobinder.com/blog/production-budget/, films with budgets below $5 mil. are considered low budget films. Only directors of  films with a budget of $5 mil. or above are considered for this query.
* And yes, this actually does effect the data. If I simply only included films with budgets above $5mil. then a few of the top 5 would not be included. 

# Results
These are the 5 directors with the highest ROI%:

![image](https://user-images.githubusercontent.com/101752113/164087753-81160ba1-7bae-4ea1-9986-0cc088e273ad.png)

# Conclusions

* Recommendation #1: **Make Adventure, Sci-Fi, and Comedy films because they are the most profitable**. 
* Recommendation # 2: **Make long films because they are the most profitable. Their median ROI is almost 250%**.
* Recommendation # 3: **If at all possible, hire M. Night Shyamalan, Christopher Landon, Jordan Peele, Pierre Coffin, or John R. Leonetti because they have the highest ROI% for their films. They have had tremendous success with films with a budget over $ 5mil**.

# Next Steps

* **Comparing the profitability of  films based on pre-existing IP with original films**. Do films based on comics, books, or Tv shows have a  higher ROI % than original films. From the database, it isn't clear which are based on pre-existing IP and which are not. Knowing which type of film is more profitable may inform Microsoft's filmmaking strategies in important ways.

* **Examining profitability of different budget amounts**. It would also be useful for Microsoft to see what what budget amount is optimal.


You can find the Jupyter Notebook and the presentation here: https://github.com/icapeli/icapeli-phase_1_project

![image](https://user-images.githubusercontent.com/101752113/164089050-05fa102f-fb03-444c-a3fc-99d6f91c5b34.png)



