# Doks API Client

Doks third party API client. Doks website: https://doks.fi

:warning: This tool is in early stages and is subject to change.

## Installation

Install from npm (not available yet):

```
npm install doks-api-client
```

## Setup

### Import

```javascript
import { DoksApiClient } from 'doks-api-client';
```

### Setup client with options

Please consult Doks to get your `email` and `apikey` for API usage.

Since v1.4.0 allows using [cacheable-lookup](https://github.com/szmarczak/cacheable-lookup) with either internal or external dns cache. Make sure you are using `cacheable-lookup@5` which is compatible with `got@11` that is used.

```javascript
const dnsCache = new CacheableLookup();

const doks = new DoksApiClient({
  apikey: 'doks-api-key-here',
  email: 'api-user-email-here',

  // optional
  apiVersion: 'v1.89', // lock api version
  apiBaseUrl: 'https://url', // change base url, defaults to https://data.doks.fi/api

  // Optional: keep alive agent
  keepAliveAgent: true,

  // Optional: DNS cache
  dnsCache: true
});
```

## Properties for resources

You can find properties for each resource from [src/interfaces.ts](src/interfaces.ts)

## Methods (examples)

```javascript
const exampleCustomerId = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx';
const exampleIdentificationId = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx';
const exampleRequestId = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx';

// Get all customers
const customers = await doks.getCustomers();

// Get single customer
const customer = await doks.getCustomerById(exampleCustomerId);

// Create customer (see src/interfaces.ts for available properties)
const newCustomer = await doks.createCustomer({
  businessid: '1234567-8',
  name: 'Example Oy',
  country: 'fi',
  type: 'business'
});

// Filter customers and return only needed fields
const customers = await doks.getCustomersByFilters(
  // Filtered fields
  { businessid: '1234567-8' },

  // Returned fields
  ['name', 'businessid']
);

// Get customer identifications
const identifications = await doks.getIdentificationsByCustomerId(exampleCustomerId);

// Create identification
const newIdentification = await doks.createIdentificationByCustomerId(exampleCustomerId, {
  name: 'Person Name',
  email: 'person.name@email.ltd'

  // other specs
});

// Send identification
const sentIdentification = await doks.sendIdentificationByCustomerAndIdentificationId(exampleCustomerId, newIdentification.id);

// Get single identification
const identification = await doks.getIdentificationByCustomerAndIdentificationId(exampleCustomerId, newIdentification.id);

// Get customer information requests
const informationRequests = await doks.getInformationRequestsByCustomerId(exampleCustomerId);

// Create information request
const newInformationRequest = await doks.createInformationRequestByCustomerId(exampleCustomerId, {
  email: 'person.name@email.ltd',
  ask_riskquestions: true

  // other specs
});

// Send information request
const sentInformationRequest = await doks.sendInformationRequestByCustomerAndIdentificationId(exampleCustomerId, newInformationRequest.id);

// Get single information request
const informationRequest = await doks.getInformationRequestByCustomerAndIdentificationId(exampleCustomerId, newInformationRequest.id);

// Create owner, responsible person, pep, actual beneficiary
const owner = await doks.createOwnerByCustomerId('customer_id', { ...props });
const responsiblePerson = await doks.createResponsiblePersonByCustomerId('customer_id', { ...props });
const pep = await doks.createPepByCustomerId('customer_id', { ...props });
const actualBeneficiary = await doks.createActualBeneficiaryBycustomerId('customer_id', { ...props });

// Get owners, responsible persons, peps, actual beneficiaries
const owners = await doks.getOwnersByCustomerId('customer_id');
const responsiblePerson = await doks.getResponsiblePersonsByCustomerId('customer_id');
const pep = await doks.getPepsByCustomerId('customer_id');
const actualBeneficiary = await doks.getActualBeneficiariesByCustomerId('customer_id');

// Get API user details
const apiUser = await doks.getAPIUser();
// Get user details by ID
const user = await doks.getUserById('user_id');

// Update user details (note! requires whole user resource fetched by getAPIUser or getUserById)
await doks.updateUser(user);
```

## Helpers

Contains some helpers for handling customers details.

```javascript
// Validate business id
const validBusinessId = doks.isValidBusinessId('1234567-8'); // true or false
```
