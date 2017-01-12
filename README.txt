This is the website portion of the LoansBot. It is created from an API perspective first since that is its most popular usecase. Then, it has a index, login page, and query page. The query page is a very light wrapper around the database - it allows you to choose what you are trying to do, and it calls the appropriate api.

/site/ - 
  Contains all site code
  
  index.php -
    The entry point to the site. Points to logging in an the query page, as well as making it clear if you are logged in.
    
  login.php - 
    Shows the appropriate form for logging in, or the appropriate form to request a claim code.
    
  claim.php -
    Shows the appropriate form for claiming your account 
    
    Optional Params (GET) to Auto-Fill Form:
      user_id    - The user id to claim
      username   - The username to claim 
      claim_code - The claim code to use 
  
  reset_password.php - 
    Shows the appropriate form for resetting your password.
    
    Params (GET) to Auto-Fill Form (non-optional):
      user_id - The user id to reset the password on
      code    - The code that was sent to the reddit account
   
  sharecode.php -
    Shows the appropriate forms for either creating your share code or using another share code.
  
  query.php -
    Main interaction with the database that is online
    
  api/
    login.php - 
      Allows the user to login, returning success/failure
      
      Type: POST
      Params:
        username - The username of the reddit account
        password - The password of the reddit account 
      Result:
        Results/Success or Results/Failure
        
        Results/Success sets the PHPSESSID cookie - The session id is the method of authentication.
        
    request_claim_code.php -
      Allows the user to request a claim code be sent to his account via the LoansBot. Will send a reset
      password link if the account is already claimed.
      
      Type: POST
      Params:
        username - The username of the reddit account.
      Result:
        Results/Success or Results/Failure
  
    claim.php -
      Allows the user to claim an account, returning success/failure.
      
      Type: POST
      Params:
        claim_code     - The claim code sent to the reddit account
        user_id        - The user id that matches the username
        username       - The username of the account
        password       - The password to set for the account
        email          - Email for the account
        name           - The users name
        street_address - The users street address
        city           - The users city
        state          - The users state
        zip            - The users zip
        country        - The users country 
        
      Result:
        Results/Success or Results/Failure 
    
    reset_password.php -
      Allows the user to reset the password on an account, returning success/failure
      
      Type: POST
      Params:
        user_id  - The user id to reset the password on
        code     - The code used to verify the account
        password - The new password for the account (min 6. chars)
        
      Result:
        Results/Success or Results/Failure
        
    request_sharecode.php -
      Allows the user to request a share code be created on his behalf.
      
      Type: POST
      Params:
        None
      Result:
        Results/ShareCode or Results/Failure 
        
    user_sharecode.php -
      Allows the user to consume a share code 
      
      Type: POST
      Params: 
        format     - The format of the users to return. 0 (ultra-compact), 1 (compact), 2 (standard), or 3 (extended). Default 3 (extended)
        share_code - The sharecode
        username   - The username the sharecode belongs to
        
      Results:
        Results/Users/UltraCompact, Results/Users/Compact, Results/Usernames/Standard, Results/Users/Extended, or Results/Failure 
        
    loans.php - 
      Allows the user to fetch loan information.
      
      Type: GET
      Cookies: PHPSESSID
      Params:
        format                        - Either 0 (ultra-compact), 1 (compact), 2 (standard), or 3 (extended). Default 2 (standard)
        limit                         - Limits the number of results. Default 10 to ensure expensive queries are intended. 0 for no limit.
        
        
        id                            - The id of the loan. Default 0 (ignore)
        after_time                    - UTC Timestamp (MS) that the loans should come after. Default 0 (ignore)
        before_time                   - UTC Timestamp (MS) that the loans should come before. Default 0 (ignore)
        borrower_id                   - The id of the borrower that the loans should have (strict). Default 0 (ignore)
        lender_id                     - The id of the lender that the loans should have (strict). Default 0 (ignore)
        includes_user_id              - The id of a user that should be either the lender or the borrower. Default 0 (ignore)
        borrower_name                 - The name of the borrower that the loans should have (strict). Default "" (ignore)
                                        CANNOT be used with borrower_id 
        lender_name                   - The name of the lender that the loans should have (strict). Default "" (ignore)
                                        CANNOT be used with lender_id 
        includes_user_name            - The name of the user that should be either the lender of the borrower. Default "" (ignore)
                                        CANNOT be used with includes_user_id 
        include_deleted               - Either 0 (don't include deleted loans) or 1 (include deleted loans). Default 0
                                        See Results/Loans/UltraCompact, Results/Loans/Compact, Results/Loans/Standard, and Results/Loans/Extended
        principal_cents               - The exact amount of principal cents of the loans. Default 0 (ignore)
        principal_repayment_cents     - The exact amount of principal_repayment_cents of the loans. Default -1 (ignore)
        unpaid                        - If the loans should be unpaid. 0 for false, 1 for true. Default -1 (ignore)
        repaid                        - If the loans should have the same principal as principal repayment. 0 for false, 1 for true. Default -1 (ignore)
        
        
        modify                        - States whether the loans that have been returned from the above query are going to be modified. Either 0 (false) or 1 (true). Default 0.
        modify_reason                 - The reason a modification is happening. Minimum length of 5 characters. 
        set_borrower_id               - The id of the new borrower. Default 0 (ignore)
        set_lender_id                 - The id of the new lender. Default 0 (ignore)
        set_borrower_name             - The name of the new borrower. Default "" (ignore)
                                        CANNOT be used with set_borrower_id
        set_lender_name               - The name of the new lender. Default "" (ignore)
                                        CANNOT be used with set_lender_id 
        set_principal_cents           - Sets the principal cents of the loan. $100 = 10000 cents. Default 0 (ignore)
        set_principal_repayment_cents - Sets the principal repayment cents of the loan. $100 = 10000 cents. Default -1 (ignore)
        set_unpaid                    - Sets if the loans are marked as unpaid, 1 for true 0 for false. Default -1 (ignore)
        set_deleted                   - Sets if the loan is deleted, 1 for true, 0 for false. Default -1 (ignore). MUST include set_deleted_reason.
        set_deleted_reason            - Sets the loan deleted message. Default "" (ignore). Minimum length of 5 characters.
        
      Result: 
        Results/Failure, Results/Loans/UltraCompact, Results/Loans/Compact, Results/Loans/Standard, Results/Loans/Extended
        
    users.php -
      Allows the user to fetch user information
      
      Type: GET 
      Cookies: PHPSESSID
      Params:
        format      - Either 0 (ultra-compact), 1 (compact), 2 (standard), or 3 (extended). Default 2 (standard).
        limit       - Limits the number of results. Default 10, 0 for no limit.
        
        
        id          - The id of the user. Default 0 (ignore)
        username    - The username of the user. Default "" (ignore)
                      CANNOT be used with id
        after_time  - UTC Timestamp (MS) that the users should come after. Default 0 (ignore)
        before_time - UTC Timestamp (MS) that the users should come before. Default 0 (ignore)
      
      Result:
        Results/Failure, Results/Users/UltraCompact, Results/Users/Compact, Results/Users/Standard, Results/Users/Extended
        
    usernames.php -
      Allows the user to fetch username information
      
      Type: GET
      Cookies: PHPSESSID
      Params:
        format  - Either 0 (ultra-compact), 1 (compact), or 2 (standard). Default 2 (standard).
        limit   - Limits the number of results. Default 10, 0 for no limit.
        
        id      - The id of the username. Default 0 (ignore)
        user_id - The id of the user. Default 0 (ignore).
        
      Result:
        Results/Failure, Results/Usernames/UltraCompact, Results/Usernames/Compact, Results/Usernames/Standard
Results
  This describes the types of things that the api returns.
  Note that for UTC Timestamps, they may be null.
  Note that when usernames are fetched via "extended" formats, only the first username is retrieved.
  
  Success -
    This signifies the operation was successful.
    
    Response Code: 200 OK
    Response Json Object: 
      {
        "result_type": "SUCCESS",
        "success": true
      }
  
  Failure - 
    This signifies the operation was unsuccessful.
    
    Response Code: 400 Bad Request
    Response Json Object:
      Let <error_message> be a string containing a human-readable error message, such as "You must be logged in to do that." or "No account with that username/password combination could be found."
      Let <error_identifier> be a string that will not change when the details of the error message change, such as "NOT_LOGGED_IN", or "ACCOUNT_NOT_FOUND".
      
        {
          "result_type": "FAILURE",
          "success": false,
          "errors": [
            {
              "error_message": "<error_message>",
              "error_identifier": "<error_identifier>"
            },
            {
              "error_message": "<error_message>",
              "error_identifier": "<error_identifier>"
            },
            ...
          ]
        }
  
  ShareCode - 
    This signifies a share code was created 
  Loans -
    Let <loan_id> be a loan id, such as 500 or 36.
    Let <lender_id> be a user id such as 736 or 28. In the event of a modification, this is the original value.
    Let <borrower_id> be a user id such as 736 or 28. In the event of a modification, this is the original value.
    Let <principal_cents> be an amount of cents such as 10000 or 5000. In the event of a modification, this is the original value.
    Let <principal_repayment_cents> be an amount of cents such as 10000 or 5000. In the event of a modification, this is the original value.
    Let <unpaid> be a boolean such as true or false. In the event of a modification, this is the original value.
    Let <deleted> be a boolean such as true or false. Only included for moderators. In the event of a modification, this is the original value.
    Let <deleted_reason> be a string such as "Loan refunded immediately". Only included for moderators. In the event of a modification, this is the original value.
    Let <created_at> be a utc timestamp in milliseconds such as 1454004926439 or null.
    Let <updated_at> be a utc timestamp in milliseconds such as 1454004926439 or null. This is **NOT** the original value.
    Let <deleted_at> be a utc timestamp in milliseconds such as 1454004926439 or null. Only included for moderators. This is **NOT** the original value.
    Let <new_lender_id> be a user id such as 736 or 28. Only included if there was a modification to some part of the loan.
    Let <new_borrower_id> be a user id such as 736 or 28. Only included if there was a modification to some part of the loan.
    Let <new_principal_cents> be an amount of cents such as 10000 or 5000. Only included if there was a modification to some part of the loan.
    Let <new_principal_repayment_cents> be an amount of cents such as 10000 or 5000. Only included if there was a modification to some part of the loan.
    Let <new_unpaid> be a boolean such as true or false. Only included if there was a modification to some part of the loan.
    Let <new_deleted> be a boolean such as true or false. Only included if there was a modification to some part of the loan.
    Let <new_deleted_reason> be a boolean such as true or false. Only included if there was a modification to some part of the loan.
    Let <new_updated_at> be a utc timestamp in milliseconds such as 1454004926439 or null. Only included if there was a modification to some part of the loan.
    Let <new_deleted_at> be a utc timestamp in milliseconds such as 1454004926439 or null. Only included if there was a modification to some part of the loan.
    
    UltraCompact -
      This is returned from api/loans.php with the format 0. Simply a list of loan ids, with no additional information.
      
      Response Code: 200 OK 
      Response Json Object:
        {
          "result_type": "LOANS_ULTRACOMPACT",
          "success": true,
          "loans": [
            <loan_id>,
            <loan_id>,
            ...
          ]
        }
    Compact -
      This is returned from api/loans.php with the format 1. This contains all information about the loan, but uses arrays to reduce return size and reduce parsing time.
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "LOANS_COMPACT",
          "success": true,
          "loans": [
            [<loan_id>, <lender_id>, <borrower_id>, <principal_cents>, <principal_repayment_cents>, <unpaid>, <created_at>, <updated_at>, <deleted>, <deleted_at>, <deleted_reason>, 
            <new_lender_id>, <new_borrower_id>, <new_principal_cents>, <new_principal_repayment_cents>, <new_unpaid>, <new_deleted>, <new_deleted_reason>, <new_updated_at>, <new_deleted_at>],
            ...
          ]
        }
        
    Standard -
      This is returned from api/loans.php with the format 2. This contains all information about the loan in a human-readable format.
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "LOANS_STANDARD",
          "success": true,
          "loans": [
            {
              "loan_id": <loan_id>,
              "lender_id": <lender_id>,
              "borrower_id": <borrower_id>,
              "principal_cents": <principal_cents>,
              "principal_repayment_cents": <principal_repayment_cents>,
              "unpaid": <unpaid>,
              "created_at": <created_at>,
              "updated_at": <updated_at>,
              "deleted": <deleted>,
              "deleted_at": <deleted_at>,
              "deleted_reason": "<deleted_reason>",
              "new_lender_id": <new_lender_id>,
              "new_borrower_id": <new_borrower_id>,
              "new_principal_cents": <new_principal_cents>,
              "new_principal_repayment_cents": <new_principal_repayment_cents>,
              "new_unpaid": <new_unpaid>,
              "new_deleted": <new_deleted>,
              "new_deleted_reason": "<new_deleted_reason>",
              "new_updated_at": <new_updated_at>,
              "new_deleted_at": <new_deleted_at>
            },
            ...
          ]
        }
        
    Extended -
      This is returned from api/loans.php with the format 3. This contains all information about the loan as well as usernames in a human-readable format. 
      
      Response Code: 200 OK
      Response Json Object:
        Let <thread> be the thread which the $loan command is in.
        Let <lender_name> be a username of a lender, such as "reddituser123".
        Let <borrower_name> be a username of a borrower, such as "reddituser123".
        Let <new_lender_name> be a new username of a lender, such as "reddituser123". Only included if there was a modification to some part of the loan.
        Let <new_borrower_name> be a new username of a borrower, such as "reddituser123". Only included if there was a modification to some part of the loan.
        
        {
          "result_type": "LOANS_EXTENDED",
          "success": true,
          "loans": [
            {
              (everything from Standard)
              "thread": "<thread>",
              "lender_name": "<lender_name>",
              "borrower_name": "<borrower_name>",
              "new_lender_name": "<new_lender_name>",
              "new_borrower_name": "<new_borrower_name>"
            },
            ...
          ]
        }
        
  Users -
    Let <user_id> be a id of a user, such as 736 or 28.
    Let <claimed> be a boolean such as true or false.
    Let <created_at> be a utc timestamp in milliseconds such as 1454004926439 or null.
    Let <updated_at> be a utc timestamp in milliseconds such as 1454004926439 or null.
    
    Let <email> be an email of a user, such as "johndoe@gmail.com". This is only returned for yourself, with a sharecode, or if you are a moderator.
    Let <name> be the name of a user, such as "John Doe". This is only returned for , with a sharecode, or if you are a moderator.
    Let <street_address> be the street address of a user, such as "1600 Pennsylvania Avenue NW". This is only returned for yourself, with a sharecode, or if you are a moderator.
    Let <city> be the city of a user, such as "Washington D.C.". This is only returned for yourself, with a sharecode, or if you are a moderator.
    Let <state> be the state of a user, such as "Washington D.C." or "N/A". This is only returned for yourself, with a sharecode, or if you are a moderator.
    Let <zip> be the zip of a user, such as "20500". This is only returned for yourself, with a sharecode, or if you are a moderator.
    Let <country> be the country of a user, such as "United States" or "U.S.". This is only returned for yourself, with a sharecode, or if you are a moderator.
    
    UltraCompact -
      This is returned from api/users.php with the format 0. This contains just a list of user ids, with no additional information.
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "USERS_ULTRACOMPACT",
          "success": true,
          "users": [<user_id>, <user_id>, ...]
        }
        
    Compact -
      This is returned from api/users.php with the format 1. This contains all information about the user, but uses arrays to reduce return size and reduce parsing time.
      
      Response Code: 200 OK
      Response Json Object: 
        {
          "result_type": "USERS_COMPACT",
          "success": true,
          "users": [
            [<user_id>, <claimed>, <created_at>, <updated_at>, "<email>", "<street_address>", "<city>", "<state>", "<zip>", "<country>"],
            ...
          ]
        }
        
    Standard -
      This is returned from api/users.php with the format 3. This contains all information about the user in a human-readable format. 
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "USERS_STANDARD",
          "success": true,
          "users": [
            {
              "user_id": <user_id>,
              "claimed": <claimed>,
              "created_at": <created_at>,
              "updated_at": <updated_at>,
              "email": "<email>",
              "street_address": "<street_address>",
              "city": "<city>",
              "state": "<state>",
              "zip": "<zip>",
              "country": "<country>"
            }
          ]
        }
        
    Extended - 
      This is returned from api/users.php with the format 3. This contains all information about the user as well as his username in a human-readable format. 
      
      Response Code: 200 OK
      Response Json Object:
        Let <username> be the username of the user, such as "reddituser123".
        {
          "result_type": "USERS_EXTENDED",
          "success": true,
          "users": [
            {
              (everything from Standard)
              "username": "<username>"
            },
            ...
          ]
        }
        
  Usernames -
    Let <username_id> be an id of a username, such as 57 or 192.
    Let <username> be a username of a user, such as "reddituser123".
    Let <user_id> be the id of a user, such as 736 or 28. 
    Let <created_at> be a utc timestamp in milliseconds such as 1454004926439 or null.
    Let <updated_at> be a utc timestamp in milliseconds such as 1454004926439 or null.
    
    UltraCompact -
      This is returned from api/usernames.php with the format 0. This contains just a list of username ids, with no additional information.
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "USERNAMES_ULTRACOMPACT",
          "success": true,
          "usernames": [<username_id>, <username_id>, ...]
        }
        
    Compact -
      This is returned from api/usernames.php with the format 1. This contains all information about the username, but uses arrays to reduce return size and reduce parsing time.
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "USERNAMES_COMPACT",
          "success": true,
          "usernames": [
            [<username_id>, "<username>", <user_id>, <created_at>, <updated_at>],
            ...
          ]
        }
    Standard - 
      This is returned from api/usernames.php with the format 3. This contains all information about the username in a human-readable format. 
      
      Response Code: 200 OK
      Response Json Object:
        {
          "result_type": "USERNAMES_STANDARD",
          "success": true,
          "usernames": [
            {
              "username_id": <username_id>,
              "username": "<username>",
              "user_id": <user_id>,
              "created_at": <created_at>,
              "updated_at": <updated_at>
            },
            ...
          ]
        }
  
