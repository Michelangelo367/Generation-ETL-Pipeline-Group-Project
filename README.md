# ETL Pipeline Project

I've uploaded a Word document named `AWS Screenshots`. This is a file that has all of the screenshots I've used in this README.md, but in better quality

## Overview
Our client has grown and expanded into multiple locations. Due to the demand that the company is receiving, they need to figure out how they can best target new and returning customers, and understand which products are selling well. They are experiencing issues with collating and analysing the data they are producing at each branch, as their technical setup is limited. 

**The Solution**: The plan was to build a fully scalable ETL pipeline to handle large volumes of transaction data for the business. This pipeline would collect all the transaction data generated by each individual café and place it in a single location. By being able to easily query the company's data as a whole, the client would drastically increase their ability to identify company-wide trends and insights. 

## What We Did
* This was a group project, so we discussed as a group how we were going to work at our best - what our ways of working would be, what our definition of done is, and how to get our values aligned. You can read more about this below :)

* Initially, we split up our tasks among ourselves. However, we gradually transitioned into doing all the tasks together, because we wanted to understand every aspect of the project - especially when it came to the AWS tasks.

* We started off our project by creating a schema (found in `schema.md`). Using data normalisation (the process of efficiently organising data in a database) - specifically 3NF, we could see what our tables would look like, what we should remove, and how we should transform our data. The aim was to make querying as straightforward as possible.

