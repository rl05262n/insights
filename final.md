# WEBSCRAPPING USING TRACKER.GG

### BackGround:What is valorant?

#### VALORANT is a 5v5 character-based tactical FPS(First Person Shooter)developped by riot games. The game has 14 million active monthly users, and 5 million active users in north america. In this project, I webscrapped and pull data from tracker.gg to create models for analysis. (I will be using the riot developper API as well but unfortunately got timed out since I had too many requests) 

##### You can find out more at https://playvalorant.com/en-us/

### I will be using the chrome inspector (control+shift+c) in order to webscrap data from the website Tracker.gg


```python
from IPython.display import Image         #function to display the image 
Image(url="https://learn.circuit.rocks/wp-content/uploads/2020/09/Web-Scraping-using-Python-Featured-1-e1601980783290-750x393.png") #display the image with the png file found using chrome inspector, learned from https://github.com/ipython/ipython/issues/10045
```




<img src="https://learn.circuit.rocks/wp-content/uploads/2020/09/Web-Scraping-using-Python-Featured-1-e1601980783290-750x393.png"/>



#### Lets Begin!

# Setup/early stages of my code: 


```python
# First I Import all the necessary libraries 
import requests              
import numpy as np
import pandas as pd
import pprint
from bs4 import BeautifulSoup
```


```python
#then I define the URL as tracker_url and with a get request, parse the whole HTML page with beautiful soup
tracker_url = 'https://tracker.gg/valorant/leaderboards/ranked/all/default?page=1&act=4cb622e1-4244-6da3-7276-8daaf1c01be2'
tracker_response = requests.get(tracker_url)      # Use requests to retrieve data from a given URL
tracker_soup = BeautifulSoup(tracker_response.text, 'lxml')     # Parse the whole page using BeautifulSoup, conver it into lxml so it is readable
tracker_soup.title.string                                        #get it without the HTML tags to check the titles 
```




    'North America Competitive Ranked Rating for Episode 3 - Act 2 Leaderboard - Valorant Tracker'



##### Now I find the corresponding elements within the chrome inspector  


```python
players=tracker_soup.find_all('span', {'class': 'trn-ign__username'})  #to make sure that everything is correct, I test out the player name first 
ratings=tracker_soup.find_all('td', {'class': 'stat highlight'})         #to double check to see if I got everything correct 
wins=tracker_soup.find_all('td', {'class': 'stat.collapse'})
tracker_table= pd.DataFrame(players)                                    #show the dataframe for the players 
tracker_table                                                           #everything looks good so far      
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>XSET AYRIN</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Crit</td>
    </tr>
    <tr>
      <th>97</th>
      <td>tarik</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NV Marved</td>
    </tr>
    <tr>
      <th>99</th>
      <td>TSM FTX Aleko</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 1 columns</p>
</div>



##### tidying up my code, getting rid of unwanted text and /n


```python
rating=tracker_soup.find('td', {'class': 'stat highlight'})                #check to see if the rating line is clean and concise   
rating
```




    <td class="stat highlight" data-v-0e94bbe2="" data-v-25cf5790=""><img class="player-rank" data-v-0e94bbe2="" data-v-25cf5790="" src="https://trackercdn.com/cdn/tracker.gg/valorant/icons/tiers/24.png"/>
                    1,257
                  </td>




```python
wins=tracker_soup.find_all('td', {'class': 'stat collapse'})                #check to see if the number of wins is clean or I need to make some edits.
wins[1]                                                    #I only set it to one to shorten the list but I see that it is a long list and realize that I have to clean the data up a bit
```




    <td class="stat collapse" data-v-0e94bbe2="" data-v-25cf5790="">
                      128
                    </td>




```python
wins_string = wins[1].get_text()                  #define the wins_string by setting the index to 1 and getting the 
wins_string                                       #print it out 
```




    '\n                  128\n                '




```python
wins=(''.join(wins_string.split()))                 #to get rid of the two placeholders, command joinsplit
(''.join(wins_string.split()))                      #show the result and it is now clean 
```




    '128'



### Problem I encountered:


```python
rankedrating = tracker_soup.select('td.stat.highlight')                               
rating = tracker_soup.select('img.player-rank')  
place=tracker_soup.select('td.rank')
players=tracker_soup.select('span.trn-ign__username')
wins=tracker_soup.select('td.stat.collapse')
links = [a.attrs.get('href') for a in tracker_soup.select('td.username a')]
tracker =[]                                                                             #set trakcer as an empty list          
for i in range(len(players)):                                                           #for all i iin range lens players,
    rankedrating_string = rankedrating[i].get_text()                                    #get the information 
    rankedratings=(''.join(rankedrating_string.split()))                                #command used to join the space and get rid of the /n like above 
    place_string=place[i]                                                               #define the place string as place[index] 
    wins=(''.join(wins_string.split()))                                                 #this is where the problem occurs
    data = {"players": players[i],                                                      #I cannot put i after wins in the data as it would result in an index error  
            "rankedratings": rankedratings,                                             #I finally figured it why after toying with it for 2 hours 
            "wins":wins,                                                                #if i put i there, since there are many wins on the website, but I defined i as len(players)          
            "place":place[i],                                                           #it would result in an error since the for loop states for i in range 
            "rating": rating[i],                                                        #but wins i would be greater and not in range resulting in the error 
            "link": links[i]}                                                           #I am glad that I have experienced that error first hand
    tracker.append(data)                                                                #I feel like I learned alot from it, thats why I left this in so hopefully I don't forget the journey I went through and the solution in the future.     
