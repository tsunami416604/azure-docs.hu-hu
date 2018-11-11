---
title: 'Az Azure Backup: Azure virtuális gépek biztonsági mentése – REST API használatával'
description: Azure VM Backup – REST API használatával a biztonsági mentési műveletek kezelése
services: backup
author: pvrk
manager: shivamg
keywords: REST API-JA; Az Azure virtuális gép biztonsági mentése; Az Azure virtuális gép visszaállítási;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 8a47d3cf346d7961e9f8b1c4fa615a2faa6b1da0
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289784"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Egy Azure virtuális gép biztonsági mentése a Azure Backup – REST API-n keresztül

Ez a cikk ismerteti a biztonsági másolatok kezelése a REST API-n keresztül az Azure Backup használatával, egy Azure virtuális gép. Először egy korábban védelem nélküli Azure virtuális gép védelme nem konfigurálható, aktiválhat egy igény szerinti biztonsági mentés egy védett Azure virtuális gép és a egy biztonsági másolat REST API-n keresztül a virtuális gép biztonsági mentési tulajdonságainak módosítása a itt leírtak.

Tekintse meg [tároló létrehozása](backup-azure-arm-userestapi-createorupdatevault.md) és [szabályzat létrehozása](backup-azure-arm-userestapi-createorupdatepolicy.md) új tárolókat és szabályzatok létrehozására szolgáló REST API-oktatóanyagok.

Tegyük fel, védendő virtuális gép "testVM" a "testVault", "testVaultRG," erőforráscsoportban található Recovery Services-tárolót egy erőforrás csoport "testRG" ("DefaultPolicy" nevű) alapértelmezett szabályzat.

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Biztonsági mentés konfigurálása egy védelem nélküli Azure virtuális gép, a REST API használatával

### <a name="discover-unprotected-azure-vms"></a>A védelem nélküli Azure virtuális gépek felderítése

