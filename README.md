# ComplexAssignmentRulesViaCRMModule

## Potential Scenario for use

Imagine you are trying to process Leads from all over the country or all over the world. You have a Creator Form on your website that people are filling in with several fields like their Country, State, Preferred Language, the type of product/service they're interested in, etc. You need to assign those Leads out to 50+ different salespeople with specific regional, product, or even language-based criteria. 

'Salesperson A' might be in charge of Texas leads for Product A in English. 'Salesperson B' might be in charge of Texas leads for Product A in Spanish. But also for Montana, Arizona, and Louisiana. But someone else is responsible for Product B in those areas. And someone else is responsible for Product C in the whole country! This could go on and on and get extremely complex.

Accounting for all these assignment rules can become extremely tedious. Most companies elect to hard-code in their assignment rules because that's the quickest solution. If your company is small or if you know that none of the rules/criteria will be changing in the future, hard-coding isn't a terrible solution. But if you're trying to scale your business or if things are changing or if you're coming at this already with hundreds of employees, you need a more flexible, scalable assignment system. That's what this code is--a way to create and manage a potentially infinite number of assignment rules through a CRM Module with Subform, then process them all to achieve accurate assignment with a simple Deluge script.

## Advantages of this technique

- Can scale to accomodate hundreds and thousands of assignment rules.
- The concept and script are fairly simple.
- Can establish tiers of assignment (i.e. if it doesn't find someone to match exactly, it finds the categorical match)
- Can easily be jerry-rigged to do something other than owner assignment.

# The Process

## Apps used
- Zoho CRM
- Zoho Creator

## General Steps
1. Create Custom Module for Users/Assignees
2. Make a Subform in that Module with the form's Assignment Criteria as columns
3. Populate the module with Users and Assignment Criteria subform rows
4. On form submission, parse the Users to find a criteria and assign to that User

We'll go into greater detail with some screenshots below.

## 1 and 2 - Create the Module and Subform (see Screenshot A in files)

For the module itself you only need basic fields related to assignment. In this case we're just doing a User lookup called 'Related User'. This will be who we ultimately assign records to that match the criteria. We're calling the module 'Representatives'.

For the subform, add all your choices as Multi-Selects for the best user experience. Here we have some Products (just a picklist, not a Product module lookup), Countries, U.S. States, and Languages. These are our assignment criteria, so we call the subform 'Assignment Criteria'.

NOTE: Add 'ALL' as an option in all Assignment Criteria column picklists. This is how we'll do tiered-assignment.

## 3 Add some records and Assignment Criteria configurations (see Screenshot B in files)

Here we've added 3 Representatives and some Assignment Criteria for each. Notice how Chad is only responsible for Bicycles in certain US States and in Canada. He is responsible for English and Spanish. Jay is responsible for Model Trains and Suntan Lotion across 3 Asian countries and he's responsible for the language of 日本語. Then we have Peter. He is assigned to ALL of everything. This is the secret of tiered assignment and will make more sense once you look at the code.

The simple explanation is this: The script will check the form submission for an exact criteria match and if it finds one it will assign to that Representative. If it doesn't find an exact match, it will choose a categorical match from ALL. We'll run through a few specific examples when we talk about the code below.

## 4 Add the 'criteriamatcher' script to your Creator Form as an 'onSubmit' action (see Screenshot C in files)

Here we'll get into the code itself.

First we get the Representative records from CRM and set a control variable. If 'truerepid' is still "Doesn't Exist" by the end, we will know that there was no exact match found.

```javascript
reps = zoho.crm.getRecords("Representatives");
truerepid = "Doesn't Exist";
```
Next we iterate through all the Representatives and get the Assignment Criteria Subform rows. We then iterate through the rows.
```javascript
for each  r in reps
	{
	rep = zoho.crm.getRecordById("Representatives",r.get("id").toLong());
	subs = rep.get("Assignment_Criteria");
	for each  s in subs
		{
```
This is where we get the meat of the script--this If condition will check their answer on the form against all 4 Assignment Criteria columns. If the Form answer matches in all 4 cases then we change our control variable to the Relater User ID, as well as the general RepID variable. If the Assignment Criteria column is empty (null) like in the case of Jay's US States, and is also empty on the form, then you will still get a match because of the ifNull condition on each option.
```javascript
		if(ifNull(s.get("Products"),"1").contains(ifNull(input.Products,"1")) && ifNull(s.get("Country"),"2").contains(ifNull(input.Country,"2")) && ifNull(s.get("State"),"3").contains(ifNull(input.State,"3")) && ifNull(s.get("Language"),"4").contains(ifNull(input.Language,"4")))
			{
			truerepid = r.get("Related_User").get("id");
			}
```
In case there isn't an exact match, we have a second condition that checks for any matches including "ALL". This is how we do 'tiered' acceptance. If there isn't an exact match, we can assign to whoever has "ALL" for the relevant categories.
```javascript
		else if(ifNull(s.get("Products"),"1").contains(ifNull(input.Products,"1")) || s.get("Products") = "ALL" && ifNull(s.get("Country"),"2").contains(ifNull(input.Country,"2")) || s.get("Country") = "ALL" && ifNull(s.get("State"),"3").contains(ifNull(input.State,"3")) || s.get("State") = "ALL" && ifNull(s.get("Language"),"4").contains(ifNull(input.Language,"4")) || s.get("Language") = "ALL")
			{
			repid = r.get("Related_User).get("id");
			}
		}
	}
```
We are now out of our loops and can determine which variable to use. If the truerepid was filled out we know there was an exact match and we want to use it. Otherwise we just want to use the general variable which would've come from an 'ALL' match.
```javascript	
	if(truerepid != "Doesn't Exist")
		{
		repid = truerepid;
		}
```
Now we have our repid and can assign to that Representative when we create the record. 

Lastly we'll add a check to alert us in case there is no match at all.
```javascript
if(repid = null)
	{
	repid = 3501808000000176021;
	emyowner = "yes";
	sendmail
	[
		from :"adminemail"
		to :"mainrepemail"
		subject :"Representative Assignment Failure"
		message :"The Representative Assignment script just failed at this precise time: " + zoho.currenttime + " for the following combo of 			  Product, Country, State, and Language. " + input.Products + ", " + input.Country + ", " + input.State + ", " + input.Language + "."
		]
	}
```
And that's it! Let's run through a few examples to help understand the logic of the assignment.

## Assignment Examples

Form Submission 1 -- 
Product: Suntan Lotion,
Country: North Korea,
US State: null,
Language: 日本語

In this case, the script would get the repid of Jay Lim.

Form Submission 2 -- 
Product: Bicycle,
Country: United States,
State: Nevada,
Language: English,

In this case, we'd get the repid of Peter Fuller.

You may have thought it would go to Chad Clayton, but it wouldn't because of the State. The script would match with Chad on Product, Country, and Language, but not State. When it went to Peter it wouldn't find an exact match but it'd find an "ALL" match in all four categories, and thus it would return Peter's id.

Form Submission 3 -- 
Product: Model Train,
Country: Japan,
US State: null,
Language: English

This case would also go to Peter because of the Language. Jay is only assigned to 日本語.

Peter here is a catch-all. He will get ANY records that don't have an exact match. He could've just had "Model Train" in Products and "ALL" in everything else and it also would've been a match for example 3 here.
