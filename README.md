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