tracker = pd.DataFrame(tracker)                        
tracker
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>players</th>
      <th>rankedratings</th>
      <th>wins</th>
      <th>place</th>
      <th>rating</th>
      <th>link</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[TSM FTX WARDELL]</td>
      <td>1,257</td>
      <td>128</td>
      <td>[[[&lt;path d="M4.868 13.333H15.13l4.444-8.889h-5...</td>
      <td>[]</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[SoaR Cryo]</td>
      <td>1,242</td>
      <td>128</td>
      <td>[[[&lt;path d="M4.868 13.333H15.13l4.444-8.889h-5...</td>
      <td>[]</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[TSM FTX bang]</td>
      <td>1,183</td>
      <td>128</td>
      <td>[[[&lt;path d="M4.868 13.333H15.13l4.444-8.889h-5...</td>
      <td>[]</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[SoaR zander]</td>
      <td>1,124</td>
      <td>128</td>
      <td>[[4]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[100T Asuna]</td>
      <td>1,120</td>
      <td>128</td>
      <td>[[5]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>[XSET AYRIN]</td>
      <td>788</td>
      <td>128</td>
      <td>[[99]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/laski%239011</td>
    </tr>
    <tr>
      <th>96</th>
      <td>[Crit]</td>
      <td>788</td>
      <td>128</td>
      <td>[[100]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/Young%20Thug%23kenza</td>
    </tr>
    <tr>
      <th>97</th>
      <td>[tarik]</td>
      <td>788</td>
      <td>128</td>
      <td>[[101]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/Troll%23Van</td>
    </tr>
    <tr>
      <th>98</th>
      <td>[NV Marved]</td>
      <td>787</td>
      <td>128</td>
      <td>[[102]]</td>
      <td>[]</td>
      <td>/valorant/profile/riot/12pk%23XANAX</td>
    </tr>
    <tr>
      <th>99</th>
      <td>[TSM FTX Aleko]</td>
      <td>787</td>
      <td>128</td>
      <td>[[103]]</td>
      <td>[]</td>
      <td>https://twitch.tv/12pkk</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 6 columns</p>
</div>



### The solution to the problem above: 
I selected the wrong data. Select the element containing just my data, which is in the format of a table. And instead of making separate list of all the column values in regards to rank, players, ratings etc, and combining them into rows, I just had to reiterate the items representing each data row on the table. Which in this website is the <tr> element and on each row, I just had to build the data dictionary from the specific cells td. And finally, in order to get rid of the header row, I just have to select tbody as my element. Learned from https://stackoverflow.com/questions/20522820/how-to-get-tbody-from-table-from-python-beautiful-soup  https://datascience.stackexchange.com/questions/10857/how-to-scrape-a-table-from-a-webpage and https://python-forum.io/thread-27991.html

# Attempt number 2, using table and for loop 

### Now I add everything together into a for loop


```python
table = tracker_soup.find('table', {'class': 'trn-table'})            #Using the chrome inspector, I found that table.trntable contains all the informations I need. 
players = []                                                          #define table as that by using the find function and setting the term players as an empty list
for row in table.find('tbody').find_all('tr'):                        #Setting a for loop, for each row in table, find the tbody which contains all the data I need and find all element tr
    rank, player, ignore, rating, tier, wins = row.find_all('td')     #the elements are rank, player, I put ignore here since it is a place where you can see the img of the radiant rank and it is the same image 
    wins_string = wins.get_text()                                     #like I did before, I set the parameters so that I can get rid of the annoying /n 
    wins=(''.join(wins_string.split()))                               #Using the join command to join everything together 
    rating_string=rating.get_text()                                   #same as the one for wins above
    rating=(''.join(rating_string.split()))                           #join them together 
    rating=rating.replace(',', '')                                    #replace the commas so that it shows up as 1234 instead of 1,234 taken from https://stackoverflow.com/questions/16233593/how-to-strip-comma-in-python-string
    rank_string=rank.get_text()                                       #because there is ornaments for rank 1 to 3, when transfering the data, I got extra N/ for only rank 1 to 3 so I ran the code for the whole ranks as well to get rid of the first 3
    rank=(''.join(rank_string.split()))                               #same as above, join them together
    data = {"player": player.find('span', {'class': 'trn-ign__username'}).get_text(),  #organize the data, the columns, as player, rank, wins, rating, link 
            "rank": rank,                                                              #I define the find command here specifically because there are two classess one is trn-ign and the other is trn-ign username, defining trn-ign username  
            "wins": wins,                                                              #is the more concise one the other shows unique numbers players put at the end of their usernames
            "rating": rating,
            "link": player.find('a')['href']}                         #the last column I use to get the hyperlink that is inside the table that links to all the player's individual profiles 
    players.append(data)                                              #Add up all the data together with players and define it as a dataframe, df
    df = pd.DataFrame(players)                                 
df                                                                    #show the data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128</td>
      <td>1257</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139</td>
      <td>1242</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150</td>
      <td>1183</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133</td>
      <td>1124</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111</td>
      <td>1120</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>XSET AYRIN</td>
      <td>99</td>
      <td>80</td>
      <td>788</td>
      <td>/valorant/profile/riot/XSET%20AYRIN%231337</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Crit</td>
      <td>100</td>
      <td>151</td>
      <td>788</td>
      <td>/valorant/profile/riot/Crit%23me0w</td>
    </tr>
    <tr>
      <th>97</th>
      <td>tarik</td>
      <td>101</td>
      <td>266</td>
      <td>788</td>
      <td>/valorant/profile/riot/tarik%231337</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NV Marved</td>
      <td>102</td>
      <td>78</td>
      <td>787</td>
      <td>/valorant/profile/riot/NV%20Marved%23ICE</td>
    </tr>
    <tr>
      <th>99</th>
      <td>TSM FTX Aleko</td>
      <td>103</td>
      <td>169</td>
      <td>787</td>
      <td>/valorant/profile/riot/TSM%20FTX%20Aleko%232001</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 5 columns</p>
</div>



### Tidying up the code in attempt number 2:

#### In order to have a "cleaner" code, I add .strip() at the end of each variable in data to get rid of the /n. The result is the same but the codes are cleaner. I discovered only after searching the internet and looking at the forum: https://stackoverflow.com/questions/39870290/how-to-strip-line-breaks-from-beautifulsoup-get-text-method


```python
table = tracker_soup.find('table', {'class': 'trn-table'})                           #define table as that by using the find function
players = []                                                                         #setting the term players as an empty list
for row in table.find('tbody').find_all('tr'):                                       #same as above, with chrome inspector find all the elements 
    rank, player, ignore, rating, tier, wins = row.find_all('td')
    data = {"player": player.find('span', {'class': 'trn-ign__username'}).get_text(), #set the columns for my datasets, same as above 
            "rank": rank.get_text().strip(),                                          #instead now, I add .strip() to get rid of the annoying /n taken from the link above
            "wins": wins.get_text().strip(), 
            "rating": rating.get_text().strip().replace(',', ''),                     #like above,except put. replace right after.strip 
            "link": player.find('a')['href']}
    players.append(data)                                                               #combine the data  
    df1 = pd.DataFrame(players)                                                         #define df as the dataframe of players
df1                                                                                     #show the data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128</td>
      <td>1257</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139</td>
      <td>1242</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150</td>
      <td>1183</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133</td>
      <td>1124</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111</td>
      <td>1120</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>XSET AYRIN</td>
      <td>99</td>
      <td>80</td>
      <td>788</td>
      <td>/valorant/profile/riot/XSET%20AYRIN%231337</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Crit</td>
      <td>100</td>
      <td>151</td>
      <td>788</td>
      <td>/valorant/profile/riot/Crit%23me0w</td>
    </tr>
    <tr>
      <th>97</th>
      <td>tarik</td>
      <td>101</td>
      <td>266</td>
      <td>788</td>
      <td>/valorant/profile/riot/tarik%231337</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NV Marved</td>
      <td>102</td>
      <td>78</td>
      <td>787</td>
      <td>/valorant/profile/riot/NV%20Marved%23ICE</td>
    </tr>
    <tr>
      <th>99</th>
      <td>TSM FTX Aleko</td>
      <td>103</td>
      <td>169</td>
      <td>787</td>
      <td>/valorant/profile/riot/TSM%20FTX%20Aleko%232001</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 5 columns</p>
</div>



# Last/Final attempt with for loop to scrap the player's individual stats. 


```python
tracker_url = 'https://tracker.gg/valorant/leaderboards/ranked/all/default?page=1&act=4cb622e1-4244-6da3-7276-8daaf1c01be2' #define tracker url as the site where the data will be scrapped from. 
tracker_response = requests.get(tracker_url)      # Use requests to retrieve data from a given URL
tracker_soup = BeautifulSoup(tracker_response.text, 'html.parser')     # Parse the whole page using BeautifulSoup, conver it into html so it is readable
tracker_soup.title.string                                        #get it without the HTML tags to check the titles 
```




    'North America Competitive Ranked Rating for Episode 3 - Act 2 Leaderboard - Valorant Tracker'




```python
table = tracker_soup.find('table', {'class': 'trn-table'})                           #same as before,in order to scrape the leaderboard data, use chrome inspector, find the table and define table that contains all the elements 
players = []   #create an empty list for players 
i = 1         #set i=1 so that the variable in the for loop assumes 1 in the first position. https://realpython.com/python-for-loop/ under numberical range loop
for row in table.find('tbody').find_all('tr'):                                       #same as above, with chrome inspector find all the elements 
    rank, player, ignore, rating, tier, wins = row.find_all('td')              #according to the table from chrom inspector, list the elements for the findall function
    data = {"player": player.find('span', {'class': 'trn-ign__username'}).get_text(), #set the columns for my datasets, same as above 
            "rank": rank.get_text().strip(),                                          #instead now, I add .strip() to get rid of the annoying /n taken from the link above
            "wins": wins.get_text().strip(), 
            "rating": rating.get_text().strip().replace(',', ''),                     #like above,except put. replace right after.strip 
            "link": player.find('a')['href']}
    players.append(data)
    df1 = pd.DataFrame(players)                                                               #combine the data                                                     
```


```python
links = df1["link"].tolist()                       #convert the panda dataframe to a list. taken from https://www.dataindependent.com/pandas/dataframe-to-list/in order to run the for loop to scrap all the links, first we have to change up the links so that it is in an http format. 
i = 1             #define our index variable as 1. 
players_stats = []       #create an empty set for players_stats that we will be scrapping 
for link in links:       #start of the forloop, for link(the one that we scrapped above) in the links(the list that we obtained from using the convert to list function in panada)
    player_url = "https://tracker.gg" + link  #define player_url as the new player url with the string tracker.gg infront so that we can directly go to the link in the forloop as the links before started with /
    response = requests.get(player_url)      # Use requests to retrieve data from a given URL taken 
    p_data = response.text                   #define a new variable p_data or player_data as the fucntion response.text which converts the html into text format to see the elements better(notshown here)
    soup = BeautifulSoup(p_data,'html.parser') #define soup using the function to parse the whole page using BeautifulSoup, conver it into html so it is readable
    stats = soup.find('div',{"class":"giant-stats"}) #define the new variable stats with the function to find all the elements within chorme inspector under div giant stats on webpage https://tracker.gg/valorant/profile/riot/TSM%20FTX%20WARDELL%238266/overview since all the pages are the same
    try:                                             #since I encountered some errors while using octoparse, I decided to use the try except argument in python just in case if the data scrap encounters some error. 
        stats = {                                    #set the columns for my datasets stats, same as above, with column urls, the completed links+the strings I made, adr, kd ratio, headshot% and win% as the new columns  
        "urls" : player_url,                         #define the column url as the newly created player_url from above
        "damaged/rounds" : stats.find_all('span',{"class":"value"})[0].text.strip(), #same as before, find all the element that corresponds to the adr bubble in chrome inspector. Use text.strip to get rid of the annoying /n since all of the stats have the same element, use index. Learn from https://stackoverflow.com/questions/63821756/how-to-access-the-second-element-that-has-the-same-class-name-with-python
        "killToDeath ratio" : stats.find_all('span',{"class":"value"})[1].text.strip(), #since they are all under the same title, use [] function to order them from left to right. Here kd is the second so i set [] to 1 find the kd ratio under the second tab of the span class value. 
        "headshot %" : stats.find_all('span',{"class":"value"})[2].text.strip(), #same as before, find all the elements that corresponds to the headshot% 
        "win %" : stats.find_all('span',{"class":"value"})[3].text.strip()} #same for winrate. 
    except:  #exception to the for loop above in case of errors 
        stats = {         
        "urls" : "Not available",
        "damaged/rounds" : "Not available",
        "killToDeath ratio" : "Not available",
        "headshot %" : "Not available",
        "win %" : "Not available",}                    #set all the column as a string of "not available" if there was an error in the forloop. For more information: https://www.pythontutorial.net/python-basics/python-try-except/
    players_stats.append(stats)                        #append the stats table that we created with the previously defined empty function of playerstats.
    stats_df = pd.DataFrame(players_stats)             #define the stats_df as the new two-dimensional heterogeneous tabular data.
df1["Urls"] = stats_df["urls"].tolist()                #set the title for the column of the links that we combined above as a list
df1["Damaged/rounds"] = stats_df["damaged/rounds"].tolist() #set the title of the adr that we scrapped above as a list also 
df1["killToDeath ratio"] = stats_df["killToDeath ratio"].tolist() #set the title of the kd ratio we scrapped above as a list
df1["headshot %"] = stats_df["headshot %"].tolist() #set the title of the headshot percentage we scrapped above as a list
df1["win %"] = stats_df["win %"].tolist() #set the title of the win percent we scrapped above as a list
df1 #show the new dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128</td>
      <td>1257</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>171.2</td>
      <td>1.44</td>
      <td>23.4%</td>
      <td>57.4%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139</td>
      <td>1242</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>172.4</td>
      <td>1.32</td>
      <td>28.8%</td>
      <td>65.5%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150</td>
      <td>1183</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>156.5</td>
      <td>1.17</td>
      <td>39.1%</td>
      <td>53.7%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133</td>
      <td>1124</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>151.1</td>
      <td>1.24</td>
      <td>28.2%</td>
      <td>67.1%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111</td>
      <td>1120</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
      <td>https://tracker.gg/valorant/profile/riot/100T%...</td>
      <td>167.3</td>
      <td>1.19</td>
      <td>26.3%</td>
      <td>57.9%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>XSET AYRIN</td>
      <td>99</td>
      <td>80</td>
      <td>788</td>
      <td>/valorant/profile/riot/XSET%20AYRIN%231337</td>
      <td>https://tracker.gg/valorant/profile/riot/XSET%...</td>
      <td>145.3</td>
      <td>1.09</td>
      <td>20.6%</td>
      <td>56.6%</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Crit</td>
      <td>100</td>
      <td>151</td>
      <td>788</td>
      <td>/valorant/profile/riot/Crit%23me0w</td>
      <td>https://tracker.gg/valorant/profile/riot/Crit%...</td>
      <td>148.9</td>
      <td>1.06</td>
      <td>30.2%</td>
      <td>46.0%</td>
    </tr>
    <tr>
      <th>97</th>
      <td>tarik</td>
      <td>101</td>
      <td>266</td>
      <td>788</td>
      <td>/valorant/profile/riot/tarik%231337</td>
      <td>https://tracker.gg/valorant/profile/riot/tarik...</td>
      <td>166.2</td>
      <td>1.34</td>
      <td>21.4%</td>
      <td>55.7%</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NV Marved</td>
      <td>102</td>
      <td>78</td>
      <td>787</td>
      <td>/valorant/profile/riot/NV%20Marved%23ICE</td>
      <td>https://tracker.gg/valorant/profile/riot/NV%20...</td>
      <td>149.6</td>
      <td>1.01</td>
      <td>30.3%</td>
      <td>40.9%</td>
    </tr>
    <tr>
      <th>99</th>
      <td>TSM FTX Aleko</td>
      <td>103</td>
      <td>169</td>
      <td>787</td>
      <td>/valorant/profile/riot/TSM%20FTX%20Aleko%232001</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>140.0</td>
      <td>1.08</td>
      <td>29.6%</td>
      <td>50.6%</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 10 columns</p>
</div>



### Time to gather even more data by collecting data on the second page


```python
tracker_url = 'https://tracker.gg/valorant/leaderboards/ranked/all/default?page=2&act=4cb622e1-4244-6da3-7276-8daaf1c01be2'       #second page, different url same function
tracker_response = requests.get(tracker_url)      # Use requests to retrieve data from a given URL
tracker_soup = BeautifulSoup(tracker_response.text, 'html.parser')     # Parse the whole page using BeautifulSoup, conver it into lxml so it is readable
```


```python
table = tracker_soup.find('table', {'class': 'trn-table'})                           #define table as that by using the find function
players = []                                                                         #setting the term players as an empty list
i = 1
for row in table.find('tbody').find_all('tr'):                                       #same as above, with chrome inspector find all the elements 
    rank, player, ignore, rating, tier, wins = row.find_all('td')
    data = {"player": player.find('span', {'class': 'trn-ign__username'}).get_text(), #set the columns for my datasets, same as above 
            "rank": rank.get_text().strip(),                                          #instead now, I add .strip() to get rid of the annoying /n taken from the link above
            "wins": wins.get_text().strip(), 
            "rating": rating.get_text().strip().replace(',', ''),                     #like above,except put. replace right after.strip 
            "link": player.find('a')['href']}
    players.append(data)                                                               #combine the data  
    df2 = pd.DataFrame(players)                                                         #define df as the dataframe of players
```


```python
links = df2["link"].tolist()  #convert the panda dataframe to a list. taken from https://www.dataindependent.com/pandas/dataframe-to-list/in order to run the for loop to scrap all the links, first we have to change up the links so that it is in an http format.
i = 1                      #rest are exactly the same as above
players_stats = []
for link in links:
    player_url = "https://tracker.gg" + link
    response = requests.get(player_url)
    p_data = response.text
    soup = BeautifulSoup(p_data,'html.parser')
    stats = soup.find('div',{"class":"giant-stats"})
    try:
        stats = { #set the columns for my datasets stats, same as above, with column urls, the completed links+the strings I made, adr, kd ratio, headshot% and win% as the new columns  
        "urls" : player_url,
        "damaged/rounds" : stats.find_all('span',{"class":"value"})[0].text.strip(),
        "killToDeath ratio" : stats.find_all('span',{"class":"value"})[1].text.strip(),
        "headshot %" : stats.find_all('span',{"class":"value"})[2].text.strip(),
        "win %" : stats.find_all('span',{"class":"value"})[3].text.strip()}
    except: #define the exceptions to the for loop above in case of errors 
        stats = {  
        "urls" : "Not available", #if the for loop runs into an exception, it was display the string not available for each of the columns
        "damaged/rounds" : "Not available",
        "killToDeath ratio" : "Not available",
        "headshot %" : "Not available",
        "win %" : "Not available",}             #set all the column as a string of "not available" if there was an error in the forloop. For more information: https://www.pythontutorial.net/python-basics/python-try-except/
    players_stats.append(stats)            #append the stats table that we created with the previously defined empty function of playerstats.
    stats_df = pd.DataFrame(players_stats)  #define the stats_df as the new two-dimensional heterogeneous tabular data.
 
df2["Urls"] = stats_df["urls"].tolist() #set the title for the column of the links that we combined above as a list
df2["Damaged/rounds"] = stats_df["damaged/rounds"].tolist() #set the title of the adr that we scrapped above as a list also 
df2["killToDeath ratio"] = stats_df["killToDeath ratio"].tolist() #set the title of the kd ratio we scrapped above as a list
df2["headshot %"] = stats_df["headshot %"].tolist() #set the title of the headshot percentage we scrapped above as a list
df2["win %"] = stats_df["win %"].tolist() #set the title of the win percent we scrapped above as a list

df2    #show the new data for page 2, titled df2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>GUARD neT</td>
      <td>104</td>
      <td>158</td>
      <td>786</td>
      <td>/valorant/profile/riot/GUARD%20neT%23zero</td>
      <td>https://tracker.gg/valorant/profile/riot/GUARD...</td>
      <td>146.9</td>
      <td>1.08</td>
      <td>32.4%</td>
      <td>57.1%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>hamburgernochees</td>
      <td>105</td>
      <td>110</td>
      <td>782</td>
      <td>/valorant/profile/riot/hamburgernochees%23king</td>
      <td>https://tracker.gg/valorant/profile/riot/hambu...</td>
      <td>140.2</td>
      <td>1.00</td>
      <td>25.7%</td>
      <td>40.7%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>IMT Rossy x1</td>
      <td>106</td>
      <td>111</td>
      <td>781</td>
      <td>/valorant/profile/riot/IMT%20Rossy%20x1%23Sav</td>
      <td>https://tracker.gg/valorant/profile/riot/IMT%2...</td>
      <td>144.2</td>
      <td>1.07</td>
      <td>31.3%</td>
      <td>46.9%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>LG moose</td>
      <td>107</td>
      <td>85</td>
      <td>779</td>
      <td>/valorant/profile/riot/LG%20moose%23E30</td>
      <td>https://tracker.gg/valorant/profile/riot/LG%20...</td>
      <td>144.4</td>
      <td>1.11</td>
      <td>25.8%</td>
      <td>51.7%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>EccentriK</td>
      <td>108</td>
      <td>70</td>
      <td>779</td>
      <td>/valorant/profile/riot/EccentriK%239658</td>
      <td>https://tracker.gg/valorant/profile/riot/Eccen...</td>
      <td>0.0</td>
      <td>1.63</td>
      <td>0.0%</td>
      <td>44.1%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>RoguePringle</td>
      <td>204</td>
      <td>87</td>
      <td>685</td>
      <td>/valorant/profile/riot/RoguePringle%23NA1</td>
      <td>https://tracker.gg/valorant/profile/riot/Rogue...</td>
      <td>152.4</td>
      <td>1.08</td>
      <td>24.3%</td>
      <td>50.3%</td>
    </tr>
    <tr>
      <th>96</th>
      <td>jstigma</td>
      <td>205</td>
      <td>103</td>
      <td>684</td>
      <td>/valorant/profile/riot/jstigma%23cls</td>
      <td>https://tracker.gg/valorant/profile/riot/jstig...</td>
      <td>0.0</td>
      <td>1.78</td>
      <td>0.0%</td>
      <td>58.1%</td>
    </tr>
    <tr>
      <th>97</th>
      <td>napkin</td>
      <td>206</td>
      <td>91</td>
      <td>684</td>
      <td>/valorant/profile/riot/napkin%23LFT</td>
      <td>https://tracker.gg/valorant/profile/riot/napki...</td>
      <td>132.0</td>
      <td>0.99</td>
      <td>29.2%</td>
      <td>47.7%</td>
    </tr>
    <tr>
      <th>98</th>
      <td>jammyz</td>
      <td>207</td>
      <td>42</td>
      <td>682</td>
      <td>/valorant/profile/riot/jammyz%23NCSM</td>
      <td>https://tracker.gg/valorant/profile/riot/jammy...</td>
      <td>143.3</td>
      <td>1.08</td>
      <td>23.6%</td>
      <td>48.2%</td>
    </tr>
    <tr>
      <th>99</th>
      <td>sym</td>
      <td>208</td>
      <td>66</td>
      <td>681</td>
      <td>/valorant/profile/riot/sym%23xoxo</td>
      <td>https://tracker.gg/valorant/profile/riot/sym%2...</td>
      <td>172.2</td>
      <td>1.24</td>
      <td>33.0%</td>
      <td>54.0%</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 10 columns</p>
</div>



### Gathering data from the third page


```python
tracker_url = 'https://tracker.gg/valorant/leaderboards/ranked/all/default?page=3&act=4cb622e1-4244-6da3-7276-8daaf1c01be2'      #now the third page, different url same function
tracker_response = requests.get(tracker_url)      # Use requests to retrieve data from a given URL
tracker_soup = BeautifulSoup(tracker_response.text, 'html.parser')     # Parse the whole page using BeautifulSoup, conver it into lxml so it is readable
```


```python
table = tracker_soup.find('table', {'class': 'trn-table'})                           #define table as that by using the find function
players = []                                                                         #setting the term players as an empty list
for row in table.find('tbody').find_all('tr'):                                       #same as above, with chrome inspector find all the elements 
    rank, player, ignore, rating, tier, wins = row.find_all('td')
    data = {"player": player.find('span', {'class': 'trn-ign__username'}).get_text(), #set the columns for my datasets, same as above 
            "rank": rank.get_text().strip(),                                          #instead now, I add .strip() to get rid of the annoying /n taken from the link above
            "wins": wins.get_text().strip(), 
            "rating": rating.get_text().strip().replace(',', ''),                     #like above,except put. replace right after.strip 
            "link": player.find('a')['href']}
    players.append(data)                                                               #combine the data  
    df3 = pd.DataFrame(players)                                                         #define df as the dataframe of players
```


```python
links = df3["link"].tolist()          #all codes are the same as the previous two pages, except here it is made for page 3 of the leaderboard.
i = 1
players_stats = []
for link in links: #start of the for loop to first build the complete link and then go into each of the link to scrape data
    player_url = "https://tracker.gg" + link #same as above, to scrap the site we need to add the string infront of the links we scrapped
    response = requests.get(player_url)
    p_data = response.text
    soup = BeautifulSoup(p_data,'html.parser')
    stats = soup.find('div',{"class":"giant-stats"}) #select the corresponding elements in chrome inspect
    try:  #start of the try except clause, exact same as above
        stats = { #define the new columns of the dataset as url, damage, kd ratio, headshot% win%
        "urls" : player_url,
        "damaged/rounds" : stats.find_all('span',{"class":"value"})[0].text.strip(),
        "killToDeath ratio" : stats.find_all('span',{"class":"value"})[1].text.strip(),
        "headshot %" : stats.find_all('span',{"class":"value"})[2].text.strip(),
        "win %" : stats.find_all('span',{"class":"value"})[3].text.strip()}
    except:  #set all the column as a string of "not available" if there was an error in the forloop. For more information: https://www.pythontutorial.net/python-basics/python-try-except/
        stats = {
        "urls" : "Not available",
        "damaged/rounds" : "Not available",
        "killToDeath ratio" : "Not available",
        "headshot %" : "Not available",
        "win %" : "Not available",}
    players_stats.append(stats)  #add the stats with the previously defined dataset of players_stats
    stats_df = pd.DataFrame(players_stats) #like above, define stats_df as the dataframe for players_stats
    
df3["Urls"] = stats_df["urls"].tolist() #like above, give the columns of the new dataset strings/names 
df3["Damaged/rounds"] = stats_df["damaged/rounds"].tolist()
df3["killToDeath ratio"] = stats_df["killToDeath ratio"].tolist()
df3["headshot %"] = stats_df["headshot %"].tolist()
df3["win %"] = stats_df["win %"].tolist()

df3  #show the final page of dataset named df3
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>sussy jakee</td>
      <td>209</td>
      <td>187</td>
      <td>680</td>
      <td>/valorant/profile/riot/sussy%20jakee%23juli</td>
      <td>https://tracker.gg/valorant/profile/riot/sussy...</td>
      <td>151.5</td>
      <td>1.06</td>
      <td>27.8%</td>
      <td>49.1%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>lin</td>
      <td>210</td>
      <td>48</td>
      <td>679</td>
      <td>/valorant/profile/riot/lin%23OTF</td>
      <td>https://tracker.gg/valorant/profile/riot/lin%2...</td>
      <td>0.0</td>
      <td>1.61</td>
      <td>0.0%</td>
      <td>50.9%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>lucasdeeznuts69</td>
      <td>211</td>
      <td>162</td>
      <td>678</td>
      <td>/valorant/profile/riot/lucasdeeznuts69%23ratio</td>
      <td>https://tracker.gg/valorant/profile/riot/lucas...</td>
      <td>153.8</td>
      <td>1.04</td>
      <td>25.4%</td>
      <td>49.5%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Verno YT</td>
      <td>212</td>
      <td>107</td>
      <td>676</td>
      <td>/valorant/profile/riot/Verno%20YT%23Verno</td>
      <td>https://tracker.gg/valorant/profile/riot/Verno...</td>
      <td>137.0</td>
      <td>1.08</td>
      <td>25.6%</td>
      <td>62.3%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bandit</td>
      <td>213</td>
      <td>76</td>
      <td>674</td>
      <td>/valorant/profile/riot/Bandit%23oe7</td>
      <td>https://tracker.gg/valorant/profile/riot/Bandi...</td>
      <td>156.3</td>
      <td>1.20</td>
      <td>21.9%</td>
      <td>54.0%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>Apoth</td>
      <td>309</td>
      <td>57</td>
      <td>616</td>
      <td>/valorant/profile/riot/Apoth%23DOG</td>
      <td>https://tracker.gg/valorant/profile/riot/Apoth...</td>
      <td>149.2</td>
      <td>1.13</td>
      <td>33.5%</td>
      <td>54.8%</td>
    </tr>
    <tr>
      <th>96</th>
      <td>DZ ScrewFace</td>
      <td>310</td>
      <td>91</td>
      <td>616</td>
      <td>/valorant/profile/riot/DZ%20ScrewFace%23MEOW</td>
      <td>https://tracker.gg/valorant/profile/riot/DZ%20...</td>
      <td>145.9</td>
      <td>1.03</td>
      <td>23.0%</td>
      <td>47.1%</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Drink Water</td>
      <td>311</td>
      <td>163</td>
      <td>616</td>
      <td>/valorant/profile/riot/Drink%20Water%235252</td>
      <td>https://tracker.gg/valorant/profile/riot/Drink...</td>
      <td>144.0</td>
      <td>1.09</td>
      <td>26.3%</td>
      <td>51.9%</td>
    </tr>
    <tr>
      <th>98</th>
      <td>WiLD</td>
      <td>312</td>
      <td>86</td>
      <td>615</td>
      <td>/valorant/profile/riot/WiLD%23YAO</td>
      <td>https://tracker.gg/valorant/profile/riot/WiLD%...</td>
      <td>136.6</td>
      <td>1.00</td>
      <td>25.9%</td>
      <td>44.4%</td>
    </tr>
    <tr>
      <th>99</th>
      <td>4fun player</td>
      <td>313</td>
      <td>145</td>
      <td>615</td>
      <td>/valorant/profile/riot/4fun%20player%23bear</td>
      <td>https://tracker.gg/valorant/profile/riot/4fun%...</td>
      <td>142.5</td>
      <td>0.96</td>
      <td>18.7%</td>
      <td>47.2%</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 10 columns</p>
</div>



### Merge the data so that I can export it


```python
valorantranked = [df1, df2, df3]                                 #define the data as all three dataframes 
result = pd.concat(valorantranked)                               #set result using the panda function concat to combine them into one
```


```python
result.to_csv('ranked.csv')                                                      #Export the new dataset into my repo folder with the name "ranked.csv" function so that I can run regressions easier. 
```

## Creating a new variable:


```python
import matplotlib.pyplot as plt                #time to import the matplotlib package that I can use to plot things and run regressions 
import pandas as pd                            #In order to create a new variable, I need to export the data since I cannot do division with strings  
```


```python
df = pd.read_csv('ranked.csv')                  #set the new dataframe by using the command read to read the csv file we exported. 
df.drop('Unnamed: 0', axis = 1, inplace = True) #I saw that during the exporting process, the data created a column of unmaed:0, using thie code gets rid of it taken from https://stackoverflow.com/questions/43983622/remove-unnamed-columns-in-pandas-dataframe
df                                              #show the dataframe 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128</td>
      <td>1257</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>171.2</td>
      <td>1.44</td>
      <td>23.4%</td>
      <td>57.4%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139</td>
      <td>1242</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>172.4</td>
      <td>1.32</td>
      <td>28.8%</td>
      <td>65.5%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150</td>
      <td>1183</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>156.5</td>
      <td>1.17</td>
      <td>39.1%</td>
      <td>53.7%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133</td>
      <td>1124</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>151.1</td>
      <td>1.24</td>
      <td>28.2%</td>
      <td>67.1%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111</td>
      <td>1120</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
      <td>https://tracker.gg/valorant/profile/riot/100T%...</td>
      <td>167.3</td>
      <td>1.19</td>
      <td>26.3%</td>
      <td>57.9%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>Apoth</td>
      <td>309</td>
      <td>57</td>
      <td>616</td>
      <td>/valorant/profile/riot/Apoth%23DOG</td>
      <td>https://tracker.gg/valorant/profile/riot/Apoth...</td>
      <td>149.2</td>
      <td>1.13</td>
      <td>33.5%</td>
      <td>54.8%</td>
    </tr>
    <tr>
      <th>296</th>
      <td>DZ ScrewFace</td>
      <td>310</td>
      <td>91</td>
      <td>616</td>
      <td>/valorant/profile/riot/DZ%20ScrewFace%23MEOW</td>
      <td>https://tracker.gg/valorant/profile/riot/DZ%20...</td>
      <td>145.9</td>
      <td>1.03</td>
      <td>23.0%</td>
      <td>47.1%</td>
    </tr>
    <tr>
      <th>297</th>
      <td>Drink Water</td>
      <td>311</td>
      <td>163</td>
      <td>616</td>
      <td>/valorant/profile/riot/Drink%20Water%235252</td>
      <td>https://tracker.gg/valorant/profile/riot/Drink...</td>
      <td>144.0</td>
      <td>1.09</td>
      <td>26.3%</td>
      <td>51.9%</td>
    </tr>
    <tr>
      <th>298</th>
      <td>WiLD</td>
      <td>312</td>
      <td>86</td>
      <td>615</td>
      <td>/valorant/profile/riot/WiLD%23YAO</td>
      <td>https://tracker.gg/valorant/profile/riot/WiLD%...</td>
      <td>136.6</td>
      <td>1.00</td>
      <td>25.9%</td>
      <td>44.4%</td>
    </tr>
    <tr>
      <th>299</th>
      <td>4fun player</td>
      <td>313</td>
      <td>145</td>
      <td>615</td>
      <td>/valorant/profile/riot/4fun%20player%23bear</td>
      <td>https://tracker.gg/valorant/profile/riot/4fun%...</td>
      <td>142.5</td>
      <td>0.96</td>
      <td>18.7%</td>
      <td>47.2%</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 10 columns</p>
</div>




```python
df['rating'] = df['rating'].astype(float)        #change the dataframe column rating into float so that there is no type conflict when dividing 
df['wins'] = df['wins'].astype(float)            #do the same thing with wins, change the dataframe column wins into a float type for division 
```


```python
df['rating/wins'] = np.where(df['wins'] < 1, df['wins'], df['rating']/df['wins'])         #define the new variable dataframe rating/wins and set the parameter for division to create the new variable
                                                                                          #taken from https://stackoverflow.com/questions/35439613/python-pandas-dividing-column-by-another-column
df                                                                                        #show the new dataframe  
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
      <th>rating/wins</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128.0</td>
      <td>1257.0</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>171.2</td>
      <td>1.44</td>
      <td>23.4%</td>
      <td>57.4%</td>
      <td>9.820312</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139.0</td>
      <td>1242.0</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>172.4</td>
      <td>1.32</td>
      <td>28.8%</td>
      <td>65.5%</td>
      <td>8.935252</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150.0</td>
      <td>1183.0</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>156.5</td>
      <td>1.17</td>
      <td>39.1%</td>
      <td>53.7%</td>
      <td>7.886667</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133.0</td>
      <td>1124.0</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>151.1</td>
      <td>1.24</td>
      <td>28.2%</td>
      <td>67.1%</td>
      <td>8.451128</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111.0</td>
      <td>1120.0</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
      <td>https://tracker.gg/valorant/profile/riot/100T%...</td>
      <td>167.3</td>
      <td>1.19</td>
      <td>26.3%</td>
      <td>57.9%</td>
      <td>10.090090</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>Apoth</td>
      <td>309</td>
      <td>57.0</td>
      <td>616.0</td>
      <td>/valorant/profile/riot/Apoth%23DOG</td>
      <td>https://tracker.gg/valorant/profile/riot/Apoth...</td>
      <td>149.2</td>
      <td>1.13</td>
      <td>33.5%</td>
      <td>54.8%</td>
      <td>10.807018</td>
    </tr>
    <tr>
      <th>296</th>
      <td>DZ ScrewFace</td>
      <td>310</td>
      <td>91.0</td>
      <td>616.0</td>
      <td>/valorant/profile/riot/DZ%20ScrewFace%23MEOW</td>
      <td>https://tracker.gg/valorant/profile/riot/DZ%20...</td>
      <td>145.9</td>
      <td>1.03</td>
      <td>23.0%</td>
      <td>47.1%</td>
      <td>6.769231</td>
    </tr>
    <tr>
      <th>297</th>
      <td>Drink Water</td>
      <td>311</td>
      <td>163.0</td>
      <td>616.0</td>
      <td>/valorant/profile/riot/Drink%20Water%235252</td>
      <td>https://tracker.gg/valorant/profile/riot/Drink...</td>
      <td>144.0</td>
      <td>1.09</td>
      <td>26.3%</td>
      <td>51.9%</td>
      <td>3.779141</td>
    </tr>
    <tr>
      <th>298</th>
      <td>WiLD</td>
      <td>312</td>
      <td>86.0</td>
      <td>615.0</td>
      <td>/valorant/profile/riot/WiLD%23YAO</td>
      <td>https://tracker.gg/valorant/profile/riot/WiLD%...</td>
      <td>136.6</td>
      <td>1.00</td>
      <td>25.9%</td>
      <td>44.4%</td>
      <td>7.151163</td>
    </tr>
    <tr>
      <th>299</th>
      <td>4fun player</td>
      <td>313</td>
      <td>145.0</td>
      <td>615.0</td>
      <td>/valorant/profile/riot/4fun%20player%23bear</td>
      <td>https://tracker.gg/valorant/profile/riot/4fun%...</td>
      <td>142.5</td>
      <td>0.96</td>
      <td>18.7%</td>
      <td>47.2%</td>
      <td>4.241379</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 11 columns</p>
</div>



## Exporting the new dataframe with the new variable as a csv file to excel


```python
df.to_csv('rankednewvar.csv')         #save the file as a new csv file 
```


```python
df = pd.read_csv('rankednewvar.csv')              #set the new dataframe by using the command read to read the csv file
df                                              #show the dataframe to make sure it looks like what we exported.
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>Unnamed: 0.1</th>
      <th>player</th>
      <th>rank</th>
      <th>wins</th>
      <th>rating</th>
      <th>link</th>
      <th>Urls</th>
      <th>Damaged/rounds</th>
      <th>killToDeath ratio</th>
      <th>headshot %</th>
      <th>win %</th>
      <th>rating/wins</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>TSM FTX WARDELL</td>
      <td>1</td>
      <td>128</td>
      <td>1257</td>
      <td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>171.2</td>
      <td>1.44</td>
      <td>0.23</td>
      <td>0.57</td>
      <td>9.820312</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>SoaR Cryo</td>
      <td>2</td>
      <td>139</td>
      <td>1242</td>
      <td>/valorant/profile/riot/SoaR%20Cryo%23Sadge</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>172.4</td>
      <td>1.32</td>
      <td>0.29</td>
      <td>0.66</td>
      <td>8.935252</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
      <td>TSM FTX bang</td>
      <td>3</td>
      <td>150</td>
      <td>1183</td>
      <td>/valorant/profile/riot/TSM%20FTX%20bang%230000</td>
      <td>https://tracker.gg/valorant/profile/riot/TSM%2...</td>
      <td>156.5</td>
      <td>1.17</td>
      <td>0.39</td>
      <td>0.54</td>
      <td>7.886667</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>SoaR zander</td>
      <td>4</td>
      <td>133</td>
      <td>1124</td>
      <td>/valorant/profile/riot/SoaR%20zander%23swagy</td>
      <td>https://tracker.gg/valorant/profile/riot/SoaR%...</td>
      <td>151.1</td>
      <td>1.24</td>
      <td>0.28</td>
      <td>0.67</td>
      <td>8.451128</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
      <td>100T Asuna</td>
      <td>5</td>
      <td>111</td>
      <td>1120</td>
      <td>/valorant/profile/riot/100T%20Asuna%231111</td>
      <td>https://tracker.gg/valorant/profile/riot/100T%...</td>
      <td>167.3</td>
      <td>1.19</td>
      <td>0.26</td>
      <td>0.58</td>
      <td>10.090090</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>295</td>
      <td>295</td>
      <td>Apoth</td>
      <td>309</td>
      <td>57</td>
      <td>616</td>
      <td>/valorant/profile/riot/Apoth%23DOG</td>
      <td>https://tracker.gg/valorant/profile/riot/Apoth...</td>
      <td>149.2</td>
      <td>1.13</td>
      <td>0.34</td>
      <td>0.55</td>
      <td>10.807018</td>
    </tr>
    <tr>
      <th>296</th>
      <td>296</td>
      <td>296</td>
      <td>DZ ScrewFace</td>
      <td>310</td>
      <td>91</td>
      <td>616</td>
      <td>/valorant/profile/riot/DZ%20ScrewFace%23MEOW</td>
      <td>https://tracker.gg/valorant/profile/riot/DZ%20...</td>
      <td>145.9</td>
      <td>1.03</td>
      <td>0.23</td>
      <td>0.47</td>
      <td>6.769231</td>
    </tr>
    <tr>
      <th>297</th>
      <td>297</td>
      <td>297</td>
      <td>Drink Water</td>
      <td>311</td>
      <td>163</td>
      <td>616</td>
      <td>/valorant/profile/riot/Drink%20Water%235252</td>
      <td>https://tracker.gg/valorant/profile/riot/Drink...</td>
      <td>144.0</td>
      <td>1.09</td>
      <td>0.26</td>
      <td>0.52</td>
      <td>3.779141</td>
    </tr>
    <tr>
      <th>298</th>
      <td>298</td>
      <td>298</td>
      <td>WiLD</td>
      <td>312</td>
      <td>86</td>
      <td>615</td>
      <td>/valorant/profile/riot/WiLD%23YAO</td>
      <td>https://tracker.gg/valorant/profile/riot/WiLD%...</td>
      <td>136.6</td>
      <td>1.00</td>
      <td>0.26</td>
      <td>0.44</td>
      <td>7.151163</td>
    </tr>
    <tr>
      <th>299</th>
      <td>299</td>
      <td>299</td>
      <td>4fun player</td>
      <td>313</td>
      <td>145</td>
      <td>615</td>
      <td>/valorant/profile/riot/4fun%20player%23bear</td>
      <td>https://tracker.gg/valorant/profile/riot/4fun%...</td>
      <td>142.5</td>
      <td>0.96</td>
      <td>0.19</td>
      <td>0.47</td>
      <td>4.241379</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 13 columns</p>
</div>



#### Converting the percentages in excel: I opened the excel file and converted all the percentages from the win% and headshot% into decimals for easier time graphing since it is much faster and easier than using astype(float) 

# Graphs:

#### Lineplot: Rank Rating by Rank 


```python
plt.plot(df['rank'],df['rating'])                                #set the parameters for my plot where x is the rank column and y is rating column
plt.xlabel('Rank')                                               #I used the function to add the x label rank
plt.ylabel('Rank Rating')                                         #I used the function to add the y label RankRating
plt.title("Rank Rating by Rank")                                  #use the function to add a title
plt.show()                                                        # Use the plt.show function to display my graph
```


    
![png](output_52_0.png)
    


#### Scatterplot: Ranked rating and total number of games won 


```python
plt.plot('wins','rating', data=df, linestyle='none', marker='.', color = 'b')      #use the command to plot out a scatter graph where the data is taken from the data function defined above and set the color of the dots to blue.
plt.ylabel('ranked rating')                                               #print the y label as ranked rating
plt.xlabel('wins')                                          #print the x label as the amount of wins
plt.title("A scatterplot between ranked rating and total number of games won",fontweight='bold')  #print the title and make the front bold 

plt.show()        #show the scatter plot 
```


    
![png](output_54_0.png)
    


## Combining the two together side by side:


```python
fig, ax = plt.subplots(nrows = 1, ncols = 2, figsize=(20,10))    #combine the fig and axis and make the figure larger by using the command figsize where first number is width and the second number is height (in inches)
ax1 = plt.subplot(1,2,1) #Define the subplot area 1 with 2 rows, 2 columns, with index 1.
ax1.plot(df['rank'],df['rating']) # assigning index 1 graph to be the line graph with the data taken from dataframe of rank and rating
ax2 = plt.subplot(1,2,2) #Define the subplot area 2 with 2 rows, 2 columns, with index 1.
ax2.plot('wins','rating', data=df, linestyle='none', marker='.', color = 'b') #assigning index 2 graph to be scatter plot created earlier with the data taken from dataframe of rank and rating
fig.suptitle('Leaderboard Rank with respect to ranked rating and number of games won', fontsize=16)  #<- here, I added the title for the figure
ax1.set_title('Rank Rating by Rank') #<- Title for subfigure 1
ax1.set_xlabel('Rank on the leaderboard')  #set the x label on ax1 as the rank on the leaderboard
ax1.set_ylabel('Rank Rating')              #set the y label on ax2 as the rank rating
ax2.set_title('Ranked rating and total number of games won') #<- Title for subfigure 2
ax2.set_xlabel('Number of wins')         #set the x label on ax2 as number of wins
ax2.set_ylabel('Rank Rating')         #set the y label on ax2 as rank rating
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'ranknrating.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 500
plt.show()  
```


    
![png](output_56_0.png)
    


#### From the graphs I created, we can see that there exists an obvious correlation between rank on the leaderboard and total rank rating as the number 1 player on the leaderboard, Wardel, has the most amount of rank rating. Moreover, looking at the graph on the right, we can see that, though it is fairly weak, rank rating is positively correlated with number of wins. This somewhat makes sense. The more games you play, if you can maintain a good winrate, the more games you win, the higher your rank rating. What is interesting is that the number 1 player on the leaderboard, Wardel, in this case has merely 128 number of wins, a rather small amount compare to other players. To see if he and other players are more efficient at gaining rank ratings or if there exists a pattern for efficiency, I made a new graph with seaborn. Using the Rating/win we calculated above, we plot that vs rank to see if there is a correlation between how efficient players are at gaining rank ratings. 

### Graphing with seaborn  


```python
import seaborn as sns                                             #import seaborn as sns 
sns.jointplot('rank', 'rating/wins',data=df, kind='reg')          #use the command to make a jointplot, good for regression analysis
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'seaborn.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
```

    C:\Users\owenl\anaconda3\lib\site-packages\seaborn\_decorators.py:36: FutureWarning: Pass the following variables as keyword args: x, y. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.
      warnings.warn(
    


    
![png](output_59_1.png)
    


#### Though not apparent, there exists a correlation between the player rank and rating/wins. It makes sense to an extenet as if some is the rank one player, they are miles ahead of everyone else in terms of mechanics and game knowledge and it is expected that they will be more efficient at winning. Therefore, their ranked rating gained per win should reflect that and be slightly more than someone who is ranked lower.


##### Now to test my hypothesis of better players should have better individual stats with the data gathered by creating the for loop. The axis of the graph is blacked out since some of the data are unavailable and is inconsistent since the data gathered is depedent on cloudflare timing you out or not. But cleaner data can be obtained straight from accessing the tracker.gg API but creating an account

###  Graphs with respect to individual stats and ranked rating(for reference/analysis): since they are all more or less the same I did not put them side by side as they would display similar data and trend.


```python
plt.plot(df['rating'],df['Damaged/rounds'])                                #set the parameters for my plot where x is the ranked rating column and y is average damager per round
plt.xlabel('Ranked Rating')                                               #I used the function to add the x label ranked rating
plt.ylabel('Damaged/rounds')                                         #I used the function to add the y label adr
plt.title("Rank Rating by ADR")                                  #use the function to add a title 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'rankratingbyadr.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()                                                        # Use the plt.show function to display my graph
```


    
![png](output_63_0.png)
    



```python
plt.plot(df['rating'],df['headshot %'])                                #set the parameters for my plot where x is the ranked rating column and y is headshotpercentage in decimals
plt.xlabel('Ranked Rating')                                               #I used the function to add the x label ranked rating
plt.ylabel('Headshot%')                                         #I used the function to add the y label hs
plt.title("Rank Rating by HS%")                                  #use the function to add a title 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'rankratingbyhs.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()                                                        # Use the plt.show function to display my graph
```


    
![png](output_64_0.png)
    



```python
plt.plot(df['rating'],df['killToDeath ratio'])                                #set the parameters for my plot where x is the rank column and y is rating column
plt.xlabel('Rank Rating')                                               #I used the function to add the x label rank rating
plt.ylabel('Kill/Death Ratio')                                         #I used the function to add the y label Kill/Death Ratio
plt.title("Rank Rating and KD")                                  #use the function to add a title 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'rankratingbykd.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()                                                        # Use the plt.show function to display my graph
```


    
![png](output_65_0.png)
    


# ScatterPlots showcasing relationship between different individual stats 


```python
plt.plot('Damaged/rounds','killToDeath ratio', data=df, linestyle='none', marker='.', color = 'b')      #use the command to plot out a scatter graph where the data is taken from the data function defined above and set the color of the dots to blue.
plt.xlabel('Average Damage Per Round')                                               #print the y label as Average Damage Per Round
plt.ylabel('Kill/Death Ratio')                                          #print the y label as the Kill/Death Ratio
plt.title("A scatterplot between Average Damage Done and Kill/Death Ratio",fontweight='bold')  #print the title and make the front bold 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'scatterkdadr.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()        #show the scatter plot 
```


    
![png](output_67_0.png)
    



```python
plt.plot('headshot %','rating/wins', data=df, linestyle='none', marker='.', color = 'r')      #use the command to plot out a scatter graph where the data is taken from the data function defined above and set the color of the dots to blue.
plt.xlabel('Headshot%')                                               #print the y label as Headshot%
plt.ylabel('rating/wins')                                          #print the y label as the rating/wins
plt.title("A scatterplot between rating/wind and Headshot%",fontweight='bold')  #print the title and make the front bold 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'scatterhsrating.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()        #show the scatter plot 
```


    
![png](output_68_0.png)
    



```python
plt.plot('killToDeath ratio','rating/wins', data=df, linestyle='none', marker='.', color = 'g')      #use the command to plot out a scatter graph where the data is taken from the data function defined above and set the color of the dots to green.
plt.xlabel('Kill/Death Ratio')                                               #print the y label as Kill/Death Ratio
plt.ylabel('Rating/wins')                                          #print the y label as the Ratingperwin
plt.title("A scatterplot between Ratings gained per win and Kill/Death Ratio",fontweight='bold')  #print the title and make the front bold 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'scatterkdratingwin.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()        #show the scatter plot 
```


    
![png](output_69_0.png)
    



```python
plt.plot('Damaged/rounds','rating/wins', data=df, linestyle='none', marker='.', color = 'm')      #use the command to plot out a scatter graph where the data is taken from the data function defined above and set the color of the dots to blue.
plt.xlabel('AverageDamage')                                               #print the y label as AverageDamage
plt.ylabel('Ratings gained Per win')                                          #print the y label as the Ratingperwin
plt.title("A scatterplot between Ratings gained per win and Average Damage per round",fontweight='bold')  #print the title and make the front bold 
path ='/Users/owenl/Documents/GitHub/RL/'  #define the location of where we want to save the image in
plt.savefig(path+'scatterratinggainadr.png', dpi = 800)   #save the graph as an png with dots per inch the resolution being 800
plt.show()        #show the scatter plot 
```


    
![png](output_70_0.png)
    


# What I realized after making the different graphs:  

## It is clear, especially when looking at the linegraphs on individual statistics and ranked rating, that there isn't really a correlation between the best player, wardell, vs the "worst" player in the ladder since each player may play a different way, have a different playstyle that corresponds to their personality, etc. Many top players have negative KD, more death than kills, yet they are ahead of the pack on the ranked ladder. This means that individual stats have no bearing in terms of winning on a team and other factors such as the mood or environment matters more.

### For coefficient and interpretation, please look through the R notebook.  
