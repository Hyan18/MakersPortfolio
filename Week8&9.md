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

I ended up injuring my foot on monday night bouldering so ended up being unable to go into the office on tuesday. I did however still end up doing some work from home as CI/CD could be done pretty independently. The next thing to do was setting up Travis to autodeploy to heroku after a merge to master with all checks passing, This took a lot longer than it should have because one of my team member's set up Heroku so the internal settings there I didn't have that much idea about. I ended up setting up the file correctly but straining over the encryption of the Heroku api key as the error message ended up leading to pages about Travis using .org or .com and having to encrypt it one way or the other depending on where your build is completed - I used the following command to encrypt and add the api key to the travis.yml file
```
travis encrypt [API KEY HERE] --org --add deploy.api_key
```
After fiddling with it for a long while we ended up realising that there were some settings in the Heroku app that we needed to change to allow for auto deployment via github. So I ended up also making an account and getting added to the Heroku team to be able to change these settings and it finally worked!
Whilst watching the Travis build progress I ended up seeing a warning about not setting a default web server so upon research I found out that Heroku highly advises to set a default web server (rather than have it be implicit) via a Procfile (their suggested web server being Puma) so I also ended up doing this.

Ended the day with reviewing another pair's work on user authentication which made me want to do some work with Rails as I hadn't really done much with it yet.

The next day we had our first sprint demo before which we had a discussion on whether we should deploy some unfinished work onto production to show a bit more off to our product owner which we ended up deciding against. The demo ended up going quite well and we learnt that we made the right decision as the product owner reiterated that the most important aspect is the user experience so what is in production (on Heroku) should be up to a good standard. In our retrospective afterwards, my other team members were happy that me and another team member persuaded them into not deploying unfinished work into production and expressed that overall the sprint went quite well despite the learning curve of Rails.

I ended up doing my first bit of rails when implementing the U of CRUD, update, for posts. We thought we finished it quite quickly until we realised that there were additional accceptance critera in the description of the Trello card, one specifically being that ```users should be unable to edit posts after 10 minutes```. At first we were a bit stumped on how to test this but ended up remembering a gem we had encountered before, TimeCop. With this gem we were able to "travel in time" for the test to check the ability to edit. The next issue we encountered was where to place the logic for checking if the time had expired yet as there were two methods relating to updating a post, edit and update. We found out that the methods new/edit interacts with the form and create/update interacts with the model so the correct place to put it was in the edit method. With our test passing we ended up also taking a well earned 10 minute break to see successful in development.

The next problem was one which cause a great deal of frustration. When checking our app on Heroku there was an error message so we checked our logs and it came back with something about CSRF which upon some research we realised we needed to include a line in our application controller to fix this ```protect_from_forgery with: :exception```. Then we encountered another error with Heroku, signup and login would not work. We thought this was an issue to do with sessions on Heroku as the logs came back with something about a user_id being undefined. We ended up trying a vareity of solutions such as MemCachier but to no avail. My team member who set up Heroku ended up giving up on it altogether, he was quite stressed out about this so I ended up taking some time myself to research Heroku and more carefully look at the logs. My suspection was that upon adding a user_id to a post so we could display who was the owner of a post there came an error since there were some posts made before we actually added this functionality so these posts without user_id's were causing the error. I was trying to fix this issue over the weekend so since I couldn't interact with the database of our app owned by my team member I tried creating another app to push our repo to (since this would also fix the suspected problem by recreating the database)...
It worked! A lot of hassle because we weren't reading the logs carefully enough - lesson learnt.

Upon completing our initial requirements we got our new set of requirements which focused on two features - walls and comments. I ended up pairing on comments which used Rails nested resources this meant that in our routes we had the following so we could use rails route helpers
```
resources :posts do
  resources :comments
end
```
and we also used ActiveRecord associations so we could use the build method when creating our comments. Most of this was easy enough but one of the further challenges was making it so you could make comments on a post without having the page refresh, which involved using AJAX requests. My pair and I thought this would be quite interesting to do so we went ahead to try it.
First of all, we had to get the form inline on the posts page which involved having to render partials - this essentially allowed us to render a view within another view. 
```
<%= render partial: 'comments/form.html', locals: { post: post, comment: post.comments.build } %>
<%= render partial: 'posts/comment.html', locals: { post: post }, collection: post.comments.order("created_at DESC") %>
```
The first is the partial of the form in which we pass in the post and new comment and the second is a partial containing all the comments for the given post.

Once we got that working we researched how to use AJAX within rails and upon research we found that we can add an option to our forms to make this possible ```remote: true```. This meant that the submit button would go through the action of the form but not refresh the page, we then needed to set it in our controller to respond to a .js.erb file rather than a .html.erb file which was done by adding the following to our create method in our controller
```
respond_to do |format|
  format.js
end
```
We then had to create a create.js.erb file in which contained our javascript (jquery in particular) which handled the submition of the form by re-rendering the form and collection of comments without refreshing the page.
```
$("#<%= @post.id %>_partial").html("<%= j render partial: 'posts/comment.html', locals: { post: @post }, collection: @post.comments.reverse %>");
$("#<%= @post.id %>_comment_form").html("<%= j render partial: 'comments/form.html', locals: { post: @post, comment: @post.comments.build } %>");
```
It was extremely satisfying to see this working on our app and so we ended up also doing something similar for editing and deleting comments.
One thing which we had problems with was that we wrote feature tests for each of these and they would fail with an unidentified format error so we had to add ```format: :js``` to our forms and buttons for rspec to recognise this.
Another thing with the tests was that it was necessary to use another driver for testing javascript so we had to implemeent Selenium Web Driver. We also had to configure Travis to use a custom webdriver in our build.

As time ran down we started to do a little bit of CSS and came across an issue of the CSS not showing up on Heroku. After some googling we found that we had to precompile our assets locally and then push to github which we did using the following command 
```
RAILS_ENV=production bundle exec rails assets:precompile
```
After merging everything together we realised that we had no user friendly way to navigate to a user's wall (you had to edit the url) so I quickly added an index page containing a list of all signed up users which were hyperlinked to their wall.

When it came to our presentation we were praised by how fluid the app worked and suggestion was to share more about our journey as a team.

Overall, these two weeks were quite tough but very enjoyable. Some things which I will keep in mind for our next group project (the final one) are:
  * keep a diary of sorts to better keep track of our journey
  * have a 'merge party' where the people who wrote the code explain to the others line for line what each part means
  * try have a theme to create a better team identity