A tároló először azonosításához az Azure virtuális gép képesnek kell lennie. Ez egy automatikus használatával a [frissítési művelet](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Egy aszinkron *POST* művelet, amely biztosítja, hogy a tároló összes nem védett virtuális gép legfrissebb listáját olvassa be az aktuális előfizetésben és a(z) "gyorsítótárazza" őket. Miután a virtuális gép "gyorsítótárazott", a Recovery services fogja tudni eléri a virtuális Gépet, és védje.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

A POST URI rendelkezik `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` paramétereket. A `{fabricName}` "Azure". A példa kedvéért megfelelően `{vaultName}` "testVault" van, és `{vaultresourceGroupName}` "testVaultRG" van. A szükséges paraméterek nincsenek megadva az URI-ban, mert nem ajánlatos egy külön kérés törzsének.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Válaszok

A "frissítés" művelet egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd a 200 (OK), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|204 Nincs tartalom     |         |  Nincs visszaadott tartalommal OK      |
|202-es elfogadva     |         |     Elfogadva    |

##### <a name="example-responses"></a>Példa válaszok

Miután a *POST* kérelem elküldését a 202-es (elfogadva) válasszal adja vissza.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Nyomon követheti az eredményül kapott művelet a "Location" fejléc használatával egy egyszerű *első* parancs

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Az Azure virtuális gépeket a felderítésüket követően a GET parancs egy 204 (nincs tartalom) választ ad vissza. A tároló már az előfizetésen belüli összes virtuális gép felderítése.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>A megfelelő Azure virtuális gép kiválasztása

 Ellenőrizheti, hogy "gyorsítótárazás" végzi el [védhető elemek listázása](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) az előfizetéshez tartozó, és keresse meg a kívánt virtuális Gépet a válaszban. [Ennek a műveletnek a válasz](#example-responses-1) hogyan a Recovery services azonosítja a virtuális gép az információkat is biztosít.  Ha ismeri a mintát, ezt a lépést kihagyhatja, és lépjen közvetlenül [védelem](#enabling-protection-for-the-azure-vm).

Ez a művelet egy *első* műveletet.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

A *első* URI rendelkezik a szükséges paramétereket. Nincsenek további kérelem törzsében van szükség.

#### <a name="responses"></a>Válaszok

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses"></a>Példa válaszok

Miután a *első* kérelem elküldését a 200 (OK) válasz.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Az értékek száma egy *első* válasz "lap" 200 korlátozódik. Használja a "nextLink" mezőt a válaszok tovább van állítva az URL-Címének lekéréséhez.

A válasz tartalmazza a minden nem védett Azure virtuális gépek és az egyes `{value}` backup konfigurálása az Azure Recovery Service által szükséges összes információt tartalmazza. Biztonsági mentés konfigurálásához jegyezze fel a `{name}` mezőt és a `{virtualMachineId}` mező `{properties}` szakaszban. Hozhatnak létre két változót a mező értéke a említettek szerint.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "virtuális gép;" + `{name}`
- `{virtualMachineId}` később, a használt [a kérelem törzse](#example-request-body)

A példában a fenti értékek fordítás:

- containerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM."
- protectedItemName = "virtuális gép; iaasvmcontainerv2; testRG; testVM."

### <a name="enabling-protection-for-the-azure-vm"></a>Az Azure virtuális gép védelmének engedélyezése

Után a megfelelő virtuális gép "gyorsítótárazott" és "azonosított", válassza ki a szabályzat védelme érdekében. Többet is megtudni a meglévő szabályzatok a tárolóban, tekintse meg [házirend API listában](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Válassza ki a [vonatkozó házirend](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) lépésként tekintse át a szabályzat neve. Szabályzatok létrehozása, tekintse meg [hozzon létre házirendet oktatóanyag](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" van kiválasztva, az az alábbi példában.

Védelem engedélyezése az aszinkron *PUT* védett elem létrehozó műveletet.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

A `{containerName}` és `{protectedItemName}` áll a fenti kialakítani. A `{fabricName}` "Azure". A példánkban ez a rendszer lefordítja arra:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

Védett elem létrehozásához, az alábbiakban a kérelem törzsében összetevői.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem erőforrás-tulajdonságok         |

A kérelem törzsében és egyéb részletek definíciója teljes listájáért tekintse meg [létrehozása REST API-t a dokumentum védett elem](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérés törzse egy védett elem létrehozásához szükséges tulajdonságokat határozza meg.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

A `{sourceResourceId}` van a `{virtualMachineId}` a fent említett a [lista védhető elemek válasz](#example-responses-1).

#### <a name="responses"></a>Válaszok

A védett elem létrehozása egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd a 200 (OK), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202-es elfogadva     |         |     Elfogadva    |

##### <a name="example-responses"></a>Példa válaszok

Miután a *PUT* -kérést a védett elem létrehozásakor vagy frissítésekor, az első válaszig a 202 (elfogadva) egy helyre vagy az Azure-aszinkron-fejléc.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet a location fejlécet, vagy az Azure-AsyncOperation fejlécet használja egy egyszerű, majd nyomon követheti *első* parancsot.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

A művelet befejezése után a védett elem tartalommal, a válasz törzsében ad vissza 200 (OK).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Ez megerősíti, hogy a virtuális gép védelmének engedélyezése és az első biztonsági mentés akkor aktiválódik, a házirend-ütemezés alapján.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>A biztonsági mentés egy védett Azure virtuális gép elindítása egy igény szerinti

Ha egy Azure virtuális gép már konfigurálva van a biztonsági mentéshez, biztonsági mentések fordulhat elő, a házirend-ütemezés alapján. Várjon, amíg az első ütemezett biztonsági mentést, vagy egy igény szerinti biztonsági mentést bármikor aktiválhat. Igény szerinti biztonsági mentések megőrzési elkülönül a biztonsági mentési szabályzat megőrzési, és a egy adott dátum és idő adható meg. Ha nincs megadva, feltételezzük fel a igény szerinti biztonsági mentés az eseményindító 30 nap.

Van egy igény szerinti biztonsági mentésének elindítása egy *POST* műveletet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

A `{containerName}` és `{protectedItemName}` áll össze [fent](#responses-1). A `{fabricName}` "Azure". A példánkban ez a rendszer lefordítja arra:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

Egy igény szerinti biztonsági mentés elindítása, az alábbiakban a kérelem törzsében összetevői.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource tulajdonságai         |

A kérelem törzsében és egyéb részletek definíciója teljes listájáért tekintse meg [aktiválja a biztonsági mentés a védett elemek REST API-dokumentumban](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérés törzse egy védett elem biztonsági másolatának aktiválásához szükséges tulajdonságokat határozza meg. Ha a megőrzési nincs megadva, idő a trigger a biztonsági mentési feladat 30 napig megőrzi.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Válaszok

Van egy igény szerinti biztonsági mentésének elindítása egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd a 200 (OK), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|202-es elfogadva     |         |     Elfogadva    |

#### <a name="example-responses"></a>Példa válaszok

Miután a *POST* kérést egy igény szerinti biztonsági mentés, az első válaszig a 202 (elfogadva) egy helyre vagy az Azure-aszinkron-fejléc.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet a location fejlécet, vagy az Azure-AsyncOperation fejlécet használja egy egyszerű, majd nyomon követheti *első* parancsot.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

A művelet befejezése után az eredményül kapott biztonsági mentési feladat, a válasz törzsében azonosítójú ad vissza 200 (OK).

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, kell követni, amint azt a [REST API-dokumentumban használatával a feladatok monitorozásához](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Az Azure a védett virtuális gépek biztonsági mentési konfigurációjának módosítása

### <a name="changing-the-policy-of-protection"></a>A védelmi házirend módosítása

Ha módosítani szeretné a szabályzatot, amellyel a virtuális gép védett, használhatja ugyanazt a formátumot, [védelem](#enabling-protection-for-the-azure-vm). Csak adja meg az új házirend-azonosító [a kérelem törzsében](#example-request-body) , és küldje el a kérelmet. A példa: 'ProdPolicy', "DefaultPolicy" a testVM, a szabályzat módosításához adja meg a "ProdPolicy" azonosítóját, a kérelem törzsében.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A válasz ugyanazt a formátumot követi említett [védelem](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Állítsa le a védelmet, de a meglévő adatok megőrzése

Távolítsa el a védett virtuális gép védelmét, de megőrizni az adatokat, már rendelkezik biztonsági, távolítsa el a szabályzatot a kérelem törzsében szereplő, és küldje el a kérelmet. Házirend társítását a rendszer eltávolítja, ha már nem aktivált biztonsági mentés, és nincs új helyreállítási pontokat.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A válasz ugyanazt a formátumot követi említett [egy igény szerinti biztonsági mentés indítására](#example-responses-3). Az eredő feladat követni kell leírtak a [REST API-dokumentumban használatával a feladatok monitorozásához](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Védelem leállítása és adatok törlése

Távolítsa el a védett virtuális gép védelmét, és a biztonsági mentési adatok törlése, hajtsa végre egy törlési művelet leírtaknak megfelelően [Itt](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Védelem leállítása és az adatok törlése egy *törlése* műveletet.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

A `{containerName}` és `{protectedItemName}` áll össze [fent](#responses-1). `{fabricName}` az "Azure". A példánkban ez a rendszer lefordítja arra:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses"></a>Válaszok

*Törlés* védelem egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd a 204 (NoContent), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202-es elfogadva     |         |     Elfogadva    |

## <a name="next-steps"></a>További lépések

[Adatok visszaállítása egy Azure-beli virtuális gépek biztonsági mentésének](backup-azure-arm-userestapi-restoreazurevms.md).

Az Azure Backup – REST API-k további információkért tekintse meg a következő dokumentumokat:

- [Az Azure Recovery Services-szolgáltató REST API-val](/rest/api/recoveryservices/)
- [Azure REST API használatának első lépései](/rest/api/azure/)
