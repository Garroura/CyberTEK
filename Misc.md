# Perms
This task is an easy one being solved by 22 teams. We are given a simple challenge.txt file with linux permissions in each line.
Having no description or hints with the task , i thought of calculating permissions using an online calculator. [Link](https://nettools.club/chmod_calc)

Than we can use [Cyberchef](https://gchq.github.io/CyberChef/) with the function `from octal` to decrypt the message and get the flag.


# Openheimer
Task description : terraform's good, but have you tried opentofu?
We were given a netcat to interact with the server having openofu console running and a `main.tf` file.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/f0d50c05-3a50-498f-a305-180c316a8e44)

This task was an opportunity to learn about terraform and opentofu as it is a free terraform fork. [OpenTofu](https://opentofu.org)
Knowing that we have the console running , i tried some console commands using the documentation [Doc](https://opentofu.org/docs/cli/commands/console/)
![image](https://github.com/Garroura/CyberTEK/assets/164345052/b04d894e-7137-4ada-af83-370ab58428d9)

In order to print the value of our variable named `SECRET` , we can use `var.SECRET` but is says `(sensitive value)` which is obvious because `sensitive` is set to `True`.
So we need to baypass this and print the value. Going through the terraform commands [link](https://developer.hashicorp.com/terraform/language/functions/nonsensitive) we found that `nonsensitive` command takes a sensitive value and returns a copy of that value with the sensitive marking removed, thereby exposing the sensitive value.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/f5a1d4eb-2054-4fe8-a0ec-e542a23ec918)


As we can see it is refusing to print the value in plaintext so we will try weak encoding functions starting with `base64encode` .
![image](https://github.com/Garroura/CyberTEK/assets/164345052/c16c0ec1-c480-4987-9d00-b6fa52b0851a)

We can see here thas he is blacklisting some functions so we will try different encoding techniques. As we go through , the `urlencode` function is printing something.
![image](https://github.com/Garroura/CyberTEK/assets/164345052/11087099-314e-4f3c-b2bc-6bb5310d89a3)

Using the urldecode function  in Cyberchef we get our flag in ascii .

`Securinets{oP3N7OfU_I5_4w35Om3_8U7_83c4R3FuL_WH3n_WORkin9_Wi7h_53Cr3Ts}`

# bolbok

Task description : Bolbok is scared of the arcane and has hidden his secret in this restricted shell.
Using the netcat to connect to the server , we have a shell but we can only use some commands. I used this offsec handbook it helps a lot [Offsec](https://0xffsec.com/handbook/shells/restricted-shells/)
To see writable variables i used `export -p`. 
![image](https://github.com/Garroura/CyberTEK/assets/164345052/5ead06c3-2c0b-42be-9246-e45173fba38d)

As we can see the commands we can execute are stored in /usr/bolbok/cmds 

I used `echo /usr/bolbok/cmds/*` to list the directories in the file.

![image](https://github.com/Garroura/CyberTEK/assets/164345052/c670dc26-3286-4384-b9dd-bddd9186413a)

Knowing that we can execute `ls` command , we can use -l to -a to and -R to list all directrories starting with / ( `ls -laR /`)

Going through the listed directories we can find a hidden file named .flag in `/usr/share/misc/bolbok/directorysecrets/youwillneverfindthis`

![image](https://github.com/Garroura/CyberTEK/assets/164345052/e791a61a-c182-4254-a529-e927ca7e8f65)

Now , knowing the directory and the flag format we can use `grep -r "Securi" /usr/share/misc/bolbok/secrets/youwillneverfindthis` 

![image](https://github.com/Garroura/CyberTEK/assets/164345052/62d58771-afb8-4d0b-b217-367981c982e9)

`Securinets{wH47_W45_7H47_3x4C7LY?4n_R845h_j41l??_0r_ls_4nd_grep_L3550n}`



