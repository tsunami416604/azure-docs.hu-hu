---
title: 'Azure Backup: Azure-beli virtuális gépek visszaállítása REST API használatával'
description: Ebből a cikkből megtudhatja, hogyan kezelheti az Azure-beli virtuális gépek biztonsági mentésének visszaállítási műveleteit REST API használatával.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure-beli virtuális gép biztonsági mentése; Azure-beli virtuális gép visszaállítása;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dacurwin
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 364b6ab589cc0fa50953a71d55adeccc79462eed
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747533"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Azure-beli virtuális gépek visszaállítása REST API használatával

Ha egy Azure-beli virtuális gép biztonsági mentését Azure Backup használatával végezte el, a teljes Azure-beli virtuális gépeket vagy lemezeket vagy fájlokat az azonos biztonsági másolatból állíthatja vissza. Ez a cikk az Azure-beli virtuális gépek vagy lemezek REST API használatával történő visszaállítását ismerteti.

Az egyik visszaállítási művelethez először a megfelelő helyreállítási pontot kell azonosítani.

## <a name="select-recovery-point"></a>Helyreállítási pont kiválasztása

A biztonsági másolati elemek rendelkezésre álló helyreállítási pontjai a [helyreállítási pont REST API listájának](https://docs.microsoft.com/rest/api/backup/recoverypoints/list)használatával is felvehetők. Ez egy egyszerű *Get* művelet az összes releváns értékkel.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

A `{containerName}` és az `{protectedItemName}` a következő módon vannak [kiépítve](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` az "Azure".

A *Get* URI az összes szükséges paraméterrel rendelkezik. Nincs szükség további kérelem törzsére

### <a name="responses"></a>Válaszok

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Példaválasz

Miután elküldte a *Get* URI-t, a rendszer egy 200 (ok) választ ad vissza.

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

A rendszer a fenti válasz `{name}` mezőjével azonosítja a helyreállítási pontot.

## <a name="restore-disks"></a>Lemezek visszaállítása

Ha testre kell szabnia egy virtuális gép létrehozását a biztonsági mentési adatokból, a lemezeket egyszerűen visszaállíthatja egy kiválasztott Storage-fiókba, és létrehozhat egy virtuális gépet a saját igényei szerint. A Storage-fióknak ugyanabban a régióban kell lennie, mint a Recovery Services-tárolónak, és nem szabad zónába esnie. A lemezeket és a biztonsági másolattal ellátni kívánt virtuális gép konfigurációját ("VMConfig. JSON") a rendszer a megadott Storage-fiókban tárolja.

A visszaállítási lemezek elindítása *post* kérelem. Ha többet szeretne tudni a lemezek visszaállítása műveletről, tekintse meg a ["trigger Restore" REST API](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

A `{containerName}` és az `{protectedItemName}` a következő módon vannak [kiépítve](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` az "Azure", a `{recoveryPointId}` pedig a [fent](#example-response)említett helyreállítási pont `{name}` mezője.

### <a name="create-request-body"></a>Kérelem törzsének létrehozása

Egy lemez Azure-beli virtuális gép biztonsági mentésből való visszaállításának elindításához kövesse a kérelem törzsének összetevőit.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

A kérelem törzsének és egyéb részleteinek teljes listájáért lásd: [trigger Restore REST API Document](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Példakérelem

A következő kérelem törzse a lemez-visszaállítás indításához szükséges tulajdonságokat határozza meg.

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

A visszaállítási lemez indítása [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 200 (OK), amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|202 elfogadva     |         |     Elfogadott    |

#### <a name="example-responses"></a>Válaszok – példa

Miután elküldte a *post* URI-t a visszaállítási lemezek indításához, a kezdeti válasz 202 (elfogadva), egy Location fejlécet vagy egy Azure-aszinkron-fejlécet tartalmaz.

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

Ezután nyomon követheti az eredményül kapott műveletet a Location fejléc vagy az Azure-AsyncOperation fejléc használatával egy egyszerű *Get* paranccsal.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

A művelet befejezése után a 200 (OK) értéket adja vissza a válasz törzsében létrejövő visszaállítási feladatokhoz tartozó AZONOSÍTÓval.

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

Mivel a biztonsági mentési feladat hosszú ideig futó művelet, azt a [feladatok figyelése REST API dokumentum használatával](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint kell követni.

A hosszú ideig tartó feladatok befejezése után a rendszer a biztonsági másolatban szereplő virtuális gép ("VMConfig. JSON") lemezeit és konfigurációját a megadott Storage-fiókban fogja megjelenni.

## <a name="restore-as-another-virtual-machine"></a>Visszaállítás másik virtuális gépként

[Válassza ki a helyreállítási pontot](#select-recovery-point) , és hozza létre a kérelem törzsét az alább megadott módon egy másik Azure-beli virtuális gép létrehozásához a helyreállítási pontból származó adatokkal.

A következő kérelem törzse határozza meg a virtuális gépek visszaállításának elindításához szükséges tulajdonságokat.

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

A választ ugyanúgy kell kezelni, mint a [lemezek visszaállítására szolgáló fentiekben ismertetett](#response)módon.

## <a name="next-steps"></a>További lépések

A Azure Backup REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
