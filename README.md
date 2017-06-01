# Kiwi Crate: Digital Thank You Page
#### By: Eddie Chou

## Design Diagram

![Thank You page Design Diagram](https://s1.postimg.org/s3izjyw7j/Kiwi_Crate_-_Thank_You_Page.png)

## Database Design

All the information we need (names and emails) is accessible by querying our database using the Order_ID.

![Database Design](https://s14.postimg.org/6n1yjzxch/DB_Table.png)

## Pseudocode & High-level Explanations

#### Send 'Give thank you email' to a giftee 7 days after their gift’s shipping date:

- **Option 1:** We can use a service like SparkPost or Mandrill to schedule emails to send 7 days after the shipping date (we may choose a specific time during the day as well). We can schedule the email (by running the relevant API calls) whenever the shipping date is determined.

```
// Whenever shipping date is determined, use SparkPost/Mandrill API calls
// Populate destination address with the order’s <Giftee email>
// Populate subject with ‘An easy way to say thanks to <Customer’s first 
   name> <Customer’s last name> for your crate!’
// Generate specific page link for this giftee and gifter (by using the order number)
// Populate body with default text and a specific link to the ‘Thank You Page’ 
// Send API call to service with sender, recipient, subject, and body
```

- **Option 2:** We can have a web-worker periodically checking our database for emails that have yet to be scheduled and scheduling the emails as needed. This can offload processing from our application server.

```
// Periodically query database for all orders that have not had emails scheduled yet (potentially containing a boolean Email_Scheduled field)
// Schedule emails that have not been sent as in option 1
// Set Email_Scheduled for the Order to true
```

#### Thank You Page: allows giftee to customize thank you note to make it more personalized

The link we send the giftee would contain the order number (e.g. kiwicrate.com/thankyou?order=00001). This allows us to access the relevant information (the gifter's and giftee's names and emails) to **pre-populate** the email with. There are some security concerns here as a user can simply increment the number and access other orders' thank you pages, so we should **hash** the order number so it would look more like kiwicrate.com/thankyou?order=HX72by7802d. By using this query parameter, we can have our server send back customized pages with pre-populated information using a templating engine.

#### Allow giftee to upload a photo or choose from pre-design templates

Here, we'd have to decide whether we'd like the images to persist past the email or not. If so, we'd want to store the uploaded images in our own database or upload it to some service that holds the images for us and returns a link that we can store in our database.

#### Allow the giftee to write a personalized note
Note should be plain text - and shouldn’t allow for any scripting

```
/* HTML */
// Create a form
  // Create a file input button that allows user to search their computer for a file
      // If we'd like to store the image
          // Make an API call to the storage service and save the returned URL to our DB
    // Display various pre-designed template images for the user to select if they wish
  // Create a form field for the thank you note
  // Create submit button (send thank you)
    
/* CSS */
// Form styling
// Button styling
// Make sure form is large enough to write a meaningful note / story

/* JS */
// Submit button event handler
  // Collect all the form data
      // Make sure to escape all form data using jquery .text() or some other method
  // Send an API call to an email service with the proper sender, subject, body, and sender's email

```

**\*\*The rest of the requirements have been considered in previous responses.**

#### Text area should be large enough to write a meaningful note / story 
Note should be prefilled with Child name, gifter email and default thank you message.

```
Dear <Gifter Name>, 
 
Thank you for my Kiwi Crate. I had a lot of fun making my project. 
 
Thanks,
<child name>
```

When the giftee clicks on “Send Thank You” button, an email should be sent to the gifter: 

```
Subject: <Child Name> sent a thank you note to you!
Body: 
  <Child Name> sent thank you note to you!
        <Check it out>
```

On clicking the “Check it out” button, gifter should be taken to their “Thank You” note page. It should have the thank you card that was designed by the giftee.


## Suggestions for improvement

1) Integration with social media to allow the gifter to post the email or image to various social media sites. This doubles as a way to market our products, though we should consider privacy concerns (the giftee may not want them posted).
2) Consider storing a Nickname and/or a Relationship field for the Customer in the database. This way, we can personalize any messages/emails with “Uncle Joe” or “Grandma”.
3) Option to upload a video. 
4) Consider various customizable options for either a parent/caretaker sending the thank you email or the child sending the thank you (if old enough).
  - This would include having database fields for either parent/caretaker or child email addresses.