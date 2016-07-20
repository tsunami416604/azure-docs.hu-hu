<properties
   pageTitle="Az Azure Data Lake Analytics kezelése az Azure SDK for Node.js használatával | Azure"
   description="Ebből a cikkből megtudhatja, hogyan kezelheti a Data Lake Analytics-fiókjait, adatforrásait, feladatait és felhasználóit az Azure SDK for Node.js használatával."
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Az Azure Data Lake Analytics kezelése az Azure SDK for Node.js használatával


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Az Azure SDK for Node.js az Azure Data Lake Analytics-fiókok, feladatok és katalógusok kezeléséhez használható. Ha más eszközök használatával szeretné megtekinteni a kezelést tárgyaló témakört, kattintson a fenti lapválasztóra.

Jelenleg a következőket támogatja:

  *  **Node.js-verzió: 0.10.0-s vagy újabb**
  *  **Fiókhoz tartozó REST API-verzió: 2015. 10. 01. előzetes verzió**
  *  **Katalógushoz tartozó REST API-verzió: 2015. 10. 01. előzetes verzió**
  *  **Feladathoz tartozó REST API-verzió: 2016. 03. 20. előzetes verzió**

## Szolgáltatások

- Fiókkezelés: létrehozás, lekérés, felsorolás, frissítés és törlés.
- Feladatkezelés: küldés, lekérés, felsorolás és visszavonás.
- Katalóguskezelés: lekérés, felsorolás, létrehozás (titkos adatok), frissítés (titkos adatok) és törlés (titkos adatok).

## A telepítés módja

```bash
npm install azure-arm-datalake-analytics
```

## Hitelesítés az Azure Active Directory használatával

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Data Lake Analytics-ügyfél létrehozása

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Data Lake Analytics-fiók létrehozása

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## A feladatok listájának lekérése

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## A Data Lake Analytics-katalógus adatbázislistáinak lekérése
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Lásd még:

- [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK for Node.js – Data Lake Store-kezelés](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)


<!--HONumber=Jun16_HO2-->


