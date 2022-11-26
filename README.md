# Salesforce: Convert a Contact to Person Account

I recently had the misfortune of needing to convert a few contacts into person accounts. I did find some very good resources online on how to do this through doing some data imports/updates, however this is a very cumbersome process for converting only a few contacts and I am lazy and want the business users to start taking care of this. 

So I created a screen flow that can be used with an action on contact records that will convert them into person accounts. 

## Acknowledgements 

I mentioned some online resources which helped me understand the person account data model and the conversion process much better - without this I wouldn't have been able to figure out how to construct this flow. 

If you would like to test your skills, open these up in some new tabs, stop reading my instructions and give it a try. 

- [Salesforce Tech Talk: How To Convert a Contact to a Person Record on 501Partners](https://501partners.com/salesforce-tech-talk-how-to-convert-a-contact-to-a-person-record/)

- [This comment on Reddit by Callister](https://www.reddit.com/r/salesforce/comments/dbhxnp/comment/f21t7oc/?utm_source=share&utm_medium=web2x&context=3)

## Why a screen flow? 

The org I was working on has more than one person account record type, so I wanted to enable the user to pick which one to use (although I didn't add that selection in these instructions). That and a screen flow adds some flexibility for warnings, instructions and some extra user input and logic if that needs to be built into it down the track. Hopefully future me appreciates it. 

## Before you start

Obviously, **person accounts need to be enabled** in your org for any of this to be relevant. 
See: [https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.account_person.htm&type=5)

If person accounts is not enabled in your org, it is really important that you ensure you read the considerations in the link above and test in a full or partial sandbox to see the implications. If your org is on the NPSP, I'm sorry, no person accounts for you. 

## Here we go

Go to **Setup**, **Flows** and click on the **New Flow** button. Select **Screen Flow** and **Create**. _If you need screenshots for this bit, go back to [Trailhead](https://trailhead.salesforce.com/) and get across the basics first_

### 1. Screen element: Warning

This is optional, but first thing I did was **add a Screen element** to display a warning before anything is processed. You can add whatever text you want here, or copy mine. 

![1 Convert Person Account](https://user-images.githubusercontent.com/119096189/204076901-3a6afdeb-44ed-4589-8519-6c263a7e1d98.png)

### 2. Get Contact

The next element I added was a **Get Records** to get the field values of the current **Contact**. This is not optional. 

![2 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077025-9ec7444f-a03e-4e47-9f85-6ff560ca023b.png)

#### 2a. Variable: recordId

Since this flow will be triggered by an action on the contact record, the ID is defined as the variable **recordId**: 
- Resource type: Variable 
- API Name: recordId
- Data Type: Text
- Available for input: checked

### 3. Check 'Reports To' field on the Contact is blank 

If you looked at [this comment on Reddit by Callister](https://www.reddit.com/r/salesforce/comments/dbhxnp/comment/f21t7oc/?utm_source=share&utm_medium=web2x&context=3) that I referred to earlier under the Acknowledgements, you will see that: 

> **4. The Reports To field on the Contact must be blank. The Contact is not set as a Reports To for any other contacts**

Therefore we need to add a **decision** element to check whether the **Reports To** field is blank. If it is not blank, the user cannot convert the contact and will get an error that tells them why. 

![3 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077522-60616649-e83d-4247-8aa2-6ba0d0e6a303.png)

> **Note that I we didn't check whether the Contact is a Reports To for other contacts. I told you I am lazy.**

#### 3a. Screen element: Cannot convert 

Next add a **screen** element under the Cannot Convert decision branch which displays some text to explain to the user that they need to remove whoever is in Reports To. That branch can **end** there. 

![4 Convert Person Account](https://user-images.githubusercontent.com/119096189/204077776-784fd9d8-deee-4b3f-bcf1-41af03d4cbb9.png)

### 4. Create a 1:1 relationship with an Account

The **Default Outcome** is our happy path - now begins a series of DML elements to convert this bad boy. 

To make sure there is a 1:1 relationship between the Contact and its Account, first we create a new non-person Account to associate with the Contact. 

#### 4a. Get the Business Account Record Type ID

Add a **Get Records** element to get the Business Account Record Type ID: 
- Object: Record Type
- Filters (AND): 
   - SobjectType equals Account
   - IsPersonType equals False

![5 Convert Person Account](https://user-images.githubusercontent.com/119096189/204079006-86f38c26-0f82-421f-b794-eac37aa0dda8.png)

#### 4b. Create the Business Account 

Add a **Create Records** element to create a Business Account to associate with the Contact: 
- Object: Account
- Set Field Values (Field <- Value): 
   - Name <- Get Contact Full Name
   - OwnerId <- Get Contact Owner ID
   - RecordTypeId <- ID from step 4a above

![6 Convert Person Account](https://user-images.githubusercontent.com/119096189/204079374-87264371-90ed-44d8-84a6-c3887a34dfed.png)

#### 4c. Update the Contact with the new Account

Add a **Update Records** element to update the Contact with the new Account that was just created: 
- Object: Contact
- Filters (AND): 
   - Id equals Get Contact ID (or recordId)
- Set Field Values (Field <- Value): 
   - AccountId <- AccountId from step 4b above

![7 Convert Person Account](https://user-images.githubusercontent.com/119096189/204079515-e27b929d-8c91-453a-b0f6-5c4273d0fec6.png)

### 5. Convert to Person Account 

Now that our Contact definitely has a 1:1 relationship with an Account that has the same Owner, we can get onto the converting! 

#### 5a. Get the Person Account Record Type ID

Add a **Get Records** element to get the Person Account Record Type ID: 
- Object: Record Type
- Filters (AND): 
   - SobjectType equals Account
   - IsPersonType equals True

> **Note**: The above will work fine for an org with only 1 person account record type. Stating the obvious, if your org has more than one, you will need to add further filter criteria to specify which record type you want to use. 

![8 Convert Person Account](https://user-images.githubusercontent.com/119096189/204079595-3b6d0700-d332-4285-b912-a83a09097f46.png)

#### 5b. Convert the Business Account to a Person Account

Add an **Update Records** element to update the Business Account Record Type to a Person Account: 
- Object: Account
- Filter: 
   - Id equals AccountId from step 4b above
- Set Field Values (Field <- Value): 
   - RecordTypeId <- Id from step 5a above

![9 Convert Person Account](https://user-images.githubusercontent.com/119096189/204079809-eaa24be3-2498-4fde-8d9c-9f1d7c73def6.png)

### 6. Finally

After all of that, in my org I added the [Update Screen flow action from UnofficialSF.com](https://unofficialsf.com/update-screen/) to refresh the page, so that the user knows it's complete. 

Alternatively you can add a screen element that says it's done, and thank the user for their commitment to the upkeep of data quality. And tell them to refresh the page. 

### 7. The last bit - create an action 

In **Setup** -> **Object Manager** -> **Contact**, click on **Buttons, Links and Actions** and **New Action**. 
- Action Type: Flow
- Flow: whatever you named your flow 
- Label: Convert to Person Account _(or whatever makes you feel good)_

Add this action to each contact page layout that you need it on, or if you have dynamic actions enabled, add it to the lightning record pages. 

## Finally finally

If the Account that was linked to the original contact record had only that one contact associated with it, you will have an 'orphaned' account. Different businesses will have different ways of dealing with this, so you can build that logic into the flow (which is why this is a screen flow). For reasons I didn't have that issue in my org so I didn't need to account for this :D 

Also this screen flow is NOT OPTIMISED FOR BULK UPDATES. If you have a lot of contacts that need to be converted to person accounts, you would be best to achieve that either using dataloader or some other means. If you are regularly finding that you need to convert contacts to person accounts en masse, consider reviewing your business workflow to prevent it! 

If you got this far, thank you for reading! This is my first time writing up something to contribute to the world hivemind and I hope it is ok.

![10 Convert Person Account](https://user-images.githubusercontent.com/119096189/204080731-72a7a19e-d809-415b-a1c7-2a273658e7ab.png)
