This is the website portion of the LoansBot. It is created from an API perspective first since that is its most popular usecase, followed by creating a basic graphical interface to access that API. This is created in a combination PHP and 

/site/ - Contains all site code
  index.php - Banner page, shows some graphs and stuff.
  login.php - Shows the forms for the login page, calls /api/login.php
    

  api/
    login.php - 
      POST username, password - returns cookie to set session id
    users/
      username_by_userid.php -
        GET userid - returns username
      userid_by_username.php -
        GET username - returns userid

      set_info.php (ADMIN ONLY) - 
        POST username,email,name,street_address,city,state,zip,country
      request_claim.php (SELF/ADMIN ONLY) -
        POST user_id
    loans/
      loan_by_loanid.php -
        GET loanid (int) - returns loan information
      loans_by_users.php
        GET borrower_id (int), lender_id (int), strict (BOOL true/false) - return loans between borrower (strict=AND, otherwise=OR) lender
      loans_by_time.php
        GET after (long ms utc or -1), before (long ms utc or -1) - return loans after after and before before, -1 meaning ignore

    
    
  
