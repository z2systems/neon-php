neon-php
========
The PHP library for the NeonCRM API.
##Why use it?
I have built this library to speed up development on the NeonCRM API. This library streamlines the process of building queries and parsing the server response, particularly for searching requests. It also handles authentication through the login and storing of session id tokens.
##How to use it
###Include the library
Include neon.php in your php file.
```PHP
require_once('neon.php');
```
###Instantiate the class
Create a new instance of the Neon class.
```PHP
$neon = new Neon();
```
##Class Methods
The following class methods are available for use:
* login
* go
* search

###login() - Server Authentication
This is necessary to make any subsequent requests through the API. Authentication is done using an orgId and an API key. More information about obtaining these can be found at http://help.neoncrm.com/api/keys
```PHP
$keys = array(
  'orgId'=>'myorg', 
  'apiKey'=>'xxxxxx'
  ); 
$neon->login($keys);
```
On the successful completion of this request, a Neon API session ID will be stored as a session variable. The library will automatically add this to subsequent requests. The session ID expires after 30 minutes of inactivity.
###go() - API Request
This method is used to make a general API request. All methods described in the REST API documentation (http://help.neoncrm.com/api/rest) will work. This method expect a structured associative array as an argument. It follows this format:
```PHP
$request = array( 
  'method' => 'common/listCustomFields', 
  'parameters' => array(
    'searchCriteria.component' => 'Account',
    'searchCriteria.search.name' => 'Favorite Color',
    ),
  );
$result = $neon->go($request);
```
###search() - List Requests
This method greatly reduces the effort necessary to build search queries and iterate through the results. It works with the following API methods:
* [listAccounts](http://help.neoncrm.com/api/rest/account/listAccounts)
* [listDonations](http://help.neoncrm.com/node/174)
* [listEvents](http://help.neoncrm.com/api/rest/events/listEvents)
* [listMemberships](http://help.neoncrm.com/node/181)

It expects a different format of array from the go() method, as this type of API request is more complex.
This is the format for this type of request:
```PHP
$search = array( 
  'method' => 'account/listAccounts', 
  'criteria' => array(
    array( 'First Name', 'EQUAL', 'John'),
    array( 'Last Name', 'EQUAL', 'Smith'),
  ),
  'columns' => array(
    'standardFields' => array('Account Id', 'First Name', 'Last Name'),
    'customFields' => array(655, 361),
  ),
  'page' => array(
    'currentPage' => 1,
    'pageSize' => 20,
    'sortColumn' => 'Account Id',
    'sortDirection' => 'ASC',
  ),
);
$result = $neon->search($search);
```
