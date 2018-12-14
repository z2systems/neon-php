neon-php
========
The PHP library for the NeonCRM API.
## Why use it?
I have built this library to speed up development on the NeonCRM API. This library streamlines the process of building queries and parsing the server response, particularly for searching requests. It also handles authentication through the login and storing of session id tokens.
## How to use it
### Include the library
Include neon.php in your php file.
```PHP
require_once('neon.php');
```
### Instantiate the class
Create a new instance of the Neon class.
```PHP
$neon = new Neon();
```
## Class Methods
The following class methods are available for use:
* login
* go
* search

### login() - Server Authentication
This is necessary to make any subsequent requests through the API. Authentication is done using an orgId and an API key. More information about obtaining these can be found at https://developer.neoncrm.com/api/getting-started/api-keys/
```PHP
$keys = [
  'orgId'=>'myorg', 
  'apiKey'=>'xxxxxx'
  ]; 
$neon->login($keys);
```
On the successful completion of this request, a Neon API session ID will be stored as a session variable. The library will automatically add this to subsequent requests. The session ID expires after 30 minutes of inactivity.
### go() - API Request
This method is used to make a general API request. All methods described in the REST API documentation (https://developer.neoncrm.com/api/) will work. This method expects a structured associative array as an argument. It includes the following keys:
* Method: The API method used
* Parameters: Individual fields passed to the API as parameters
* RepeatingParameters: Any set of parameters where the parameter name must be sent more than once in a request. Typically this is necessary when including multiple custom fields in a single request.

Requests use this format:
```PHP
$request = [ 
  'method' => 'account/createIndividualAccount', 
  'parameters' => [
    'individualAccount.primaryContact.firstName' => 'Ambrose',
    'individualAccount.primaryContact.lastName' => 'Burnside',
  ],
  'repeatingParameters' => [
    [
      'individualAccount.customFieldDataList.customFieldData.fieldId' => 45,
      'individualAccount.customFieldDataList.customFieldData.fieldOptionId' => 47,
      'individualAccount.customFieldDataList.customFieldData.fieldValue' => '',
    ],
    [
      'individualAccount.customFieldDataList.customFieldData.fieldId' => 22,
      'individualAccount.customFieldDataList.customFieldData.fieldOptionId' => '',
      'individualAccount.customFieldDataList.customFieldData.fieldValue' => 'EXAMPLETEXT',
    ],
  ]
];
$result = $neon->go($request);
```

### search() - List Requests

This method greatly reduces the effort necessary to build search queries and iterate through the results. It works with the following API methods:
* [listAccounts](https://developer.neoncrm.com/api/accounts/searching-for-accounts/list-accounts/)
* [listDonations](https://developer.neoncrm.com/api/donations/list-donations/)
* [listEvents](https://developer.neoncrm.com/api/events/list-events/)
* [listMemberships](https://developer.neoncrm.com/api/memberships/list-members/)

It expects a different format of array from the go() method, as this type of API request is more complex.

This is the format for this type of request:
```PHP
$search = [
  'method' => 'account/listAccounts', 
  'criteria' => [
    ['First Name', 'EQUAL', 'John'],
    ['Last Name', 'EQUAL', 'Smith'],
  ],
  'columns' => [
    'standardFields' => ['Account ID', 'First Name', 'Last Name'],
    'customFields' => [655, 361],
  ],
  'page' => [
    'currentPage' => 1,
    'pageSize' => 20,
    'sortColumn' => 'Account Id',
    'sortDirection' => 'ASC',
  ],
];
$result = $neon->search($search);
```
