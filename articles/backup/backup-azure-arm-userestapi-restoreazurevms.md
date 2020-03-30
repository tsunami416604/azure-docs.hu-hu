---
title: Az Azure virtuális gépei visszaállítása a REST API használatával
description: Ebben a cikkben megtudhatja, hogyan kezelheti az Azure virtual machine backup rest-alapú biztonsági mentési műveleteit a REST API használatával.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4990d815721ddbdde8e6eb6ebf8d6d3b49adc700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173384"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Az Azure virtuális gépek visszaállítása REST API használatával

Miután befejeződött egy Azure virtuális gép biztonsági mentése az Azure Backup használatával, visszaállíthatja a teljes Azure virtuális gépeket vagy lemezeket vagy fájlokat ugyanabból a biztonsági másolatból. Ez a cikk ismerteti, hogyan állíthatja vissza az Azure virtuális gép vagy lemezek REST API használatával.

Minden visszaállítási művelethez először azonosítani kell a megfelelő helyreállítási pontot.

## <a name="select-recovery-point"></a>Helyreállítási pont kiválasztása

A biztonsági másolat elem elérhető helyreállítási pontjai a [REST API listahelyreállítási pont](https://docs.microsoft.com/rest/api/backup/recoverypoints/list)használatával sorolhatók fel. Ez egy egyszerű *GET* művelet az összes vonatkozó értékkel.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` és a épített [itt](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}`"Azure".

A *GET* URI rendelkezik az összes szükséges paraméterrel. Nincs szükség további kéréstörzsre

### <a name="responses"></a>Válaszok

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList lista](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Példaválasz

A *GET* URI beküldése után egy 200 (OK) választ ad vissza.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

A helyreállítási pontot a `{name}` fenti válasz mezője azonosítja.

## <a name="restore-disks"></a>Lemezek visszaállítása

Ha szükség van egy virtuális gép létrehozásának testreszabására a biztonsági mentési adatokból, egyszerűen visszaállíthatja a lemezeket egy kiválasztott tárfiókba, és létrehozhat egy virtuális gépet ezekből a lemezekből, a követelményeiknek megfelelően. A tárfióknak ugyanabban a régióban kell lennie, mint a helyreállítási szolgáltatások tárolójának, és nem lehet zónaredundáns. A lemezek, valamint a konfiguráció a biztonsági másolatot készíteni virtuális gép ("vmconfig.json") az adott tárfiókban lesz tárolva.

A visszaállítási lemezek aktiválása *POST-kérelem.* Ha többet szeretne megtudni a lemezek visszaállítása műveletről, olvassa el az ["trigger restore" REST API-t.](https://docs.microsoft.com/rest/api/backup/restores/trigger)

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` és a épített [itt](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}`az "Azure", `{recoveryPointId}` és `{name}` a [fent](#example-response)említett helyreállítási pont területe .

### <a name="create-request-body"></a>Kérelemtörzs létrehozása

Egy lemez-visszaállítás egy Azure virtuális gép biztonsági mentés, a következő összetevők a kérelem törzse.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket a [REST API-dokumentum visszaállítása című dokumentum ban](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)találja.

#### <a name="example-request"></a>Példakérelem

A következő kérelemtörzs a lemez-visszaállítás elindításához szükséges tulajdonságokat határozza meg.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>Válasz

A visszaállítási lemez aktiválása [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 200 (OK) értéket ad vissza, amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|202 Elfogadva     |         |     Elfogadva    |

#### <a name="example-responses"></a>Példa válaszok

Miután elküldte a POST URI-t a visszaállítási lemezek elindításához, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *POST* URI for triggering restore disks, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy egyszerű *GET* paranccsal.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Miután a művelet befejeződött, 200 (OK) értéket ad vissza az eredményül kapott visszaállítási feladat azonosítójával a választörzsben.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, nyomon kell követni a [rest API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

Miután a hosszú ideig futó feladat befejeződött, a lemezek és a biztonsági másolatot készíteni virtuális gép konfigurációja ("VMConfig.json") jelen lesz az adott tárfiókban.

## <a name="restore-as-another-virtual-machine"></a>Visszaállítás másik virtuális gépként

[Válassza ki a helyreállítási pontot,](#select-recovery-point) és hozza létre a kérelem törzse az alábbiakszerint egy másik Azure virtuális gép a helyreállítási pont ból származó adatokat.

A következő kérelem törzse határozza meg a virtuális gép visszaállításához szükséges tulajdonságokat.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

A választ ugyanúgy kell kezelni, mint [ahogy azt a lemezek visszaállítása kor leírta.](#response)

## <a name="next-steps"></a>További lépések

Az Azure Backup REST API-król az alábbi dokumentumokban talál további információt:

- [Az Azure Recovery Services szolgáltatóREST API-ja](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
