---
title: Az Azure File share biztonsági mentésének kezelése a Rest API-val
description: Ismerje meg, hogyan használhatja a REST API-t az Azure Backup által biztonsági mentésben álló Azure-fájlmegosztások kezelésére és figyelésére.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444731"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Az Azure File share biztonsági mentésének kezelése rest API-val

Ebből a cikkből megtudhatja, hogyan végezhet feladatokat az Azure Backup által biztonsági mentésben szereplő Azure-fájlmegosztások kezeléséhez és [figyeléséhez.](https://docs.microsoft.com/azure/backup/backup-overview)

## <a name="monitor-jobs"></a>Feladatok figyelése

Az Azure Backup szolgáltatás elindítja a háttérben futó feladatokat. Ez olyan forgatókönyveket foglal magában, mint például a biztonsági mentés, a visszaállítási műveletek és a biztonsági mentés letiltása. Ezek a feladatok nyomon követhetők az azonosítójukkal.

### <a name="fetch-job-information-from-operations"></a>Feladatadatok beolvasása a műveletekből

Egy művelet, például a biztonsági mentés aktiválása mindig egy jobID-t ad vissza a válaszban.

Például egy [trigger backup REST API-művelet](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) végső válasza a következő:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Az Azure fájlmegosztásbiztonsági mentési feladat azonosítja a **jobId** mező, és nyomon [követhető,](https://docs.microsoft.com/rest/api/backup/jobdetails/) ahogy itt említettget egy GET kérés használatával.

### <a name="tracking-the-job"></a>A feladat nyomon követése

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

A(z) {jobName} a fent említett "jobId". A válasz mindig "200 OK" az **állapotmező** jelzi a feladat állapotát. Miután "Befejeződött" vagy "CompletedWithWarnings", a **extendedInfo** szakasz további részleteket tár fel a feladatról.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Válasz

Név  | Típus  |  Leírás
--- | --- | ----
200 OK |  Feladaterőforrás  | OK

#### <a name="response-example"></a>Példa válaszra

A *GET* URI beküldése után egy 200 válasz ad vissza.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Házirend módosítása

A fájlmegosztás védelmét szolgáló házirend módosításához ugyanazt a formátumot használhatja, mint a védelem engedélyezése. Csak adja meg az új házirend-azonosítót a kérelemházirendben, és küldje el a kérelmet.

Például: A *testshare* védelmi házirendjének *módosítása ütemezés1-ről* *schedule2-re,* adja meg a *schedule2* azonosítót a kérelem törzsében.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>A védelem leállítása, de a meglévő adatok megőrzése

Eltávolíthatja a védett fájlmegosztások védelmét, de megőrizheti a már biztonsági másolatot. Ehhez távolítsa el a házirendet a[biztonsági mentés engedélyezéséhez](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) használt kérelemtörzsből, és küldje el a kérelmet. A házirenddel való társítás eltávolítása után a biztonsági mentések már nem aktiválódnak, és nem jönnek létre új helyreállítási pontok.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Mintaválasz

A fájlmegosztás védelmének leállítása aszinkron művelet. A művelet létrehoz egy másik műveletet, amelyet nyomon kell követni. Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 200-at, amikor a művelet befejeződik.

Válaszfejléc a művelet sikeres elfogadásakor:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy GET paranccsal:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Választörzs

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>A védelem leállítása és az adatok törlése

A védett fájlmegosztás védelmének megszüntetéséhez és a biztonsági mentési adatok törléséhez hajtson végre az [itt](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)részletezett törlési műveletet.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A(z) {containerName} és a ({protectedItemName} paraméterek [itt](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)vannak beállítva.

A következő példa egy olyan műveletet indít el, amely leállítja az *azurefilesvault-kal*védett *testshare* fájlmegosztás védelmét.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Válaszok

A törléselleni védelem aszinkron művelet. A művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.
Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 204 (NoContent) a művelet befejezésekor.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogy [miként háríthatja el a problémákat az Azure-fájlmegosztások biztonsági másolatának konfigurálása során.](troubleshoot-azure-files.md)