# HACKTHEBOX-stocker
A very simple, detailed, step by step write up for stocker machine on hackthebox.
## First step:
Join the machine and connect to the lab.
Connected to the machine:
<table>
  <tr>
    <td>
      <img src="Images/Screenshot_3.png" alt="Local Image 1">
    </td>
    <td>
      <img src="Images/Screenshot_1.png" alt="Local Image 2">
    </td>
  </tr>
</table>
I’ll create a directory named “HTB” in “/home/joxavy/Desktop” and place the downloaded OVPN file inside of it, as you can see below:

![Local Image](Images/Screenshot_2.png)

Then I’ll install OpenVPN inside that same location:

![Local Image](Images/Screenshot_4.png)

Then I will run my “. ovpn” file with OpenVPN in order to connect to it:

![Local Image](Images/Screenshot_5.png)

And as you can see below, the operation has been completed successfully:

![Local Image](Images/Screenshot_6.png)

## Second step:
Ping the machine and do some scanning

![Local Image](Images/Screenshot_7.png)

pinged with 0% packet loss.
Using “Nmap” to detect open ports or any vulnerabilities with the options -sV and -sC in order to detect both the version used and the OS.

![Local Image](Images/Screenshot_8.png)

The information obtained from this scan is as follows:

| Opened ports | Version | OS    |
|--------------|---------|-------|
| SSH, HTTP    | 8.2p1   | Linux |

Now I will add the domain that http redirects to “ http://stocker/htb” to the etc/hosts since it’s the domain we need to enumerate, which is the process that establishes an active connection to the target host (stocker in our case) to discover potential attack vectors in the system, and the same can be used for further exploitation of the system.

![Local Image](Images/Screenshot_9.png)

As you can it was added successfully:

![Local Image](Images/Screenshot_10.png)

Explore that domain name now:

![Local Image](Images/Screenshot_11.png)

## Third Step:
I’m going to use “gobuster” in order to enumerate the subdomains, as well as use “seclists” wordlists to make the job easier .
first thing I’ll do is download seclists, which is a collection of multiple types of lists used during security assessments. This includes usernames, passwords, URLs, etc.

![Local Image](Images/Screenshot_12.png)

Then I will run the go buster tool:

![Local Image](Images/Screenshot_13.png)

As you can see above, I’ve found one subdomain that is “dev.stocker.htb”.
I’ll add it to “etc/hosts” in order to explore it.

![Local Image](Images/Screenshot_14.png)

This is the resulting page:

![Local Image](Images/Screenshot_15.png)

It’s a login page.

## Fourth Step: intercept traffic
First things first I will lunch “Burp suite”
And set a manual proxy in order to intercept the upcoming traffic on Firefox:

![Local Image](Images/Screenshot_16.png)

Ill turn the burp suite intercept by going to proxy > intercept > intercept is off and turn it on

![Local Image](Images/Screenshot_17.png)

And the result above is the intercepted traffic from the site.
I’ll change the content-type to Json format to make the work easier.

<table>
  <tr>
    <td>
      <img src="Images/Screenshot_18.png" alt="Local Image 1">
    </td>
    <td>
      <img src="Images/Screenshot_19.png" alt="Local Image 2">
    </td>
  </tr>
</table>

Then I’ll press “forward” to get the response and see what website the request is redirecting to:

![Local Image](Images/Screenshot_20.png)

It redirects to “stock”
Which automatically shows up on the screen after I pressed forward 3 times:

![Local Image](Images/Screenshot_21.png)

Now I will turn my interceptor off and back on to intercept traffic from this site and add items to the cart, such as the red cup:

![Local Image](Images/Screenshot_22.png)

View my cart:

![Local Image](Images/Screenshot_23.png)

Submit the purchase and view the receipt:

![Local Image](Images/Screenshot_24.png)

Which directly gets intercepted in burp suite and shows up like this:

![Local Image](Images/Screenshot_25.png)

As you can see it shows the product id, the item name, price, etc.

![Local Image](Images/Screenshot_26.png)

Ill send this to the repeater in order to Send a request with varying parameter values to test for input-based vulnerabilities and turn the intercept off.

![Local Image](Images/Screenshot_27.png)

## Fifth step: search for password and username of user.
Now ill write a code to replace the title which is a server side forgery in order to try and display the passwords:

![Local Image](Images/Screenshot_28.png)

Which will display the content of “etc/passwd” instead of where the item’s title was, and “iframe” will load that page inside the current parent page.
After sending this, we get:

![Local Image](Images/Screenshot_29.png)

We copy the new order Id and paste It into the dev.stocker.htb webpage with the corresponding link and get this result:

![Local Image](Images/Screenshot_30.png)

![Local Image](Images/Screenshot_31.png)

Since we figured out earlier that through Nmap that the system is Ubuntu based on Linux, and knowing that the users in ubuntu are given an UID that starts from 1000, therefore a newly created account, will usually be give the next-highest unused number, which is going to be the UID of 1001, therefore, “angoose” is the user we are looking for.
I will use the following code snippet in order to get details of the index.js file that’s located inside the var directory which contains variable data files. This includes spool directories and files, administrative and logging data, and transient and temporary files

![Local Image](Images/Screenshot_32.png)

Press send and copy the output id I got.
Paste it into the website as we did previously and this is the result I obtained:

![Local Image](Images/Screenshot_33.png)

And from here we see the user’s password that is:

![Local Image](Images/Screenshot_34.png)

Which displays the mongodb and Node.js user connection setting
## Sixth step: connect to user’s account
Now I will connect to the user’s account:

![Local Image](Images/Screenshot_35.png)

Use the following command to see which files have root permissions:

![Local Image](Images/Screenshot_36.png)

Which shows that the files of type “.js” have the root permission therefore I will create a file .js and write a script into it in order to catch the flag using fs.readfile which is a method that’s mostly used to read the file and write the data with console.log in the terminal:

![Local Image](Images/Screenshot_37.png)

Run the file:

![Local Image](Images/Screenshot_38.png)

And as you can see, the flag has been captured !
