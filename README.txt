This is the website portion of the LoansBot. It is created from an API perspective first since that is its most popular usecase. Then, it has a index, login page, and query page. The query page is a very light wrapper around the database - it allows you to choose what you are trying to do, and it calls the appropriate api.

/site/ - 
  Contains all site code
  
  index.php -
    The entry point to the site. Points to logging in an the query page, as well as making it clear if you are logged in.
    
  login.php - 
    Shows the appropriate form for logging in, or the appropriate form to request a claim code.
    
  claim.php -
    Shows the appropriate form for claiming your account 
  
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
      Allows the user to request a claim code be sent to his account via the LoansBot.
      
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
        
    loans.php - 
      Allows the user to fetch loan information.
      
      Type: GET
      Cookies: PHPSESSID
      Params:
        format                        - Either 0 (ultra-compact), 1 (compact), 2 (standard), or 3 (extended). Default 2 (standard)
        limit                     - Limits the number of results. Default 10 to ensure expensive queries are intended. 0 for no limit.
        
        
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
        principal_repayment_cents     - The exact amount of principal_repayment_cents of the loans. Default 0 (ignore)
        unpaid                        - If the loans should be unpaid. 0 for false, 1 for true. Default -1 (ignore)
        
        
        modify                        - States whether the loans that have been returned from the above query are going to be modified. Either 0 (false) or 1 (true). Default 0.
        modify_reason                 - The reason a modification is happening. Minimum length of 5 characters. 
        set_borrower_id               - The id of the new borrower. Default 0 (ignore)
        set_lender_id                 - The id of the new lender. Default 0 (ignore)
        set_borrower_name             - The name of the new borrower. Default "" (ignore)
                                        CANNOT be used with set_borrower_id
        set_lender_name               - The name of the new lender. Default "" (ignore)
                                        CANNOT be used with set_lender_id 
        set_principal_cents           - Sets the principal cents of the loan. $100 = 10000 cents. Default 0 (ignore)
        set_principal_repayment_cents - Sets the principal repayment cents of the loan. $100 = 10000 cents. Default 0 (ignore)
        set_unpaid                    - Sets if the loans are marked as unpaid, 1 for true 0 for false. Default -1 (ignore)
        set_deleted                   - Sets if the loan is deleted, 1 for true, 0 for false. Default -1 (ignore). MUST include set_deleted_reason.
        set_deleted_reason            - Sets the loan deleted message. Default "" (ignore). Minimum length of 5 characters.
        
      Result: 
        Results/Failure, Results/Loans/UltraCompact, Results/Loans/Compact, Results/Loans/Standard, Results/Loans/Extended
Results
  This describes the types of things that the api returns.
  Note that for UTC Timestamps, they may be null.
  
  Success -
    This signifies the operation was successful.
    
    Response Code: 200 OK
    Response Json Object: 
      {
        "result_type": 0
        "success": true
      }
  
  Failure - 
    This signifies the operation was unsuccessful.
    
    Response Code: 400 Bad Request
    Response Json Object:
      Let <error_message> be a string containing a human-readable error message, such as "You must be logged in to do that." or "No account with that username/password combination could be found."
      Let <error_identifier> be a string that will not change when the details of the error message change, such as "NOT_LOGGED_IN", or "ACCOUNT_NOT_FOUND".
      
        {
          "result_type": 1,
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
    Let <updated_at> be a utc timestamp in milliseconds such as 1454004926439 or null. This is the original value.
    Let <deleted_at> be a utc timestamp in milliseconds such as 1454004926439 or null. Only included for moderators.
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
          "result_type": 2,
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
          "result_type": 3,
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
          "result_type": 4,
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
            }
          ]
        }
        
    Extended -
      This is returned from api/loans.php with the format 3. This contains all information about the loan as well as usernames in a human-readable format. 
      
      Response Code: 200 OK
      Response Json Object:
        Let <lender_name> be the username of the lender, such as "reddituser123".
        Let <borrower_name> be the username of the borrower, such as "reddituser123".
        Let <new_lender_name> be the new username of the lender, such as "reddituser123". Only included if there was a modification to some part of the loan.
        Let <new_borrower_name> be the new username of the borrower, such as "reddituser123". Only included if there was a modification to some part of the loan.
        
        {
          "result_type": 5,
          "success": true,
          "loans": [
            (everything from Standard)
            "lender_name": "<lender_name>",
            "borrower_name": "<borrower_name>",
            "new_lender_name": "<new_lender_name>",
            "new_borrower_name": "<new_borrower_name>"
          ]
        }