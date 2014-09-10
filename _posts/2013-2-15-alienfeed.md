---
layout: post
title: "AlienFeed & Developing Command Line Tools with Python"
category: posts
---

I just recently decided to hack together a command line application for fun. I chose python for its readable code and ease of use since I only wanted to code it in a few hours, and not have it take up my whole week. I stumbled upon argparse, a built-in python module for parsing and handling command line arguments. What I wound up creating is a small Reddit client called AlienFeed. With AlienFeed you can return a list of Reddit submissions and open the links in your default browser window. I added a random argument as well that opens a random link in the requested subreddit. What I want accomplish in this post is to quickly demonstrate how one can make a command line tool, like AlienFeed, with python. First off, you of course need a copy of the python distribution, a web browser, a computer that turns on and off, fingers that can type, et cetera. Now that you have a functioning computer, create a file named 'hello.py' (or what ever you want) and save it in the folder of your choice. The command line tool we will make is going to be a barbones app that simply returns your argument with a message like 'Hello '. Now that we defined our app, edit the file, 'hello.py', with the following code.

    import argparse

    parser = argparse.ArgumentParser(description='say hello to the computer')

    parser.add_argument("name", help='returns a message to the user')

    parser.add_argument("-c", "--caps", help='Turns the message into all CAPS')

    args = parser.parse_args()
    print args.name

The code above is an incomplete argument parser since the all caps argument functionality has not been created yet. However, the program currently takes the first argument (name) and prints it out to the console. The second line 'parser = argpar...' creates the parser essentially, and gives us access to the object ArgumentParser which allows us to define arguments for our parser. The next two lines with 'parser.add_argument' are meant to declare arguments and their usage. Note that the help parameter is used to provide information about the CLI within the '-h, --help' arguments. Now run the command below to see what the 'help' output returns.

Run:

    $ python hello.py -h

Output:

    usage: hello.py [-h] [-c CAPS] name
     
    say hello to the computer

    positional arguments:
    name returns a message to the user

    optional arguments:
    -h, --help show this help message and exit
    -c CAPS, --caps CAPS Turns the message into all CAPS

Now lets rewrite this code to make it do what we really want it to do.

    import argparse

    def main():
        parser = argparse.ArgumentParser(description='say hello to the computer')

        parser.add_argument("name", help='returns a message to the user')

        parser.add_argument("-c", "--caps", action='store_true', help='Turns the message into all CAPS')

        args = parser.parse_args()

        msg = 'Hello ' + args.name

        if args.caps:

        	print msg.upper()

        else:

        	print msg

    if __name__ == '__main__':
        main()

The code above takes the users input and returns it with a message that says 'Hello ' in either all caps or no caps. Here is an example of how to use this command line tool.

Run:

    $ python hello.py Jared

Output:

    `Hello Jared`

Run:

    $ python hello.py Jared --caps

Output:

    `HELLO JARED`

The parameter "action='store_true'", in our case, is used to make sure that caps takes no argument of its own. Anyway, this is how one could parse command line tools with python. If you have any questions about the post, my projects, or anything programming related, feel free email me at jawerty@gmail.com. And be sure to see my Github if you would like contribute to any of my projects. Goodbye and happy hacking.



