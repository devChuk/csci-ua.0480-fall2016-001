---
layout: homework
title: Final Project Deployment
nav-state: "Final Project Deployment"
---
<style>
pre {
	display: inline-block;
	padding: 9.5px;
	margin: 0 0 10px;
	font-size: 15px;
	word-break: break-all;
	word-wrap: break-word;
	background-color: rgb(224, 229, 234);	
	color: #001446;
	border-radius: 4px;
	border: none !important;
}

#final h3 {
}

section {
	padding-left: 1em;
}
</style>
<div id="final" class="panel panel-default">
	<div class="panel-heading">Final Project</div>
	<div class="panel-body" markdown="block">

## Final Project Deployment

These instructions detail deployment to i6. __If you'd like to deploy elsewhere (your own server, Heroku, GitHub Pages, etc.), please let me know by email / message via NYU Classes.__

Deployment involves the following steps:

1. Finding your assigned port numbers in NYU Classes
2. Logging in to i6 and prepping your home directory
3. Creating a database for your project
4. Getting your project onto i6
5. Installing your project's dependencies
6. Configuring your project
7. Running your project as a daemon


### Part 1: Finding your assigned port numbers in NYU Classes

<section markdown="block">
i6 is a shared server, so other students will be running their projects on it. Consequently, to run your node web application, you'll have run it on a port number that's been assigned to you.

1. Log in to NYU Classes
2. Go to <code>Assignments</code>
3. Click on <code>Final Project Milestone 2</code>
4. Find your NetID in the table
	* The port number listed is for your express application
	* Write this number down... you'll use this in a later step

</section>


### Part 2: Logging in to i6 and prepping your home directory

<section markdown="block">

We'll be using ssh, a commandline tool that allows you to login to and run commands on a remote server, to access i6. Of course, you'll need an i6 account first before you can login, soooo...

1. If you've never logged in to i6 before or if you don't remember your i6 password...
	1. Retrieve the new password you'll use to log in to i6 by going to [https://cims.nyu.edu/webapps/password](https://cims.nyu.edu/webapps/password) ...and entering your netid and password
	2. If you receive an error saying that your account does not exist, email helpdesk@cims.nyu.edu ... and cc me on your correspondence so that I'm aware that you're having account issues
	3. Otherwise, use the i6 password that you received to for the next step
2. Attempt to login to i6 by using ssh (replace <code>your_net_id</code> with your _actual_ net id, all lowercase): 
	
	<pre><code>ssh your_net_id@i6.cims.nyu.edu</code></pre>

3. Once you've logged in, create the following directories in your home directory: 
	* <code>var/log</code> a directory to dump your application logs
	* <code>usr/local/lib</code> to store all of your "global" node modules
	* <code>opt</code> ... as the directory where you'll install your web application
	* run the following command to create the directories specified above:
        <br>
		<pre><code>mkdir -p ~/var/log
mkdir -p ~/usr/local/lib
mkdir ~/opt</code></pre>

4. Verify that these directories exist by running:

	<pre><code>ls ~</code></pre>

	Should show:

	<pre><code>opt  usr  var</code></pre>

</section>

### Part 3: Creating a database for your project

<section markdown="block">

You'll have your own database on an instance of __mongod__ running on i6. You don't have to keep the database running like you do locally; that's already taken care of for you. 

