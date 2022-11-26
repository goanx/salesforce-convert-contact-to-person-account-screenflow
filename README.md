# private-test
This is my first attempt at writing something. I recently had the misfortune of needing to convert a few contacts into person accounts. I did find some very good resources online on how to do this through doing some data imports/updates, however this is a very cumbersome process for converting only a few contacts and I am lazy and want the business users to start taking care of this. 

So I created a screen flow that can be used with an action on contact records that will convert them into person accounts. 

### Acknowledgements 

I mentioned some online resources which helped me understand the person account data model and the conversion process much better - without this I wouldn't have been able to figure out how to construct this flow. 

If you would like to test your skills, open these up in some new tabs, stop reading my instructions and give it a try. 

- [Salesforce Tech Talk: How To Convert a Contact to a Person Record on 501Partners](https://501partners.com/salesforce-tech-talk-how-to-convert-a-contact-to-a-person-record/)

- [This comment on Reddit by Callister](https://www.reddit.com/r/salesforce/comments/dbhxnp/comment/f21t7oc/?utm_source=share&utm_medium=web2x&context=3)

### Why a screen flow? 

The org I was working on has more than one person account record type, so I wanted to enable the user to pick which one to use (although I didn't add that selection in these instructions). That and a screen flow adds some flexibility for warnings, instructions and some extra user input and logic if that needs to be built into it down the track. Hopefully future me appreciates it. 

### Before you start

Obviously, person accounts need to be enabled in your org for any of this to be relevant. 
See: [https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5)

### Here we go

Go to *Setup*, *Flows* and click on the *New Flow* button. Select *Screen Flow* and *Create*. _If you need screenshots for this bit, go back to [Trailhead](https://trailhead.salesforce.com/) and get across the basics first_