![image](https://user-images.githubusercontent.com/73751287/117478851-957dd780-af57-11eb-86dc-8027b8bb76c9.png)

* From there, we created the PostreSQL script to create our tables. This can be found in `sql_script.py` (`src` folder --> `team3etl` folder). We were initially connecting to a local PostreSQL database that ran on Docker, and would move onto AWS later on.

![image](https://user-images.githubusercontent.com/73751287/117479317-176e0080-af58-11eb-888d-41bc021e6fec.png)

* We were given an example CSV file, `2021-02-23-isle-of-wight.csv`, replicating the type of data we would be handling later. The CSV had a lot of PII, so we created a function where we pass in the CSV as a parameter, and the name, card details and card type columns would be removed. It then returns a list of the extracted data.

![image](https://user-images.githubusercontent.com/73751287/117479884-c4e11400-af58-11eb-891b-5c356d8a03df.png)

* We then cleaned this data. Here is a function that replaces empty spaces in the size column with “standard”, and makes sure each row of data has the same format: 

![image](https://user-images.githubusercontent.com/73751287/117480019-f35eef00-af58-11eb-9ef5-e41fae490baf.png)

* We also unit tested these functions - this can be found in our `test` folder.

* After cleaning the data, we were able to populate our tables, confirming that our functions worked. It was time to move to the cloud.

* We received the actual CSVs we would be using for the project, each named `'location'-'date'.csv`. So, we had to refactor our code because these new CSVs had a different format - for example, there were no more missing size values.

* We created our bucket in the `serverless.yml` file. We deployed serverless changes through the command line. 

* Our handle function in `handler.py` (`src` folder --> `team3etl` folder) ran our lambda function. When triggered, the lambda would read from the bucket, and find the new CSV file. The lambda would then call `run_loading.py` (`src` folder) which runs all our code - `sql_script`, `trail.py` and `loading.py`.

### The final Process
* We upload a CSV into our S3 bucket:

![image](https://user-images.githubusercontent.com/73751287/117473622-10dc8a80-af52-11eb-8b66-1a2d77f6b1aa.png)

* This triggers the lambda function:

![image](https://user-images.githubusercontent.com/73751287/117473835-4aad9100-af52-11eb-831a-122631558e16.png)

* We can then check Cloudwatch Logs to monitor what functions are being executed at what time:

![image](https://user-images.githubusercontent.com/73751287/117475836-6dd94000-af54-11eb-99ed-7746beb6af0e.png)

* Our code creates our tables, cleans all the data in the CSV, and inserts the clean data into our Redshift tables:

![image](https://user-images.githubusercontent.com/73751287/117476321-e17b4d00-af54-11eb-8626-79f959f7b00b.png)

* After this, we set up an EC2 instance which ran Grafana on Docker. Grafana is a multi-platform open-source analytics and visualization web application. It provides charts, graphs, and alerts for the web when connected to supported data sources. We only had time to create one table, which showed the most popular products across all stores (which was the chai latte):

![image](https://user-images.githubusercontent.com/73751287/117476920-4afb5b80-af55-11eb-874d-52c6b444ebeb.png)

## If we had more time
* More refactoring
* More unit tests!
* Fix some left over bugs (for example, if uploading multiple CSVs into the S3 bucket, we would get some errors that don't occur when uploading one CSV at a time)
* More data visualisation


## Team 3's Team Values
### Value Alignment 

#### How are we going to balance rest with work?
-	Finish at a certain time – cut off time for work is 5.30pm 
-	Although some people work at different times of day – also people work at different speeds
-	Everyone should feel free to say if they’re fatigued 
-	There’s going to be overtime but not let it happen too often
-	Avoid work on weekends – but sometimes unavoidable  
-	depends on person
-	respect others obligations

#### How are you going to support yourself and each other?
-	Talking, being honest with how you’re feeling, daily check-ins, regular check ins 
-	Cultivate an environment where you can express yourself, don’t shame others for being behind work

#### How are you going to challenge yourself and each other?
-	Ask questions, ask each other about why we did something a certain way, (whether at a certain part or at the end of the week)
-	Learning something new, doing something that makes you uncomfortable

#### How are you going to apply yourselves to the work, what professional standards will you uphold?
-	We don’t have a choice 
-	Apply yourself- don’t procrastinate, don’t waste too much time
-	Set yourself a time limit- if you don’t get something done with a certain time period, you should ask for help
-	Dedicating yourself to best practises for both work and self-care

#### How are you going to decide who works on what?
-	Democracy 
-	Paired programming 
-	When it’s a big task or issue – then we can split the tasks up – otherwise individual working
-	When its task delegation: volunteering, varied work difficulties for each person, see what everyone’s comfortable with. Give 1-5 difficulty levels and if someone has a 5 then try not to give the ticket to them.
-	Perhaps more than one person works on the same ticket – 
-	Sometimes group decision – also if one person is working on architecture then present it to the group then we make a decision as a team.
-	Weekly retros: give each person a chance to explain what they did- good practise for the future. Both for asking and answering questions. 

#### How are you going to deal with conflict? 	
-	Be straight forward – voice your opinion / concern respectfully 
-	Don’t instigate 
-	Talk everything out 


### Definition of Done 

#### What is our definition of ‘Done’?

-	Unit tests passed 
-	Acceptance criteria met (our standards??) (ASK JOAO)
-	Functional tests passed (does it work)
-	Product owner accepts the user story (Joao accepts)
-	README are up-to-date. (ASK JOAO)
-	The solution has been code reviewed or fully pair-programmed.
-	The solution has passed code quality (code analysis) and code style standards (linting) checks. – (will define code quality)


### Setup ways of working

#### How are you going to operate efficiently as a team?
-	Good communication 
-	Review before merge – Joao / drop a message to slack and two people have to approve it
-	Delegation – using 1-5 ratings system for each person’s self-perceived ability

#### Will you have a daily stand up?
-	Yes, talk about daily tasks, anything left over from previous day

#### Will you have retrospectives? / How will you structure your retro?
-	One whenever we finish all the tasks for the week
-	When needed, at least once a week.
-	Written up minutes – reviewed every week.

#### How will you decide to split the workload?
-	Based on difficulty scale and group decision.

#### What methods of communication will you use?
-	Slack, Zoom, WhatsApp group chat

#### Will you use different git branches to create and test features in?
-	Yes

#### Will you make use of GitHub's Pull Requests feature so that other members can review your code?
-	Yes
-	Then delete when merged