1. [Follow these instructions for initializing your mongoDB database on i6](https://cims.nyu.edu/webapps/content/systems/userservices/databases/class-mongodb)
2. __Make sure you keep the password that's given to you after you follow the above instructions.__ (ideally, in a password safe)
3. To test connectivity, you can try connecting to your database while your ssh'd in to i6:

	<pre><code>module load mongodb-3.2.0
mongo &lt;dbname&gt; --host class-mongodb.cims.nyu.edu -u &lt;username&gt; -p
</code></pre>

4. You should see your MongoDB shell if you're able to successfully connect!

</section>

### Part 4: Getting your project onto i6

<section markdown="block">

1. ssh to i6

	<pre><code>ssh your_net_id@i6.cims.nyu.edu</code></pre>

2. Clone your repository into your opt directory (you can find your full repository name on GitHub). Remember to substitute <code>REPOSITORY_NAME</code> with your actual repository name:

	<pre><code>cd ~/opt
git clone https://github.com/nyu-csci-ua-0480-010-spring-2016/REPOSITORY_NAME</code></pre>

3. Alternatively, you can use <code>sftp</code> or <code>scp</code> to transfer files to i6

</section>

### Part 5: Installing your project's dependencies

<section markdown="block">

Just like local development, you'll have to install your projects dependencies. Replace <code>REPOSITORY_NAME</code> with your project's actual repository name in the following command.

<pre><code>cd ~/opt/REPOSITORY_NAME/ && npm install</code></pre>

</section>

### Part 6: Configuring your project

<section markdown="block">

Edit your <code>db.js</code> file on the server to modify the mongodb connection string. You can use a commandline text editor, like <code>nano</code>, <code>vim</code> or <code>emacs</code>. We'll use <code>nano</code> in these examples. 

1. Open your file for editing by:

	<pre><code>nano db.js</code></pre>

2. Then modify your connection string so that it includes username and password (that you retrieved above):

	<pre><code>mongoose.connect('mongodb://jversoza:my_password@class-mongodb.cims.nyu.edu/jversoza');</code></pre>

3. __Typically, passwords should not be kept in files that are in your source code repository__ ... you _should_ minimally move the username and password out to another file that's not in version control, and construct the connection string based on the external file...

4. Save it by <code>CONTROL+O</code> to _write out_ the file. Press <code>RETURN/ENTER</code> to accept the file name.

5. Quit <code>nano</code> by <code>CONTROL+X</code>

6. Test your application. Substitute <code>APP_PORT_NUMBER</code> with the port number you retrieved from Part 1.

	* Run <code>bin/www</code> or... if you didn't use express generator, use <code>node app.js</code>. __Don't use nodemon to run it.__

		<pre><code>PORT=APP_PORT_NUMBER bin/www</code></pre>

	* If it starts up fine, try connecting to it from your browser: 

		<pre><code>http://i6.cims.nyu.edu:APP_PORT_NUMBER/</code></pre>

6. Troubleshooting:

	* If you see the following error 

		<pre><code data-trim contenteditable>/home/net_id/opt/final-project/node_modules/mongoose/node_modules/mongodb/lib/server.js:235
        process.nextTick(function() { throw err; })
                                            ^
Error: connect ECONNREFUSED
    at errnoException (net.js:905:11)
    at Object.afterConnect [as oncomplete] (net.js:896:19)</code></pre>

		You can't connect to your database; double check your mongoose connection string... and review part 3.

	* If you see <code>Error: listen EADDRINUSE</code> ... the port you've been assigned is already in use. Either your application is already running or someone inadvertently is running there application on your port. Contact me if it's the latter.
	
7. (Optional) If you'd like to __reinstall__ or __redeploy__ your application (again, with <code>REPOSITORY_NAME</code> replaced with your repository name):

	1. <code>rm -rf ~/opt/REPOSITORY_NAME</code>
	2. go back to Part 5

8. (Optional) If you'd like to be able to run <code>git pull</code> to update your code on the server, you could try:
    1. ideally, you'd switch on <code>process.env.NODE_ENV</code> 
    2. in your code, where <code>NODE_ENV</code> could store a value of "prod", "dev", "test", etc. ... and set an environment variable before running, as you do with <code>PORT</code> 
    3. <code>PORT=10000 NODE_ENV=dev node app.js</code>

</section>

### Part 7: Running your project as a daemon

<section markdown="block">

In order to have your project run even when you're not logged in to i6 through an interactive shell, you'll have to run it as a daemon. We'll use a node module called <code>forever</code> to do this. It will run your application in the background and give you tools to manage it (start and stop).

1. Install <code>forever</code>:

	<pre><code>cd ~/usr/local/lib/
npm install forever</code></pre>

2. Run your app with <code>forever start</code>. Substitute <code>APP_PORT_NUMBER</code> with your actual port number. Note the <code>-o</code> and <code>-e</code> options; they specify where your application's output (debug and error output) should go.

	<pre><code>cd ~/opt/final-project/
export PORT=APP_PORT_NUMBER; ~/usr/local/lib/node_modules/.bin/forever -o ~/var/log/app.log -e ~/var/log/app_error.log start bin/www</code></pre>

3. Check that everything started up fine by looking for the process id, and then checking the log files.

	<pre><code>ps aux | grep forever | grep -v grep</code></pre>

	Your application would usually log debug lines and errors to the console. However, since you're running your app as a daemon, all of that output is now dumped into log files, which are in <code>~/var/log</code>. To view the last few lines of the error and app log:

	<pre><code>tail ~/var/log/app.log ~/var/log/app_error.log</code></pre>

4. Listing managed apps and Stopping <code>forever</code> / shutting down your app:

	<pre><code data-trim contenteditable># show managed apps
~/usr/local/lib/node_modules/.bin/forever list
# stop all apps
~/usr/local/lib/node_modules/.bin/forever stopall
</code></pre>

5. Viewing error logs and debug statements.

	For all logs:

	<pre><code>cat ~/var/log/app.log ~/var/log/app_error.log</code></pre>

	For just the last few lines:

	<pre><code>tail ~/var/log/app.log ~/var/log/app_error.log</code></pre>

6. Viewing error logs and debug statements _as they happen_ use the <code>-f</code> flag with <code>tail</code>. __This is super useful for debugging.__ 

	<pre><code>tail ~/var/log/app.log ~/var/log/app_error.log</code></pre>

7. (Optional) Want to have <code>forever</code> accessible in your path? Add <code>PATH=$PATH:home/jjv222/usr/local/lib/node_modules/.bin/</code> to your <code>.bash_profile</code>

</section>

</div>
</div>
