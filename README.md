# Denver.rb - Beginner's Track
This is a small blog for notes and links from Beginner's Track talks.

## Creating a new post
### Fork the repo - hit the fork button in the upper right

### Clone it to your system
run ```$ gem install jekyll``` from the command line. Jekyll lets you see your post.

From the command line, run
```shell
$ rake post["Title of the Post"]
```
This creates a new markdown formatted post under `beginners-track/_posts` with the correct Yaml header.

The formatting style is github markdown. Here is a cheat sheet on how to use it. https://help.github.com/articles/github-flavored-markdown

Make sure to give yourself credit on the post and to include whatever links you want for people to get in touch.

If you run ```$ jekyll serve --watch``` you will be able to see your post at 'localhost:4000'. The ```--watch``` will have jekyll keep an eye on changes update the server.

Write your post!  

## Make a Pull Request
Save changes to post and push to master on your fork.

Make a pull request to the master on rubybeginnerstrack/rubybeginnerstrack. When you're ready, email jeffrey@matthias.org and let him know that there is a PR waiting.
