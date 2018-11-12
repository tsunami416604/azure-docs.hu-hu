---
title: 'Az Azure Backup: REST API-val biztonsági mentési feladatok kezelése'
description: biztonsági mentés kezelhetik és visszaállíthatják a feladatokat az Azure Backup – REST API használatával
services: backup
author: pvrk
manager: shivamg
keywords: REST API-JA; Az Azure virtuális gép biztonsági mentése; Az Azure virtuális gép visszaállítási;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: eb8b7dc77d180eb56c2585e93e60a36742f6c84c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289679"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Nyomon követheti a biztonsági mentési és helyreállítási feladatok REST API használatával

Az Azure Backup szolgáltatás aktiválása, feladatok, amelyek különböző alkalmazási helyzetekben, például a biztonsági mentésének elindítása a háttérben működtethető, visszaállítási műveletek, biztonsági mentés letiltása. Ezek a feladatok azonosítók alapján követhető nyomon.

## <a name="fetch-job-information-from-operations"></a>Kéri le a feladat adatait a műveletek

Egy művelet, például a biztonsági mentésének elindítása egy jobID mindig ad vissza. A példa: utolsó válasza egy [aktiválja a biztonsági mentési REST API-művelet](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) a következő:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Az Azure virtuális gép biztonsági mentési feladat "jobId" mező azonosíthatók és említettek szerint nyomon követhetők [Itt](https://docs.microsoft.com/rest/api/backup/jobdetails/) használatával egy egyszerű *első* kérelmet.

## <a name="tracking-the-job"></a>A feladat követése

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

A `{jobName}` "jobId" fent említett. A válasz mindig 200 OK gombra a "status" mezőt a feladat aktuális állapotát jelzi. Ha az "Befejezve" vagy "CompletedWithWarnings", "extendedInfo" szakasz a feladat részleteinek tárja fel.

### <a name="response"></a>Válasz

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Példaválasz

Miután a *első* URI azonosító elküldve, 200 (OK) választ ad vissza.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```