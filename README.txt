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
        Results/Success, or Results/Failure
    request_claim_code.php -
      Allows the user 
  
  
Results
  This describes the types of things that the api returns.
  
  Success -
    This signifies the operation was successful.
    
    Response Code: 200
    Response Json Object: 
      {
        "success" = true
      }
  
  Failure - 
    This signifies the operation was unsuccessful.
    
    Response Code: 400 Bad Request
    Response Json Object:
      Let <error_message> be a string containing a human-readable error message, such as "You must be logged in to do that." or "No account with that username/password combination could be found."
      Let <error_identifier> be a string that will not change when the details of the error message change, such as "NOT_LOGGED_IN", or "ACCOUNT_NOT_FOUND".
      
        {
          "success" = false,
          errors: [
            {
              "error_message": <error_message>,
              "error_identifier": <error_identifier>
            },
            {
              "error_message": <error_message>,
              "error_identifier": <error_identifier>
            }
          ]
        }
    
    