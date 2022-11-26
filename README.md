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

Obviously, **person accounts need to be enabled** in your org for any of this to be relevant. 
See: [https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5)

### Here we go

Go to **Setup**, **Flows** and click on the **New Flow** button. Select **Screen Flow** and **Create**. _If you need screenshots for this bit, go back to [Trailhead](https://trailhead.salesforce.com/) and get across the basics first_

This is optional, but first thing I did was **add a Screen element** to display a warning before anything is processed. You can add whatever text you want here, or copy mine. 

![1 Convert Person Account](https://user-images.githubusercontent.com/119096189/204076901-3a6afdeb-44ed-4589-8519-6c263a7e1d98.png)

The next element I added was a **Get Records** to get the field values of the current **Contact**. This is not optional. 

![2 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077025-9ec7444f-a03e-4e47-9f85-6ff560ca023b.png)

Since this flow will be triggered by an action on the contact record, the ID is defined as the variable **recordId**: 
- Resource type: Variable 
- API Name: recordId
- Data Type: Text
- Available for input: checked

If you looked at [this comment on Reddit by Callister](https://www.reddit.com/r/salesforce/comments/dbhxnp/comment/f21t7oc/?utm_source=share&utm_medium=web2x&context=3) that I referred to earlier under the Acknowledgements, you will see that: 

> **4. The Reports To field on the Contact must be blank. The Contact is not set as a Reports To for any other contacts**


Therefore we need to add a **decision** element to check whether the **Reports To** field is blank. If it is not blank, the user cannot convert the contact and will get an error that tells them why. 
![3 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077522-60616649-e83d-4247-8aa2-6ba0d0e6a303.png)

> **Note that I we didn't check whether the Contact is a Reports To for other contacts. I told you I am lazy.**


Add a **screen** element under the Cannot Convert decision branch which displays some text to explain to the user that they need to remove whoever is in Reports To. That branch can **end** there. 
![4 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077776-784fd9d8-deee-4b3f-bcf1-41af03d4cbb9.png)

The **Default Outcome** is our happy path and now begins a series of DML elements to convert this bad boy. 

To make sure I have a 1:1 relationship between the Contact and its 
