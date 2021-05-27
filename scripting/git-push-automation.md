# Git Push Automation

We developers generally like to copy-paste our clipboard on some notepad. I generally write the commands I forget \(or the commands I dont want to remember\) on a notepad++

Going a step forward, I created a git repo for that. Now I can see the command I used 3 years back.

Only problem with this is I need to push this repo whenever I make change to any of the files.

So I wrote a simple shell script which can do this in a single click.

Whats more interesting is that, I scheduled this to "Execute whenever I login", so now onward, I dont even have to remember that I had script.

This problem looks very basic and also the solution is too basic. Nevertheless it works like a charm.

The script is as follows

```bash
debug(){
	echo "[DEBUG] Executing $@"; # Print the command
	$@; # then executes
	sleep 2s; # this is optional
}

debug cd D:/Work/scribblepad # cd to the git repo
debug git add . # add to index (explicit add command is needed since I create multiples new files)
debug git status # optional
debug git commit -am "$(date) - auto commit" # commit. I like to see the date in the msg itself
debug git push origin master # push to desired branch
echo
echo
echo
read -p "[EXIT] Press any key to close this prompt" -n 1 -r # just waiting for user to press any key. This is a confirmation for me, I will know that the script was executed.
```

To add this to Windows 10 scheduler, press **Win+R** then type **taskschd.msc**

Then under Actions tab, create a basic task and you are done.

