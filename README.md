# Python-SMS-for-Free
This is a simple script for sending Email and SMS messages for free

Most scripts out there when searching google are a bit outdated. The logic is sound but the tech has evolved only slightly, but enough to render the scripts non-functioning. Specifically, what I found was a variety of libraries being used in various ways, imported into a notebook, claiming to be free, but really you had to create an account with a 3rd party. This wasn't what I wanted, so I made the following solution which I couldn't find elsewhere.

*note: the primary reason I couldn't find a solution online already, is because Google recently changed their mail service to reject turning the low security access feature off. You can't do that anymore. So a lot of the scripts I found were obsolete. Many do mention the workaround for creating an application password, but I found that to be poorly documented.

This isn't ground breaking. It's just finding a way that works... for free. No accounts. The only account I set up was a burner GMail account and used the Python SMTP library. This is able to send email and SMS messages - from your email address. So I'll try to break it down for you.

USE CASE: Your reasons may differ, but I wanted to be able to insert a simple script into my projects to send me a text message at any given point. Whether it tells me the code finished, or something went wrong, whatever. And I didn't want to sign up for a 3rd party to do it, nor pay per text.

SETUP (March 2023):
1) Create a bogus account on Gmail. Example@Gmail.com

2) Log into the gmail account to create an application password (this is specific and separate from your regular account password):
  a) When you log into gmail, click the gear icon for Settings
  b) Select the "See All Settings" link at the top, this opens a new page
  c) Select tab "Accounts and Import"
  d) Under "Change Account Settings" select "Other Google Account Settings"
  e) This will open a new tab
  f) Select "Security" on the left side
  g) Scroll down to "How you Sign Into Google" and choose "2-Factor Authentication"
  h) This will ask you to turn it on for your account, which is necessary to be able to get a working script
  i) You might need to go back into the 2-Factor Authentication tab at this point. When you do, scroll to the bottom
  j) At the bottom, there's an "App Password" section, click into that
  k) Here, you'll click the drop down for "Select App" and choose "Other"
  l) Enter any name you choose. Example: Google Colab Notebook
  m) Click generate, and this will give you a 16 character string - Copy it
  n) This 16c string will be your specific "password" for the notebook or IDE environment of your choice to allow it access to your new gmail account. You don't need to change your gmail account password, lease that alone. Just put this one into your environment variables as a password strictly for your app to use to access your gmail account. Think of it like an API token. 
  
3) Create an OS environment variable to hold your login password for your gmail account.
  a) Depending on your IDE or notebook, this can be different for people
  b) On Google Colab notebooks, you'll need to put it in the Colab environment (not your local OS environment)
  c) If you are on Visual Studio on windows, for example, you're likely going to use the windows OS environment variables to save the 16c token
  
4) Once you have saved your token to the environment variable we're ready to write the code.

5) Bring in the libraries:
  a) import smtplib - this will provide server connectivity
  b) import os - this will provide environment variable access
  c) os.environ['GmailPass'] = 'xxxxxxxxxxxxxxxx' # On google colab, this is how you can easily insert the token to the environment. Keep in mind it's 16 characters no spaces. I've arbitrarily called it "GmailPass". 
  
6) # set up constants that we'll use in the script and likely won't change whether Email or SMS
SERVER = smtplib.SMTP_SSL('smtp.gmail.com', 465) # This sets up the server connection and port
PASSWORD = os.environ.get('GmailPass') # This gets the actual token
SENDER = "Example@gmail.com" # this is your gmail account you set up

7) # set up variables like sender, recipient, phone number, etc. For the SMS gateway, each carrier is different. You can easily find them online. For this script, it's important to know that the carrier here is the *recipient's* carrier. In this case I'm using verizon.
to_email = "myfriend@gmail.com" 
to_SMS = "1234567890" #KEEP THIS FORMAT
VERIZON = str(to_SMS) + "@vtext.com" #KEEP THIS FORMAT. This concats the phone number you want to text with the carrier SMS gateway
message = "testing testing 123." # This could be anything. Often SMS can be limited to 160 characters, but email is unlimited. With the way this is set up, because it's using the SMTP through email even for SMS, you may like to keep an eye on errors due to length of the message.

8) Define the function to send an email:
def send_email(message):
  try:
    SERVER.login("<your username>", PASSWORD) # logging into your gmail account using your username and the token
    SERVER.sendmail(SENDER, to_email, message) # sending the message From, To, Message
    SERVER.quit() # close the connection
    print("SUCCESS: Sent")
  except:
    print("ERROR: Not Sent")
    
9) Almost identical, to send SMS:
def send_text(message):
  try:
    SERVER.login("<your username>", PASSWORD) # logging in with your username and token
    SERVER.sendmail(SENDER, VERIZON, message) # sending message From, Carrier, Message
    SERVER.quit() # close connection
    print("SUCCESS: Sent.")
  except:
    print("ERROR: Not Sent.")
    
10) And finally, to call those functions, simply run send_email(message) or send_text(message)

11) Full Code:

import smtplib
import os
os.environ['GmailPass'] = 'xxxxxxxxxxxxxxxx'

SERVER = smtplib.SMTP_SSL('smtp.gmail.com', 465) # This sets up the server connection and port
PASSWORD = os.environ.get('GmailPass') # This gets the actual token
SENDER = "Example@gmail.com" # this is your gmail account you set up

to_email = "myfriend@gmail.com" 
to_SMS = "1234567890" #KEEP THIS FORMAT
VERIZON = str(to_SMS) + "@vtext.com" #KEEP THIS FORMAT.
message = "testing testing 123."

def send_email(message):
  try:
    SERVER.login("<your username>", PASSWORD) # logging into your gmail account using your username and the token
    SERVER.sendmail(SENDER, to_email, message) # sending the message From, To, Message
    SERVER.quit() # close the connection
    print("SUCCESS: Sent")
  except:
    print("ERROR: Not Sent")

def send_text(message):
  try:
    SERVER.login("<your username>", PASSWORD) # logging in with your username and token
    SERVER.sendmail(SENDER, VERIZON, message) # sending message From, Carrier, Message
    SERVER.quit() # close connection
    print("SUCCESS: Sent.")
  except:
    print("ERROR: Not Sent.")

send_email(message)
send_text(message)
