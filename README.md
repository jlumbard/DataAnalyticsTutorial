# Data Analytics Tutorial

## **Step 1**: Purpose

- To explain the concepts behind data analytics, in a fun and engaging way.
- To introduce basic data tools, and what they're useful for.
- To predict who you should put in your march madness bracket.

## **Step 2**: Requirements
- Microsoft Excel
- Python 3.7
- Or, Google CoLab!

Why do we use google colab? Its free, It can be set up in about 4 clicks, its easy to go back and edit stuff, its easy to share, you don't have to download a runtime or editor, and more!

### Setup option 1:

[Download Python here.](https://www.python.org/downloads/) 
DO NOT download Python 2, I'll ask you to leave.

On the command line, (terminal on mac, windows powershell or command prompt on windows), use pip3 to install Pandas, MatPlotLib, Scikit.
Like so:
> pip3 install Pandas

> pip3 install MatPlotLib

> pip3 install SciKit

Open the IDLE editor to a new shell.

### Setup option 2:

1. Type "Google Colab" into your search bar.
2. Click the first link, sign in, click "new python3 noteboook."

A lot easier than the above, right? Those packages are already installed.

## **Step 3**: Gameplan

What are we going to tackle today?
1. First, make an Elo model for the NHL.
2. Second, learn about statistical distributions, and how real model building works in terms of aggregating rankings. This is the nitty gritty. Lots of data collection. The better you do this, the better your results will be.
3. Third, build our entire March madness model. This is tough! March madness has less data than the big leagues.


## **Step 4**: What are our analytical tools?

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

## **Step 5**: Data

Your model is SERIOUSLY only as good as its data.
Places to look for data: Kaggle, Reddit.com/r/datasets, statcrunch, BBref, Data.World, namara etc.

Where did I find the baseball dataset?
Well, I googled it, discovered bbref gets their game data from a source called retrosheet, and they have all of the data listed in CSV files.
So I downloaded one (https://www.retrosheet.org/gamelogs/index.html), it had no headers.
found the headers: https://www.retrosheet.org/gamelogs/glfields.txt

Then I found some [weather data](https://www.ncdc.noaa.gov/cdo-web/confirmation)


## **Step 6**: A Regression

Here's what we'll do. Open the O'hare Data:

Expand the columns, Alt+H+O+I

Use GetData in a new worksheet to open the GameData from 2018. Name it bballData.

Mark two columns:
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

  > =DATE(ROUND(_GL2018[@Column1]/10000,0),ROUND((_GL2018[@Column1]-20180000)/100,0),_GL2018[@Column1]-20180000-ROUND((_GL2018[@Column1]-20180000)/100,0)*100)

Use that forumla, and don't ask why we're using it!

Then pull the wind data:
  > =VLOOKUP([@Column8],OHareDataRequest!$C$2:$L$442,2)

Now you have your two columns! Lets run a regression!

We find somewhat shoddy reliability that this is predictive.

## **Step 6**: Make an Elo model: Python!

  A Quick intro to Python.

Here we are, finally. So open your editor.

What are we using today?
  - Pandas - dataframes, excel sheets for python
  - MatPlotLib - a graphing library
  - SKLearn - an analytics library, using their regression!

First thing we need to do is get our data in. We're going to start with NHL data, which I found on hockeyreference.com.
Once we get it down with one dataset it will be easy in another.

  Open our data:
  In IDLE:
  > import pandas as pd
  
  > csv = pd.read_csv('path_to_csv')
  csv = pd.read_csv('https://raw.githubusercontent.com/jlumbard/DataAnalyticsTutorial/master/201819NHLGameData.csv')

  In Google Colab:
  > csv = pandas.read_csv('link_to_Raw_csv_on_Brock's_Github')

  once you have that, do something like this just to check the data you have:
    print(csv)

  set some default Elo values:
  > csv['HomeELO'] = 1500
  
  > csv['AwayELO'] = 1500
  
  > csv['HomeExpectedGoals'] = 0
  
  > csv['AwayExpectedGoals'] = 0
  
  > csv['HomeNewElo'] = 1500
  
  > csv['AwayNewElo'] = 1500

  make the basic Elo functions:
    def expectedScore(meanScore, teamRating, opponentRating):
      return meanScore/(1+math.pow(10,(opponentRating-teamRating)/400))

    def NewRating(Rating, K, Score, expectedScore):
      return Rating + K*(Score-expectedScore)


    This actually does the meat of the function:

    for index, row in csv.iterrows():
    TeanDict = {}

	if row['Visitor'] not in TeamDict:
		TeamDict[row['Visitor']] = row['AwayNewElo']
	if row['Home'] not in TeamDict:
		TeamDict[row['Home']] = row['HomeNewElo']

	a=csv.set_value(index,'HomeELO', TeamDict[row['Home']])
	a=csv.set_value(index,'AwayELO', TeamDict[row['Visitor']])
	a=csv.set_value(index,'AwayExpectedGoals', expectedScore(meanGoals,row['AwayELO'],row['HomeELO']))

	a=csv.set_value(index,'HomeExpectedGoals', expectedScore(meanGoals,row['HomeELO'],row['AwayELO']))
	a=csv.set_value(index,'AwayExpectedGoals', expectedScore(meanGoals,row['AwayELO'],row['HomeELO']))

	if(row['HomeGoals'] >= 0):
		a=csv.set_value(index,'HomeNewElo', NewRating(row['HomeELO'],20,row['HomeGoals'],row['HomeExpectedGoals']))
		a=csv.set_value(index,'AwayNewElo', NewRating(row['AwayELO'],20,row['VisitorGoals'],row['AwayExpectedGoals']))
		TeamDict[row['Visitor']] = row['AwayNewElo']
		TeamDict[row['Home']] = row['HomeNewElo']
	print(str(index) + row['Home'])

  You're done your Elo.
    Do this:
      csv.to_csv("c:/users/brock/desktop/newTest.csv")
    Open with Excel.


## **Step 7**: Putting It All Together

  All the data sources:
  - [BPI](http://www.espn.com/mens-college-basketball/bpi)
  - [Pomeroy](https://kenpom.com/)
  - [Sagarin](http://sagarin.com/sports/cbsend.htm)
  - [Moore](https://sonnymoorepowerratings.com/m-basket.htm)
  - [LRMC](https://www2.isye.gatech.edu/~jsokol/lrmc/) (Has Gonzaga first!!!!)
  - Plus the 538 Elo, Which isn't made available.


  We're going to use all except for the LRMC, and no Elo. Why? We can add one later if we want to.

  Load up the NCAA csv, which lists rankings in the above.

    csv = pandas.read_csv('c:/users/brock/desktop/NCAADATA.csv')
    x = csv.plot('KenPom', 'First')
    plt.show()

    #make a linear regression object

    from sklearn import datasets, linear_model
    regr = linear_model.LinearRegression()

    x = csv.BPI.values
    y = csv.First.values

    length = 64
    
    x = x.reshape(length, 1)
    y = y.reshape(length, 1)

    regr.fit(x,y)

    Odds we win first round?

    regr.predict([[5]])

    Do it with multi variables:
    x = csv[['BPI', 'Moore', 'KenPom', 'Sagarin']].values.reshape(-1,4)

    regr.fit(x,y)
    regr.predict([[1,1,1,1]])
