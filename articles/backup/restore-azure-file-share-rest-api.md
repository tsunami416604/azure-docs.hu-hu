---
title: Az Azure-fájlmegosztások visszaállítása a REST API-val
description: Megtudhatja, hogy a REST API használatával hogyan állíthatja vissza az Azure-fájlmegosztásokat vagy adott fájlokat az Azure Backup által létrehozott visszaállítási pontról
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252505"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Az Azure-fájlmegosztások visszaállítása rest api-val

Ez a cikk bemutatja, hogyan állíthat vissza egy teljes fájlmegosztást vagy adott fájlokat az [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) által a REST API használatával létrehozott visszaállítási pontról.

A cikk végére megtudhatja, hogyan hajthatja végre a következő műveleteket a REST API használatával:

* Tekintse meg a biztonsági másolatot, az Azure-fájlmegosztás visszaállítási pontjait.
* Teljes Azure-fájlmegosztás visszaállítása.
* Az egyes fájlok vagy mappák visszaállítása.

## <a name="prerequisites"></a>Előfeltételek

Feltételezzük, hogy már rendelkezik a visszaállítani kívánt biztonsági másolatot tartalmazó fájlmegosztással. Ha nem, ellenőrizze [a Backup Azure fájlmegosztás rest API használatával,](backup-azure-file-share-rest-api.md) hogyan hozhat létre egyet.

Ebben a cikkben a következő forrásokat használjuk:

* **RecoveryServicesVault**: *azurefilesvault*
* **Erőforráscsoport**: *azurefiles*
* **Tárfiók**: *afsaccount*
* **Fájlmegosztás**: *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>Tárolónév és védettelemnév beolvasása

A legtöbb visszaállítással kapcsolatos API-hívás esetén meg kell adnia a{containerName} és a {protectedItemName} URI-paraméterek értékeit. Használja az ID attribútumot a [GET backupprotectableitems](https://docs.microsoft.com/rest/api/backup/protecteditems/get) művelet választörzsében a paraméterek értékeinek lekéréséhez. A példánkban a védeni kívánt fájlmegosztás azonosítója a következő:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Így az értékek a következőképpen lefordíthatók:

* {containername} - *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName} - *azurefileshare;azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Helyreállítási pontok beolvasása az Azure-fájlmegosztás biztonsági mentéséhez

A biztonsági mentésben részt vett fájlmegosztások vagy fájlok visszaállításához először jelöljön ki egy helyreállítási pontot a visszaállítási művelet végrehajtásához. A biztonsági másolatot tartalmazó elem rendelkezésre álló helyreállítási pontjai a [Recovery Point-List](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) REST API-hívással sorolhatók fel. Ez egy GET művelet az összes vonatkozó értékkel.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Az URI-értékeket a következőképpen állítsa be:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {containername}: *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName}: *azurefileshare;azurefiles*
* {ResourceGroupName}: *azurefiles*

A GET URI rendelkezik az összes szükséges paraméterrel. Nincs szükség további kéréstörzsre.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>Példaválasz

A GET URI beküldése után a rendszer 200-as választ ad vissza:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

A helyreállítási pontot a fenti válasz {name} mezője azonosítja.

## <a name="full-share-recovery-using-rest-api"></a>Teljes megosztás helyreállítása rest api-val

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.
A visszaállítás aktiválása postai kérelem, és ezt a műveletet az [eseményindító REST](https://docs.microsoft.com/rest/api/backup/restores/trigger) API használatával hajthatja végre.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A(z) {containerName} és a ({protectedItemName} értékek [az itt](#fetch-containername-and-protecteditemname) megadottak, a recoveryPointID pedig a fent említett helyreállítási pont {name} mezője.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Kérelemtörzs létrehozása

Egy Azure-fájlmegosztás visszaállításának elindításához a következő összetevők a kérelem törzse:

Név |  Típus   |   Leírás
--- | ---- | ----
Tulajdonságok | AzureFileShareRestoreRequest | RestoreRequestResource tulajdonságai

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket az [eseményindító REST API-visszaállítási dokumentumcímű dokumentumban](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)találja.

### <a name="restore-to-original-location"></a>Visszaállítás az eredeti helyre

#### <a name="request-body-example"></a>Törzs igénylése példa

A következő kérelemtörzs az Azure-fájlmegosztás-visszaállítás indításához szükséges tulajdonságokat határozza meg:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

Adja meg a következő paramétereket az alternatív hely helyreállítása érdekében:

* **targetResourceId**: Az a tárfiók, amelyre a biztonsági másolatot készítő tartalom visszaáll. A céltárfióknak ugyanazon a helyen kell lennie, mint a tárolónak.
* **name**: Az a fájlmegosztás a céltárfiókon belül, amelyre a biztonsági másolatot készítő tartalom visszaáll.
* **targetFolderPath**: Az a mappa a fájlmegosztás alatt, amelyre az adatokat visszaállítják.

#### <a name="request-body-example"></a>Törzs igénylése példa

A következő kérelem törzse visszaállítja az *azurefiles* fájlmegosztást az *afsaccount* tárfiókban az *azurefiles1* fájlmegosztásra az *afaccount1* tárfiókban.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Válasz

A visszaállítási művelet aktiválása [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.
Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 200 (OK) a művelet befejezésekor.

#### <a name="response-example"></a>Példa válaszra

Miután elküldte a POST URI-t a visszaállítás elindításához, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *POST* URI for triggering a restore, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy GET paranccsal.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Miután a művelet befejeződött, 200 (OK) értéket ad vissza az eredményül kapott visszaállítási feladat azonosítójával a választörzsben.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Az alternatív hely helyreállítása esetén a választörzs a következőkre fog épülni:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, nyomon kell követni a [rest API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)

## <a name="item-level-recovery-using-rest-api"></a>Elemszintű helyreállítás rest api használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A(z) {containerName} és a ({protectedItemName} értékek [az itt](#fetch-containername-and-protecteditemname) megadottak, a recoveryPointID pedig a fent említett helyreállítási pont {name} mezője.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Kérelemtörzs létrehozása

Egy Azure-fájlmegosztás visszaállításának elindításához a következő összetevők a kérelem törzse:

Név |  Típus   |   Leírás
--- | ---- | ----
Tulajdonságok | AzureFileShareRestoreRequest | RestoreRequestResource tulajdonságai

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket az [eseményindító REST API-visszaállítási dokumentumcímű dokumentumban](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)találja.

### <a name="restore-to-original-location"></a>Visszaállítás az eredeti helyre

A következő kérelem törzse a *Restoretest.txt* fájl visszaállítása az *azurefiles* fájlmegosztásban az *afsaccount* tárfiókban.

Kérelem törzsének létrehozása

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

A következő kérelem törzse a *Restoretest.txt* fájl visszaállítása az *azurefiles* fájlmegosztásban az *afsaccount* tárfiókban az *azurefiles1* fájlmegosztás *restoredata* mappájába az *afaccount1* tárfiókban.

Kérelemtörzs létrehozása

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

A választ ugyanúgy kell kezelni, ahogy azt a fentiekben kifejtettük a [teljes megosztás-visszaállításesetén](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan kezelheti az [Azure-fájlmegosztások biztonsági mentését a Rest API használatával.](manage-azure-file-share-rest-api.md)
