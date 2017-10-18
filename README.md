# linux-server
Udacity Full Stack Developer Linux Server Project 
- IP ADDRESS: 54.152.137.233
- SSH PORT: 2200
- URL: HTTP://54.152.137.233
# Create new user (grader)
1. sudo adduser grader
2. vim /etc/sudoers
3. touch /etc/sudoers.d/grader
4. vim /etc/sudoers.d/grader
- edit file to look like: grader ALL=(ALL:ALL)ALL
- save and quit 
