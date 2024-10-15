# pentest

1. Ping Station:

Steps and Logic:
Through testing input options by adding a command to a valid input we find out that input has vulnerability – we can run commands through it as long as command starts with a valid, acceptable-for-the-system input; in this case, that input is a valid IP address (you can change 1.1.1.1 for any other valid IP address). We can test this vulnerability by entering ’1.1.1.1; ls’ for example and see the contents of the current directory.

Through this we learn that in the current directory there is a file named ‘flag’ which we output to the page using ‘cat flag’ command.

BASICALLY PUT 1.1.1.1; cat flag in the insert IP space

Flag:
ECSC{b1c0bc8e5e1b4c81199ad3c41bef8b69bea3ab86ecfb08c211d90ace0ff98df3}


2. File Crawler:
   
   Steps and Logic:
Based on the hint: “The flag is located in a temporary folder” we can start looking for a temporary folder in the files. We can try a couple of different ‘levels’(so ./tmp.flag, etc.) – in the same folder, in parent folder, etc. We use two slashes because of slash filtering, so one ‘/’ will not work, but ‘//’ will because the second slash will bypass the filter.
From inspecting the initial page we can see the arguments in the url
We can change the url for the path we’re interested in, so -> .//tmp/flag.

BASICALLY Enter: ‘ /local?image_name=.//tmp/flag ’ after the address
File containing the flag is automatically downloaded

additional steps that i did: Then I changed the url http://34.107.26.201:30513/local?image_name=static/path.jpg into http://34.107.26.201:30513/local?image_name=app.py to see the python code of the file
With the etc/password I downloaded a file which showed information stored about the users on the system
Finally I used ..tmp/flag to find the flag which was:
CTF{0caec419d3ad1e1f052f06bae84d9106b77d166aae899c6dbe1355d10a4ba854}


3. Alien inclusion

   I opened Linux on VMWare and first checked if I had curl function installed and then went with the command curl -X POST "http://34.107.26.201:30640/?start=" --data "start=/var/www/html/flag.php", to find my flag.
The command curl -X POST "http://34.107.26.201:30640/?start=" --data "start=/var/www/html/flag.php" sends a POST request to a web server. It aims to include and execute the flag.php file on the server by setting the start parameter to its path. This is used to retrieve the hidden flag for the CTF challenge. 
And as expected the command gave us the flag:
ctf{b513ef6d1a5735810bca608be42bda8ef28840ee458df4a3508d25e4b706134d}


Steps and Logic:
Based on the logic given on the landing page we can see what condition we need met: we should definitely be getting ‘start’ parameter in a request, so in our trials we will take that into account. So when we start using Curl to send a post request, we are making sure to set the ‘start’ to the file we want to see and also include it in the url. As we can see from the landing page and by using the inspect tool, the page uses php – hence the file extension for ‘flag.php’.

4. Ultra Crawl

   In BurpSuite turn on intercept, go to provided url and send the request to repeater. Change the Host to ‘ company.tld ’ and add line ‘ url=file:///home/ctf/app.py ’. Send the request. In the response you will see the flag. or tru ... :////home ...
Flag:
ctf{d8b7e522b0ab04101e78ab1c6ff68c4cb2f30ce9d4427d4cd77bc19238367933}

Steps and Logic:
Through testing we can see that we can access different files via input for example: file:///etc/passwd is valid
We can see that there is a home/ctf directory. There should also most likely be a startup bash script named ‘start.sh’ and through checking this suspicion is proven correct.
Then we go on to check the ‘app.py’ file
After brief analysis we know what parameters need to be set in POST request to get the flag and we can move to BurpSuite and send the request accordingly – making sure to set the Host to ‘company.tld’ as that is an important condition. The following request returns desired response

5. Substitute

   Add to the address: ‘ /?vector=/Admin/e&replace=system(‘cat here_we_dont_have_flag/flag.txt’) ’

   Flag:
CTF{92b435bcd2f70aa18c38cee7749583d0adf178b2507222cf1c49ec95bd39054c}

Steps and Logic:
Upon inspecting the logic given on the landing page, we know that we need to set ‘Vector’ parameter AND the ‘replace’ parameter for the initial Admin-swapping to work -> ‘?vector=/Admin/&replace=User’ – adding this to the url, for example, will replace ’Admin’ on the page with ‘User’. Following the same logic, we can check what is in the current directory by running the following command : /?vector=/Admin/e&replace=system(‘ls’)
Through this, we learn the name ‘here_we_dont_have_flag index.php’ 
in which we later locate flag.txt.
