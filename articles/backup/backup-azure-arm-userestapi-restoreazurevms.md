---
title: 'Azure Backup: A REST API használatával az Azure virtuális gépek visszaállítása'
description: az Azure VM Backup – REST API-val visszaállítási műveletek kezelése
services: backup
author: pvrk
manager: shivamg
keywords: REST API-JA; Az Azure virtuális gép biztonsági mentése; Az Azure virtuális gép visszaállítási;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100199"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>A REST API használatával Azure-beli virtuális gépek visszaállítása

Egy Azure-beli virtuális gép Azure Backup használatával a biztonsági mentés befejezése után egy a teljes Azure-beli virtuális gépek vagy a lemezek vagy a fájlokat az azonos biztonsági másolatból helyreállíthatja. Ez a cikk ismerteti, hogyan lehet visszaállítani egy Azure virtuális gép vagy lemez REST API használatával.

A visszaállítási művelet, adatáthelyezések először azonosítsa a megfelelő helyreállítási pontot.

## <a name="select-recovery-point"></a>Válassza ki a helyreállítási pont

A rendelkezésre álló helyreállítási pontok egy biztonsági mentési elem használatával is megadható a [helyreállítási pont REST API listában](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Egy egyszerű *első* műveletet a megfelelő értékeket.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

A `{containerName}` és `{protectedItemName}` áll össze [Itt](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` az "Azure".

A *első* URI rendelkezik a szükséges paramétereket. Nem kell egy további kérés törzsének

### <a name="responses"></a>Válaszok

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Példaválasz

Miután a *első* URI azonosító elküldve, 200 (OK) választ ad vissza.

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

A helyreállítási pontot, amelynél az a `{name}` a fenti válasz mező.

## <a name="restore-disks"></a>Lemezek visszaállítása

Szabhatja testre a biztonsági mentési adatokat a virtuális gép létrehozása szükség van, ha egy csak lemezek visszaállítása a választott storage-fiókra és egy virtuális gép létrehozása ezeket a lemezeket a követelményeknek. A tárfiók és a recovery services-tárolónak ugyanabban a régióban kell lennie, és nem lehet zónaredundánsak. A lemezek, valamint a biztonsági másolat ("vmconfig.json") virtuális gép konfigurációját az adott tárfiók lesz tárolva.

A lemezek visszaállításának elindítása egy *POST* kérelmet. Többet is megtudni a Lemezvisszaállítási műveletet, olvassa az ["a visszaállítás elindítása" a REST API-val](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

A `{containerName}` és `{protectedItemName}` áll össze [Itt](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` az "Azure" és a `{recoveryPointId}` van a `{name}` mezőjét a helyreállítási pont már említettük, [fent](#example-response).

### <a name="create-request-body"></a>Kérés törzsének létrehozása

Lemez visszaállítása egy Azure virtuális gép biztonsági mentése aktiválhat, az alábbiakban a kérelem törzsében összetevői.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

A kérelem törzsében és egyéb részletek definíciója teljes listájáért tekintse meg [visszaállítása – REST API-dokumentumban aktiválása](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Példakérelem

A következő kérés törzse egy lemezt a visszaállítás elindítása szükséges tulajdonságokat határozza meg.

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

Van egy visszaállítási lemez aktiválása egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 200 (OK), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|202-es elfogadva     |         |     Elfogadva    |

#### <a name="example-responses"></a>Példa válaszok

Miután a *POST* URI indítására, a lemezek visszaállítása, az első válaszig 202 (elfogadva) és a egy helyen vagy az Azure-aszinkron-fejléc.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet a location fejlécet, vagy az Azure-AsyncOperation fejlécet használja egy egyszerű, majd nyomon követheti *első* parancsot.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

A művelet befejezése után az eredményül kapott visszaállítási feladat a válasz törzsében azonosítójú ad vissza 200 (OK).

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

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, hogy követni kell leírtak a [REST API-dokumentumban használatával a feladatok monitorozásához](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

A hosszú ideig futó feladat befejeződése után a lemezek és a konfiguráció a biztonsági másolat virtuális gép ("VMConfig.json") lesz az adott tárfiók található.

## <a name="restore-as-another-virtual-machine"></a>Állítsa vissza egy másik virtuális gépként

[Válassza ki a helyreállítási pont](#select-recovery-point) és a kérelem törzsében megadott az alábbi egy másik Azure-beli virtuális gép létrehozása az adatok a helyreállítási pontot létrehozni.

A következő kérés törzse egy virtuális gépet a visszaállítás elindítása szükséges tulajdonságokat határozza meg.

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
        "api-version": "2016-12-01",
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

A válasz azonos módon kell kezelni [a lemezek visszaállítását a fent ismertetett](#response).

## <a name="next-steps"></a>További lépések

További információ az Azure Backup – REST API-k a következő dokumentumokban talál:

- [Az Azure Recovery Services-szolgáltató REST API-val](/rest/api/recoveryservices/)
- [Azure REST API használatának első lépései](/rest/api/azure/)