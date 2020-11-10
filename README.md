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


