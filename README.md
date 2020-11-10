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

## 4 Add the 'criteriamatcher' script to your Creator Form as an 'onSubmit' action (see criteriamatcher in files)

Here we'll get into the code itself.


