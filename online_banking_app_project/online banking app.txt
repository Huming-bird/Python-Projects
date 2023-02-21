# THIS IS AN ONLINE BANKING APP PROJECT
#==========================================================================================================================

def password_check(password):
    upper, lower = 0,0
    for i in password:
        if i.isupper():
            upper+=1
        elif i.islower():
            lower+=1
    al_num=(any(c.isalpha() for c in password) and any(c.isdigit() for c in password))
    
    if upper>= 1 and lower>=1 and al_num and len(password)>=8:
        return True
    return False
#==========================================================================================================================
def signup(user_accounts, log_in, bank):
    
    '''
    This function allows users to sign up.
    If both username and password meet the requirements:
    - Updates the username and the corresponding password in the user_accounts dictionary.
    - Updates the log_in dictionary, setting the value to False.
    - Returns True.

    If the username and password fail to meet any one of the following requirements, returns False.
    - The username already exists in the user_accounts.
    - The password must be at least 8 characters.
    - The password must contain at least one lowercase character.
    - The password must contain at least one uppercase character.
    - The password must contain at least one number.
    - The username & password cannot be the same.

    Input:
        user_accounts (dictionary): A dictionary contains the users as keys and their passwords as values.
        log_in (dictionary): A dictionary contains the users as keys and a boolean value as values.
        username (string): The username of the user trying to signup.
        password (string): The password of the user trying to signup.

    Output:
        (boolean): Return True if the username and password are valid and the user is successfully signed up.
        Return False otherwise.'''
    
    username = input('Please provide your name: ')
    print("a valid password is at least 8chars long, should contain at least one uppercase, lowercase and numbers\n")
    password = input('please provide a valid password: ')
    

    if username not in user_accounts and username!= password and password_check(password):
        user_accounts[username] = password
        log_in[username] = False
        bank[username] = 0
        return 'Congratulations!!, We Successfully Signed Up your account'
    else:
        return 'Something went wrong, please check your details and try again'
#==========================================================================================================================

def login(user_accounts, log_in):
    '''
    This function allows users to log in with their username and password.
    The user_accounts dictionary stores the username and associated password.
    The log_in dictionary stores the username and associated log-in status.

    If the username does not exist in user_accounts or the password is incorrect:
    - Returns False.
    Otherwise:
    - Updates the user's log-in status in the log_in dictionary, setting the value to True.
    - Returns True.

    For example:
    - Calling login(user_accounts, "Brandon", "123abcAB") will return False
    - Calling login(user_accounts, "Brandon", "brandon123ABC") will return True
    '''
    username = input('Please provide your name: ')
    
    if username in user_accounts:
        print("Note: a valid password is at least 8chars long, should contain at least one uppercase, lowercase and numbers\n")
        password = input('please provide a valid password: ')
    else:
        return 'Name doesn\'t exist'
    
    # Check if username exists in user_accounts
    if username in user_accounts and password == user_accounts[username]:
        # Update log_in status to True
        log_in[username] = True
        return 'Login Successful'
    else:
        return 'Invalid Credentials, try again'
#==========================================================================================================================

def import_and_create_accounts(filename, user_accounts, log_in, bank):
    
    '''
    This function creates accounts for users that have submitted their names rather than sign up themselves.
    The given argument is the filename containing customer details.
    Every line in the file should be in the following format:
      username - password (key : value pairs to use in user_accounts dictionary)
    Note: The password must fulfil the requirements in signup function.

    For the login dictionary, the key is the username, and its value indicates whether the user is logged in(True), or not.
    (Initially, all users are not logged in.)

    What you will do:
    - Create an empty user accounts dictionary and an empty login dictionary.
    - Read in the file.
    - If the username and password fulfills the requirements, adds the username and password
    into the user accounts dictionary, and updates the login dictionary.
    - You should also handle the following cases:
    -- When the password is missing.  If so, ignore that line and don't update the dictionaries.
    -- When there is whitespace at the beginning or end of a line and/or between the name and password on a line.  You
    should trim any and all whitespace.
    
    input:
        - file containing stored usernames and password
    output:
    - Return both the user accounts dictionary and login dictionary from this function.
    '''

    # Open the file and read it line by line
    file_handle = open(filename, "r")
    for line in file_handle:
        # Split the line into a list
        customer = line.strip().split(" - ")
        # Check if the line contains enough elements to contain username and password
        if len(customer) == 2:
            # Extract the username and password
            username = customer[0].strip()
            password = customer[1].strip()
            # Use the signup function to check if the password is valid
            if username in user_accounts:
                continue
            elif password_check(password):
                user_accounts[username] = password
                log_in[username] = False
                bank[username] = 0
    file_handle.close()
    return user_accounts, log_in, bank
#==========================================================================================================================

def deposit(bank, log_in):
    '''
    This function will credit the given user's bank account with the given amount.
    bank is a dictionary where the key is the username and the value is the user's account balance.
    log_in is a dictionary where the key is the username and the value is the user's log-in status.
    amount is the amount to update with, and can either be positive or negative.

    To credit the user's account with the amount, the following requirements must be met:
    - The user exists in log_in and his/her status is True, meaning, the user is logged in.

    If the user doesn't exist in the bank, create the user.
    - The given amount can not result in a negative balance in the bank account.

    Return True if the user's account was updated.
    '''
    # for a customer to make deposit, he/she must first have a bank account
    # to make deposit, customer has to be logged in
    # deposit amount has to be greaer than zero
    
    username = input('Please provide your name: ')
    amount = float(input('How much do you want to deposit: '))
    
    if username not in bank:
        signup(user_accounts, log_in, bank)
        
    # this condition checks if a user is already logged in
    if amount > 0 and not(log_in[username]):
        login(user_accounts, log_in)
    elif amount > 0 and log_in[username]:
        bank[username]+= amount
        return 'Deposit Successful'
    else:
        return 'Invalid amount'
