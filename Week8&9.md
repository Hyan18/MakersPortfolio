# Week 8&9

For these two weeks I ended up writing up my thoughts in a blog style format.

These two weeks was our second group project [Acebook](https://github.com/Hyan18/acebook-luckerberg), a Facebook clone developed using Ruby on Rails.

At the beginning of the week our project was kicked off with an introduction to the AGILE development process and the outline of the project. After deciding on our name and setting up github the we were first tasked with estimating the tasks on our Trello board - we decided to use the T-shirt size approach (i.e XS, S, M, L, XL) as for the first time working with eachother, we thought it would be hard to gauge exactly how much time a task could take.

After lunch, we decided to spend a little bit of time researching Rails as although we had all used Ruby before none of us had actually used Rails. We also had to look into ActiveRecord as this was Rails choice of ORM, luckily enough I had used ActiveRecord in my first group project so upon refreshing my memory and taking a look at the rails syntax I was able to explain to my team how an ORM works and how to use ActiveRecord.

The tasks we were given were also given a priority level which affected our decision regarding what tasks to take on first. The highest priority being setting up Continuous Integration and Continuous Deployment pipelines (CI/CD) and also deployment onto Heroku. Since we were a group of 5 we ending up leaving Heroku to one person, user setup to one pair and code style, test coverage, code quality (and then CI/CD) to the other pair. I initially wanted to try setup Heroku but another member of my team also registered interest in it so I chose to set up code style, test coverage and code quality instead.

To enforce code style we used Rubocop as our linter which had Makers configuration and to check test coverage we used SimpleCov. Both of these were pretty simple to setup as we had used them in previous projects and there wasn't too much different in Rails. One thing we hadn't used before was CodeClimate to check our code quality but thankfully it was pretty much straightforward.

Moving on to setting up our choice of CI, Travis. It was also something we hadn't done before but the documentation was easy to follow - we had to make sure to configure it to set up our databases too.
Whilst working on configuring Travis, one of my team members had some trouble with deploying to Heroku. When trying to push to our app's Heroku repository, he was using the command
```
git push heroku master
```
which resulted in the output of something on the lines of everything is up to date but when checking the app's url, our changes did not appear. Upon some googling, we realised that since we were working on another branch we had to use the command (given our branch was called heroku-setup)
```
git push heroku heroku-setup:master
```
indicating that we want to push from our branch heroku-setup to our Heroku repo's master. Without specifying the branch it was trying to push our changes from our master, of which there were none.


Tuesday:
Injured myself climbing on monday night so had to miss the day. (Ended up missing the standups, retro, rails workshop)
Did end up doing some work from home though. Was at this point realised CodeClimate was basically done other than implementing test coverage with Travis.
The next thing we had to do with Travis was to implement auto deployment to Heroku when merging to master and having the checks pass.
This took a lot longer than it should have because one of my team member's set up Heroku so the internal settings there I didn't have that much idea about. I ended up setting up the file correctly but straining over the encryption of the Heroku api key as the error message ended up leading to pages about Travis using .org or .com and having to encrypt it one way or the other depending on where your checks are done. After fiddling with it for a long while we ended up realising that there were some settings in the Heroku app that we needed to change to allow for auto deployment via github. So I ended up also making an account and getting added to the Heroku team to be able to change these settings and it finally worked!

Whilst watching the Travis build progress I ended up seeing a warning about not setting a default web server so upon research I found out that Heroku highly advises to set a default web server (rather than have it be implicit) via a Procfile (their suggested web server being Puma) so I also ended up doing this.

Ended the day with reviewing another pair's work on user authentication which made me want to do some work with Rails as I hadn't really done any yet.

Wednesday & Thursday
Had our first sprint demo in which we learned that the most important aspect is what is on Heroku (user experience). 
Had our first proper retro 

Did a bit of rails when implementing update post functionality

Problems with Heroku authentication (CSRF) and then with sessions.

Friday & Weekend:

New/edit interacts with form, create/update interacts with model
