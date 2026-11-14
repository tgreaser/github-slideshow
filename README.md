READ ME SEYMORE 
show ethernet mac-learning-log
There is no knob to get this data in a clean off the box or in continusely that is not manual.
Here is a FRAGILE(will explain that later) way to run the command and redirct it to a file..  

This is a little bit of a process but pays off..  Maybe during the holidays I will polish this mad dash solution to tshoot a good once over. 
** Planning to setup  Junos event-options or cron to send uniq logs to syslog, every  5 min tee it to a file and then  flush the learning file . 

1st
Get yourself setup for the long haul so your sesson can drop but still run the command on your box.
screen -S ex4400-mac-learning-log-session ssh tom@ex4400
tmux too..

2nd 
Set account your using has 0 idle timeout.
 set system login class tom-superhero idle-timeout 0;
commit

3rd 
start shell

FRAGILE Part.. When you run this command. IMMEDIATLY screen detach.. If you hit entrer it will stop the command 
$ cli -c "show ethernet-switching mac-learning-log use-system-timezone |no-more | refresh 30" >> /var/log/tom-mac-learning.log

command breakdown
Run cli
-c used with the " " so we can do | pipes.  You know you love *NIX .

Magic command for the data we are after.. !!! 
show ethernet-switching mac-learning-log  use-system-timezone.

Just give me all the data with no paging as I am just appending it 
| no-more

A reasonable amount of time to wait for your the ephermail log to update with new info.. 
| refresh 30

redirect the data to a txt file the user account can rw to.
>> /var/log/tom-mac-learning.log

NOW.. Your thinking great..now what..  Im going to get so much repeated info with the refresh appending to the file .. Or how do I even see that info? 
Well since we are on a *NIX box, someone already saved that day..
Make a NEW ssh session back into your switch and lets take a peak at what you got.. 

run start shell

Lets take a look at what you have collected, and see if the refresh rate needs raised or lowered. 
cat /var/log/tom-mac-learning.log | grep -v more |grep -v "refreshed"   | sort | uniq 

If you want to throw them at your syslog box / siem here you go.. I just do it manualy .. I plan on looking AT 
cat /var/log/tom-mac-learning.log | grep -v more |grep -v "refreshed"   | sort | uniq | logger
You can do that as many times as you want..

You can either offbox the file whatever you want.


Lastly.. when your done screen back into your session
screen -r 4400-mac-learning-log-session 
and hit enter it will stop your the show ethernet mac-learning-log 

Hope this helps anyone .. 