#==========================================================================================================================

  def import_and_deposit_bank(filename, user_accounts, log_in, bank):
    '''
    This function is used to mass update the bank dictionary. In cases where customers cant process their own transactions,
    a bank staff can get their deposits and mass deposit at a later time using this function.
    The given argument is the filename to load.
    Every line in the file should be in the following format ( key: value)
       
    The key is a user's name and the value is an amount to credit the user's bank account with.  The value should be a
    number, however, it is possible that there is no value or that the value is an invalid number.

    What you will do:
    - Create an empty bank dictionary.
    - Read in the file.
    - Add keys and values to the dictionary from the contents of the file.
    - If the key doesn't exist in the dictionary, create a new key:value pair.
    - If the key does exist in the dictionary, increment its value with the amount.
    - You should also handle the following cases:
    -- When the value is missing or invalid.  If so, ignore that line and don't update the dictionary.
    -- When the line is completely blank.  Again, ignore that line and don't update the dictionary.
    -- When there is whitespace at the beginning or end of a line and/or between the name and value on a line.  You
    should trim any and all whitespace.
    - Return the bank dictionary from this function.
    '''

    with open(filename) as f:
        for lines in f:
            # Remove whitespaces
            line = lines.strip()

            # Skip blank lines
            if len(line) == 0:
                continue
            
            # Split the line by ":"
            part = line.split(":")
            if len(part)==2:
                username = part[0].strip()
                try:
                    amount = float(part[1].strip())
                except ValueError as e:
                    continue
        # no need to login for mass deposit as password ought not to be shared with anyone
            if username in bank:
                bank[username]+=amount
            else:
                print('This user can\'t deposit yet, kinly open accounts for them')
                
            
    return bank
#==========================================================================================================================

def transfer(user_accounts, bank, log_in):
    
    '''
    This function makes a transfer between two customers.
    userA must be a bank customer while userB may or may not be a customer(other bank transfers).
    bank is a dictionary where the key is the username and the value is the user's account balance.
    log_in is a dictionary where the key is the username and the value is the user's log-in status.
    amount is the amount to be transferred between user accounts (userA and userB).  amount is always positive.

    What we will do:
    - Deduct the given amount from userA and add it to userB, which makes a transfer.
    - You should consider some following cases:
      - userA must be in the bank and his/her log-in status in log_in must be True.
      - userB must be in log_in, regardless of log-in status.  userB can be absent in the bank.
      - No user can have a negative amount in their account. He/she must have a positive or zero balance.

    Return True if a transfer is made.
    '''
    userA = input('Please provide your bank account name: ')
    userB = input('Please provide receiver bank account name: ')
    amount = float(input('How much do you want to transfer: '))

    if userA in bank:
        login(user_accounts, log_in)
        
        # customer's account balance needs to be greater than amount he wants to transfer        
        if bank[userA] >= amount and log_in[userA]:
            bank[userA] -= amount
            
            if userB in bank:
                bank[userB] += amount
                return 'Transfer Successful'
        else:
            return 'Request Declined'
#==========================================================================================================================

def change_password(user_accounts, log_in):
    '''
    This function allows users to change their password.

    If all of the following requirements are met, changes the password and returns True. Otherwise, returns False.
    - The username exists in the user_accounts.
    - The user is logged in (the username is associated with the value True in the log_in dictionary)
    - The old_password is the user's current password.
    - The new_password should be different from the old one.
    - The new_password fulfills the requirement in signup.

    '''
    username = input('Please provide your name: ')
    login(user_accounts, log_in)
    old_password = input('Please provide your old password: ')
    print("Note: a valid password is at least 8chars long, should contain at least one uppercase, lowercase and numbers\n")
    new_password = input('please provide a valid password: ')
    
    if username in user_accounts and log_in[username] and old_password == user_accounts[username] and new_password!= old_password \
                                                                                                and password_check(new_password):
        user_accounts[username] = new_password
        return 'Password changed Successfully'
    else:
        return 'Try again'
#==========================================================================================================================

def delete_account(user_accounts, log_in, bank):
    '''
    Completely deletes the user from the online banking system.
    If the user exists in the user_accounts dictionary and the password is correct, and the user 
    is logged in (the username is associated with the value True in the log_in dictionary):
    - Deletes the user from the user_accounts dictionary, the log_in dictionary, and the bank dictionary.
    - Returns True.
    Otherwise:
    - Returns False.
    '''
    
    username = input('Please provide your name: ')
    login(user_accounts, log_in)
    
    if username in user_accounts and log_in[username]:
        del user_accounts[username]
        del log_in[username]
        del bank[username]
        return 'Account deleted Successfully. Thank you for banking with us :('
    else:
        return 'Account has not been deleted. Thank you for choosing to remain with us :)'

#==========================================================================================================================
# CODE BY HUMING-BIRD <sonugaahmed@gmail.com>
