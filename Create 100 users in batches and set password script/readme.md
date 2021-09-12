# Simple requirements realization process

```　　　　　　　　 

Background: 100 new recruits, everyone needs to log in to the server
Simple requirements realization process:
  1. Add users
  2. Set a password for the user

Enterprise requirements realization process:
  1. Add users
  2. Set a password for the user
```
1. Add users
```　　　　　　　　 

#Create a user named zs
useradd zs
```


2. Set a password for the user

```　　　　　　　　 
#Set the password for zs user to 123456
echo 123456 |passwd --stdin zs
```
```　　　　　　　　 
USER_LIST=$@
USER_FILE=./user.info
for USER in $USER_LIST; do
    if ! id $USER &>/dev/null; then
        PASS=123456
        useradd $USER
        echo $PASS |passwd --stdin $USER &>/dev/null
        echo "$USER   $PASS" >> $USER_FILE
        echo "$USER User create successful."
    else
        echo "$USER User already exists!"
    fi
done

```
# Enterprise requirements realization process
1. Script preparation   

Linux generates random numbers
```　　　　　　　　 
#Linux Generate simple random numbers
echo $RANDOM
#Linux Generate md5 random numbers
echo $RANDOM |md5sum
##Linux Generate md5 random number and intercept the first 8 characters
echo $RANDOM |md5sum |cut -c 1-8
```

2. Enterprise-level scripts

```　　　　　　　　 
#!/bin/bash
USER_LIST=$@
USER_FILE=./user.info
for USER in $USER_LIST; do
    if ! id $USER &>/dev/null; then
        PASS=$(echo $RANDOM |md5sum |cut -c 1-8)
        useradd $USER
        echo $PASS |passwd --stdin $USER &>/dev/null
        echo "$USER   $PASS" >> $USER_FILE
        echo "$USER User create successful."
    else
        echo "$USER User already exists!"
    fi
done

```
3. Requirements

```　　　　　　　　 
Enterprise-level requirement script:
1. Create incoming users in a loop
2. Set a random md5 password for the created user
3. Save the user and password to a designated file for easy management
4. The user existence prompt "$USER User create successful."
5. The prompt "$USER User already exists!" is displayed if the user does not exist
```
7. Enterprise-level script description

# Use case
1. Run the script
2. View created users
3. View user and password information in the file
