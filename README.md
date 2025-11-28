# isc-workflow-repository

Repository with workflows and forms for specific use cases

# Governance Groups Management

Workflows and forms to delegate to the governance group owner, the management of its groups. With that workflow, the owner can now remove members

# Track my requests

The idea of the workflow is to provide an interactive way, for an end user, to get details about its pending access request. The user can search for its request and get details about the status, the approver(s) and, if necessary, send reminders to the approvers.

# Welcome package for joiners

The workflow is triggered after an identity is created. The workflow will simply ask the manager about the computer and mobile phone to be assigned to the joiner. Then, tickets are automatically opened in ServiceNow

# Duplicated Identities detection and remediation

This workflow triggers an Identity being created. It does the fuzzy search to search for a potential duplicated using approximative search on firstname and lastname. If there is a match, it sends a form to someone (manager ?) to arbitrate.

- Create a new Identity because it is not the same guy
- Merge the existing identity with the new one
- If Merge
  -- It filters the old authoritative account on its source
  -- It removes the old authoritative account on the identity
  -- It processes the identity to recalculate the attributes

## Pre requisites

For the workflow to work properly you need :

- A “pending” lifecycle state in which the new identity will be assigned
- An “init” lifecycle state in which the new identity will be assigned in case it is not the same guy. Init LCS will assign a birthright to create an account in a target source. It can be an AD account, the uniqueID source I presented before. The main idea is to have something that can be used in the calculation of the LCS to then move the LCS automatically to “active”
- An “arbitration” lifecycle state in which the new identity will be assigned in case arbitration is required
- Any other LCS that you need : active, inactive …

## The workflows

You need 2 workflows

### Set Lifecycle State

This workflow uses an external trigger. It is called from the main workflow to change the LCS of an identity. I’ve update that workflow to avoid usage of the loop. Remember you need to generate a new client_id / client_secret and you will have to change the url to the external trigger in the main workflow

### Homonymous workflow

This workflow is used to trigger a new identity created and run the search.

## The form

The form is presented to the manager for instance (in my workflow to me) so he can choose wether he wants to create a new identity or merge with an existing one

# Flexible Approval

The objective of the workflow is to:

- check wether the access request has been launched by someone from a Support Team (based on Governance Group membership aggregated using ISC Governance Connector)
- if yes and if the recipient is within the scope of that team, automatically approve. Otherwise, send the approval to the relevant team
- if no, check wether the request has been launched by a manager
- if yes, auto approve if the recipient is in his team. Otherwise, send the approval to the relevant team.

# Create Contractor With Forms

The object of the workflow is to use custom Forms to create contractor accounts. The worklow implements a detection of duplicated identities

## The Source

The source is used to create an account that will then create an Identity using an Identity Profile

## The Forms

The form implements some **select fields** that use some search features.
There is a form to manage deduplication

## The Workflow

Nothing particular here

# Update Contractor With Forms

After creating a contractor, you might want to edit it. This workflow implements some forms to select the identity then update it.

## The Workflow

The workflow starts by selecting an identity. There are some logic to choose wether to present a picker for managers or for support teams. Depending if the user is an Employee or a Contractor, it is not the same source to update

## The Forms

The first Form is used to select the identity. I've implemented some logic to display the relevant picker
The second Form is used to choose the attributes to update. I use some transforms to calculate the values to be used to update the accounts. To extend the end date of a contractor, instead of using a Date Picker, I present a select field with periods (1 month, 6 months ...). This is actually an array that is pushed to the Form from the Workflow. That way, I can have an **id** that is returned to the workflow and can be used to calculate the new date using a **date add** operator.

# Detect Brand Move and Ask for Decision

This workflow is specific to a customer use case. Here the idea is to detect when someone is moving from a Brand to another. When someone is moving, first of all, the Identity must not be updated with the new value. Instead of that, a notification has to be send to someone (I'm doing it using form but maybe an adaptive approval would be better as it can be assigned to a Group of People). The recipient must decide wether or not the current AD account must be kept, moved and updated OR if a brand new account must be created. In order to put the process an hold until validation, I'm using custom LCS and transforms to make sure the identity attributes are not updated before the decision. I'm also using a NELM Source to temporaly create an Identity for the mover in case of new Account to be created. Then I relink the old AD account to that new Identity. The old Identity not having an AD account anymore will automatically create a new one.
