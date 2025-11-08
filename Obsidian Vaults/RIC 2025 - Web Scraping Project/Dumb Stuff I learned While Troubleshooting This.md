**Creating Virtual Python Environments on Windows:**
- making sure python was installed on the system
- ```py 
  
   py -m venv .venv
  ```
  to create the environment. the second argument is supposed to be the location where the environment is made, but I just generated it in the directory open in my workspace
- ```powershell
  
  .venv\Scripts\activate
  ```
  activating the environment will put the virtual environment-specific python and pip executables into the shell's path - BUT I ENCOUNTERED A POLICY EXECUTION ERROR

**Changing powershell execution policies**:
- I didn't have the right execution policies set to the terminal. To Activate it in VSC, executing: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

**Installing Libraries in a virtual environment:**
- I accidentally install all my packages in my system. oops. I'll have to reinstall all of them using:
	- `python3 -m venv --system-site-packages .venv`

**troubleshooting scripts not launching in powershell in vscode**
- well first time was because my dumbass didn't activate the virtual environment.
- then I turned to chatgpt for advice. 
	- `Get-Command python | select-object -expand source`
	- that printed the python.exe executable in my scripts folder in the virtual environment
- okay so using `ctrl+shift+p` I changed the python interpreter for the workspace and selected the exe path printed with the command earlier...sigh.
- it also said to change the workspace settings in settings to activate the environment...which is enabled already, but I set it so that every time the workspace is open, it should activate the environment. 
- bro.... the syntax is `python -m <module>`
- thought I had to create some complex code to get my scraper to comply with robots.txt on sites, but scrapy does it by default...huh...damn.
- the tutorial added this link to try to avoid getting the bot banned while it parses: https://docs.zyte.com/web-scraping/tutorials/main/cloud.html