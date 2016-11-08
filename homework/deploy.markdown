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

These instructions detail deployment to i6. __If you'd like to deploy elsewhere (your own server, Heroku, AWS, etc.), please let me know by email / message via NYU Classes.__ Heroku has been an option that students have used in the past because of the good [introductory documentation](https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction), and [deployment tutorial](https://devcenter.heroku.com/articles/deploying-nodejs).

Deployment __to i6__ involves the following steps:

1. Finding your assigned port numbers in NYU Classes
2. Logging in to i6 and prepping your home directory
3. Creating a database for your project
4. Getting your project onto i6
5. Installing your project's dependencies
6. Configuring your project
7. Running your project as a daemon
8. Reinstalling and/or redeploying


### Part 1: Finding your assigned port numbers in NYU Classes

<section markdown="block">
i6 is a shared server, so other students will be running their projects on it. Consequently, to run your node web application, you'll have run it on a port number that's been assigned to you.

1. Log in to NYU Classes
2. Go to <code>Assignments</code>
3. Click on <code>Milestone 2</code>
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

### Part 4: Using an external configuration file for your database

<section markdown="block">
Because the mongodb server you'll connect to from i6 requires user authentication, the string you pass in to `mongoose.connect` (your __database connection string__) will have to include the username and password you were given from the previous section, Part 3. The new connection string will have the following format:

<pre><code data-trim contenteditable>mongodb://USERNAME:PASSWORD@class-mongodb.cims.nyu.edu/USERNAME
</code></pre>

Where:

* `USERNAME` - is the username you used for logging it to i6
* `PASSWORD` - is the password for __mongodb__ that you created from Part 3.

However, you should not put these credentials directly into your `db.js` file, and they should not be in a file in version control (you may inadvertently disclose these credentials if your repository becomes public). One way to deal with this issue is to put your credential in an external file that is conditionally read:


1. add a conditional to your database configuration code that...
2. checks if an environment variable named `NODE_ENV` is set to `PRODUCTION`
    * [read the excellent digital ocean summary regarding environment variables](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)
    * use [process.env.NAME_OF_VARIABLE] to access environment variables through node
