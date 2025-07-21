First, copy the link to the [challenge.zip](./challenge.zip) file, then use the following command:

```bash
wget <link>
```
We need to initialize our working git directory.

```
$ cd drop-in/
```

Inside the directory there's a message.py file, but there's nothing about the flag.
Then we can see from hint no. 1, we need to pinpoint the commit for one file.
To do that we can use this command:

```
git log -- message.py
```

Using that command, we got 2 commit changes for message.py and one of them is the flag that we are searching for.

<img width="497" height="220" alt="image" src="https://github.com/user-attachments/assets/433a7ce6-2698-4e33-a250-7365c7933e88" />

# Flag
picoCTF{@sk_th3_1nt3rn_cfca95b2}
