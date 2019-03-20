# DataAnalyticsTutorial

Step 1, Purpose:
> To explain the concepts behind data analytics, in a fun and engaging way. 
> To introduce basic data tools, and what they're useful for.
> To predict who you should put in your march madness bracket. 

Step 2, Requirements:
> Microsoft Excel, Python 3.7, or some other Python. Or Google CoLab!

> Why do we use google colab? Its free, It can be set up in about 4 clicks, its easy to go back and edit stuff, its easy to share, you don't have to download a runtime or editor, and more!

  Setup option 1:
    Download Python:
    https://www.python.org/downloads/
    DO NOT download Python 2, I'll ask you to leave.
    
    On the command line, (terminal on mac, windows powershell or command prompt on windows), use pip3 to install Pandas, MatPlotLib, Scikit.
    Like so:
    pip3 install Pandas
    pip3 install MatPlotLib
    pip3 install SciKit
    
    Open the IDLE editor to a new shell. 
    
  Setup option 2:
    
    Type "Google Colab" into your search bar. 
    Click the first link, sign in, click "new python3 noteboook."
    
    A lot easier than the above, right?
    
    
Step 3, Gameplan:

>What are we going to tackle today? 3 Things. 
  First, make an Elo model for the NHL. 
  Second, learn about statistical distributions, and how real model building works in terms of aggregating rankings. 
    > This is the nitty gritty. Lots of data collection. The better you do this, the better your results will be. 
  Third, build our entire March madness model. This is tough! March madness has less data than the big leagues. 
    
  
Step 4, what are our analytical tools???
  
  A quick intro to analytical tools: 
  
  
    Question, whats a good way to decide how good a team is?
    Wins? Record? Run Differential?
  
    A model commonly used, created by a guy named Elo. 
    
    Statistical analysis in sports is typically regression analysis. Why is this? 
    
    Regression is a way to directly predict an outcome based on an input. 
    
    Usually thats what we're trying to do, predict a win. Or a loss. Or some other outcome. 
    
    Lets try that really quickly. Any Baseball fans? What do you know about Wrigley Field?
    What's Chicago's nickname? 
    
    So lets correlate wind data with something. 
    What should we correlate it with???
    
    Here we go. 
      
Step 5, Data:

  >Your model is SERIOUSLY only as good as its data. 
  >Places to look for data: Kaggle, Reddit.com/r/datasets, statcrunch, BBref, Data.World, namara etc.
  
  Where did I find the baseball dataset?
  Well, I googled it, discovered bbref gets their game data from a source called retrosheet, and they have all of the data listed in CSV files.
  So I downloaded one (https://www.retrosheet.org/gamelogs/index.html), it had no headers. 
  found the headers: https://www.retrosheet.org/gamelogs/glfields.txt
  
  Then I found some weather data:
  https://www.ncdc.noaa.gov/cdo-web/confirmation
  
  
Step 6, a Regression:
  
  Here's what we'll do. Open the O'hare Data:
  
  Expand the columns, Alt+H+O+I
  
  Use GetData in a new worksheet to open the GameData from 2018. Name it bballData.
  
  Col 26: Homeruns Away
  Col 54: Homeruns Home
  Maybe mark home team and away team too? 
  
  Delete columns you don't need, clean it up.
  Use your first formula! Yay! Sum up those homer columns.
  
  Now sort by home team (column 7) and delete everything except for Cubs. 
  This assumes we know every cubs home game was at Wrigley. It was. 
  
  Buttt: were they all played on the day they said they were? Lets make that assumption. It actually was, despite the postponement.
  
  This model is getting worse the more assumptions are in it. 
  
  Now lets fix up our dates (Or I'll fix them up for you) 
  =DATE(ROUND(_GL2018[@Column1]/10000,0),ROUND((_GL2018[@Column1]-20180000)/100,0),_GL2018[@Column1]-20180000-ROUND((_GL2018[@Column1]-20180000)/100,0)*100)
  
  Use that forumla, and don't ask why we're using it!
  
  Then pull the wind data: 
  
  =VLOOKUP([@Column8],OHareDataRequest!$C$2:$L$442,2)
  
  Now you have your two columns! Lets run a regression!
  
  We find somewhat shoddy reliability that this is predictive. 
  
Step 6: Make an Elo model: Python!


  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