3. if the above is true, then read a file synchronously (blocking) by using [fs.readFileSync](https://nodejs.org/api/fs.html#fs_fs_readfilesync_file_options)
4. the file that is read in will be a `json` file that's not in version control... that contains the database connection string for your application when deployed on i6

(Note that this is a simple way of managing configuration, so if you plan on _actually_ deploying your app outside of this class, consider using a configuration / configuration management library, like [node-convict](https://github.com/mozilla/node-convict))

__To add an external configuration file, follow these steps__ &rarr;

1. add `config.json` to your `.gitignore` so that your credentials don't inadvertently get committed
2. in `db.js` add the following code before `mongoose.connect`:
    <pre><code data-trim contenteditable>// is the environment variable, NODE_ENV, set to PRODUCTION? 
if (process.env.NODE_ENV == 'PRODUCTION') {
    // if we're in PRODUCTION mode, then read the configration from a file
    // use blocking file io to do this...
    var fs = require('fs');
    var path = require('path');
    var fn = path.join(__dirname, 'config.json');
    var data = fs.readFileSync(fn);

    // our configuration file will be in json, so parse it and set the
    // conenction string appropriately!
    var conf = JSON.parse(data);
    var dbconf = conf.dbconf;
} else {
    // if we're not in PRODUCTION mode, then use
    dbconf = 'mongodb://localhost/YOUR_DATABASE_NAME_HERE';
}
</code></pre>
3. when you use `mongoose.connect`, pass in `dbconf` as the argument instead of a hardcoded string
4. you can test that everything works by:
    * try running your application without any environment variables (just use `./bin/www`)
        * ... and inserting data
        * via your form
        * (make sure the data persists)
    * create a `config.json` that contains a connection string with a different database name
        * the key should be `"dbconf"` (remember that json keys are double quoted)
        * the value should be `"mongodb://localhost/SOME_OTHER_DATABASE_NAME"`
    * then, run your application again, this time forcing your app to use the config file
        * `NODE_ENV=PRODUCTION ./bin/www`
    * the new database shouldn't have any data that you entered previously!
    * __DO NOT COMMIT__ `config.json` (in fact, it should be in your `.gitignore` as the previous instructions specify)
    * (you'll create a `config.json` on i6)
5. commit and push your code


</section>

### Part 5: Getting your project onto i6

<section markdown="block">

1. ssh to i6

	<pre><code>ssh your_net_id@i6.cims.nyu.edu</code></pre>

2. Clone your repository into your opt directory (you can find your full repository name on GitHub). Remember to substitute <code>REPOSITORY_NAME</code> with your actual repository name:

	<pre><code>cd ~/opt
git clone https://github.com/nyu-csci-ua-0480-010-spring-2016/REPOSITORY_NAME</code></pre>

3. Alternatively, you can use <code>sftp</code> or <code>scp</code> to transfer files to i6

</section>

### Part 6: Installing your project's dependencies

<section markdown="block">

Just like local development, you'll have to install your projects dependencies. Replace <code>REPOSITORY_NAME</code> with your project's actual repository name in the following command.

<pre><code>cd ~/opt/REPOSITORY_NAME/ && npm install</code></pre>

</section>

### Part 7: Configuring your project

<section markdown="block">

Create a <code>config.js</code> file on the server to add the `PRODUCTION` version of your mongodb connection string. You can use a commandline text editor, like <code>nano</code>, <code>vim</code> or <code>emacs</code>. We'll use <code>nano</code> in these examples (but you can use vim or emacs as well). 

1. In your project directory, create and open your file by:
    <br>
    <pre><code>nano config.json</code></pre>
2. Then add your connection string so that it includes username and password (that you retrieved above):
	<br>
    <pre><code>mongoose.connect('mongodb://jversoza:my_password@class-mongodb.cims.nyu.edu/jversoza');</code></pre>
3. Save it by <code>CONTROL+O</code> to _write out_ the file. Press <code>RETURN/ENTER</code> to accept the file name.
4. Quit <code>nano</code> by <code>CONTROL+X</code>
5. Test your application. Substitute <code>APP_PORT_NUMBER</code> with the port number you retrieved from Part 1 and add the environment variable, `NODE_ENV`.
	* Run <code>bin/www</code> or... if you didn't use express generator, use <code>node app.js</code>. __Don't use nodemon to run it.__
		<br>
        <pre><code>PORT=APP_PORT_NUMBER NODE_ENV=PRODUCTION bin/www</code></pre>
	* If it starts up fine, try connecting to it from your browser: 
        <br>
		<pre><code>http://i6.cims.nyu.edu:APP_PORT_NUMBER/</code></pre>
6. Troubleshooting:
	* If you see the following error 
        <br>
		<pre><code data-trim contenteditable>/home/net_id/opt/final-project/node_modules/mongoose/node_modules/mongodb/lib/server.js:235
        process.nextTick(function() { throw err; })
Error: connect ECONNREFUSED
    at errnoException (net.js:905:11)
    at Object.afterConnect [as oncomplete] (net.js:896:19)</code></pre>
		You can't connect to your database; double check your mongoose connection string... and review part 3.
	* If you see <code>Error: listen EADDRINUSE</code> ... the port you've been assigned is already in use. Either your application is already running or someone inadvertently is running there application on your port. Contact me if it's the latter.
	
</section>

### Part 8: Running your project as a daemon

<section markdown="block">

In order to have your project run even when you're not logged in to i6 through an interactive shell, you'll have to run it as a daemon. We'll use a node module called <code>forever</code> to do this. It will run your application in the background and give you tools to manage it (start and stop).

1. Install <code>forever</code>:

	<pre><code>cd ~/usr/local/lib/
npm install forever</code></pre>

2. Run your app with <code>forever start</code>. Substitute <code>APP_PORT_NUMBER</code> with your actual port number. Note the <code>-o</code> and <code>-e</code> options; they specify where your application's output (debug and error output) should go.

	<pre><code>cd ~/opt/final-project/
export PORT=APP_PORT_NUMBER; export NODE_ENV=PRODUCTION; ~/usr/local/lib/node_modules/.bin/forever -o ~/var/log/app.log -e ~/var/log/app_error.log start bin/www</code></pre>

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

### Part 8: Reinstalling and/or redeploying
<section markdown="block">

1. If you'd like to __reinstall__  your application (again, with <code>REPOSITORY_NAME</code> replaced with your repository name):

	1. <code>rm -rf ~/opt/REPOSITORY_NAME</code>
	2. go back to Part 5

2. If you'd like to simply __redeploy__ or __update__ your application, run <code>git pull</code> to update your code on the server:
    1. `cd ~/opt/REPOSITORY_NAME`
    2. `git pull`

</section>
</div>
</div>
