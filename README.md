HeadooMailjetBundle
=========

HeadooMailjetBundle is a Symfony2 Bundle, mainly aimed at processing [MailJet API v3](http://dev.mailjet.com).

## Installation !!

1. Add to `composer.json` file :
	```json
	{
	    "require": {
	        "headoo/headoo-mailjet-bundle" : "1.0.x-dev"
	    }
	}
	```	

2. Install our dependencies using :
	```bash
	$ php composer.phar update
	```

3. Register the bundle in your `app/AppKernel.php`:
   ``` php
    <?php
    ...
    public function registerBundles()
    {
        $bundles = array(
            ...
            new Headoo\HeadooMailjetBundle\HeadooMailjetBundle(),
            ...
        );
    ...
   ```

## Usage

### RESTful API as a service

Add api/secret keys in `parameters.yml` and it's up!

```yaml
# app/parameters.yml
    mailjet_api_key: <your api key>
    mailjet_secret_key: <your secret key>
```

Now you can access RESTful API via DIC by calling `headoo_mailjet_wrapper` service:
```php
	$mailjet = $this->container->get('headoo_mailjet_wrapper');
```

### Responses (after using API)

You can access the following return values:

- `$mailjet->_response_code` : like `200` for sucess
- `$mailjet->_response` : return Mailjet datas (like the ID) 

## API examples usage

### Email informations

```php
    $params = array(
        "method" => "VIEW",
        "ID" => $id
    );

    $resutl = $mailjet->message($params);
```

### Send email

- Send an email :
```php
	$params = array(
        "method" => "POST",
        "from" => "ms.mailjet@example.com",
        "to" => "mr.mailjet@example.com",
        "subject" => "Hello World!",
        "text" => "Greetings from Mailjet."
    );

    $result = $mailjet->sendEmail($params);
```

- Send an email with some attachments (absolute paths on your computer) :
```php
    $params = array(
        "method" => "POST",
        "from" => "ms.mailjet@example.com",
        "to" => "mr.mailjet@example.com",
        "subject" => "Hello World!",
        "text" => "Greetings from Mailjet.",
        "attachment" => array("@/path/to/first/file.txt", "@/path/to/second/file.txt")
    );

    $result = $mailjet->sendEmail($params);
```

- Send an email with some inline attachments (absolute paths on your computer) :
```php
    $params = array(
        "method" => "POST",
        "from" => "ms.mailjet@example.com",
        "to" => "mr.mailjet@example.com",
        "subject" => "Hello World!",
        "html" => "<html>Greetings from Mailjet <img src=\"cid:photo1.jpg\"><img src=\"cid:photo2.jpg\"></html>",
	"inlineattachment" => array("@/path/to/photo1.jpg", "@/path/to/photo2.jpg")
    );

    $result = $mailjet->sendEmail($params);
}
```

### Account Settings

- Get your profile information :
```php
    $result = $mailjet->myprofile();
```

- Update the field ```AddressCity``` of your profile :
```php
    $params = array(
        "method" => "PUT",
        "AddressCity" => "New York"
    );

    $result = $mailjet->myprofile($params);
```

### Contact Lists

- Print the list of your contacts :
```php
    $result = $mailjet->contact();
}
```

- Update your contactData resource with ID ```$id```, using arrays :
```php
	$data = array(array('Name' => 'lastname', 'Value' => 'Jet'), array('Name' => 'firstname', 'Value' => 'Mail'));
	$params = array(
		'ID' => $id,
		'Data' => $data,
		'method' => 'PUT'
	);

	$result = $mailjet->contactdata($params);
```

- Create a list with name ```$Lname``` :
```php
    $params = array(
    	"method" => "POST",
    	"Name" => $Lname
    );

    $result = $mailjet->contactslist($params);
```

- Get a list with ID ```$listID``` :
```php
    $params = array(
    	"method" => "VIEW",
    	"ID" => $listID
    );

    $result = $mailjet->contactslist($params);
```
Note : You can use unique fields of resources instead of IDs, like
```"unique" => "test@gmail.com"``` in your ```params``` array for this example

- Create a contact with email ```$Cemail``` :
```php
    $params = array(
    	"method" => "POST",
    	"Email" => $Cemail
    );

    $result = $mailjet->contact($params);
```

- Add the contact which ID is ```$contactID``` to the list which ID is ```$listID``` :
```php
    $params = array(
    	"method" => "POST",
    	"ContactID" => $contactID,
    	"ListID" => $listID,
    	"IsActive" => "True"
    );

    $result = $mailjet->listrecipient($params);
```

- Delete the list which ID is ```$listID``` :
```php
    $params = array(
    	"method" => "DELETE",
    	"ID" => $listID
    );

    $result = $mailjet->contactslist($params);
```

### Newsletters

- Get the HTML body of the newsletter ```$newsletter_id``` :
```php
    $params = array(
        "method" => "GET",
        "ID" => $newsletter_id
    );

    $result = $mailjet->getHTMLbody($params);
```
Note : You need to put an underscore character before your parameters that are not filters in a GET request

- Add an HTML body ```$html_content``` to a newsletter ```$newsletter_id``` :
```php
    $params = array(
        "method" => "PUT",
        "ID" => $newsletter_id,
        "html_content" => $html_content
    );

    $result = $mailjet->addHTMLbody($params);
```
