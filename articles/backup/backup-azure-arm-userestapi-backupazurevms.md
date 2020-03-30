---
title: Az Azure-beli virtuális gépek biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági, REST API-jának
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja, kezdeményezi és kezelheti az Azure VM biztonsági mentési biztonsági mentési műveletek rest API használatával biztonsági mentési műveleteket.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248163"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Azure-beli virtuális gép biztonsági mentése az Azure Backup használatával rest API-n keresztül

Ez a cikk ismerteti, hogyan kezelheti az Azure virtuális gépek biztonsági mentéseit az Azure Backup használatával rest API-n keresztül. Konfigurálja a korábban nem védett Azure virtuális gép védelmét, indítsa el a védett Azure virtuális gép igény szerinti biztonsági mentését, és módosítsa a biztonsági mentést a REST API-n keresztüli biztonsági mentési tulajdonságait, ahogy azt itt ismertetük.

Tekintse meg a [tároló létrehozása,](backup-azure-arm-userestapi-createorupdatevault.md) és [hozzon létre házirend](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API oktatóanyagok új tárolók és szabályzatok létrehozásához.

Tegyük fel, hogy egy virtuális gép "testVM" nevű erőforráscsoport "testRG" alatt a Recovery Services-tretretái "testVault" tárolóba szeretné védeni, amely a "testVaultRG" erőforráscsoporton belül van, az alapértelmezett házirenddel ("DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Nem védett Azure-virtuális gép biztonsági mentésének konfigurálása REST API használatával

### <a name="discover-unprotected-azure-vms"></a>Fedezheti a nem védett Azure-virtuális gépeket

Először is a tároló képesnek kell lennie az Azure virtuális gép azonosítására. Ez a [frissítési művelettel](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)aktiválódik. Ez egy aszinkron *POST-művelet,* amely biztosítja, hogy a tároló megkapja az aktuális előfizetésben lévő összes nem védett virtuális gép legújabb listáját, és "gyorsítótárakat" kap. Miután a virtuális gép "gyorsítótárazott", a helyreállítási szolgáltatások hozzáférhetnek a virtuális géphez, és megvédhetik azt.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

A POST `{subscriptionId}`URI-nak , `{vaultName}`, `{vaultresourceGroupName}`paraméterei `{fabricName}` vannak. Az `{fabricName}` "Azure". Mint egy példa, `{vaultName}` a "testVault", és `{vaultresourceGroupName}` a "testVaultRG". Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelemtörzsre.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Válaszok

A "frissítési" művelet [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 200 (OK) értéket ad vissza, amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|204 Nincs tartalom     |         |  OK tartalom visszaadása nélkül      |
|202 Elfogadva     |         |     Elfogadva    |

##### <a name="example-responses"></a>Példa válaszok

A *POST-kérelem* benyújtását követően a rendszer 202(Elfogadva) választ ad vissza.

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet nyomon követése a "Hely" fejléc segítségével egy egyszerű *GET* paranccsal

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Miután az összes Azure-beli virtuális gépek felderítése, a GET parancs egy 204 (nincs tartalom) választ ad vissza. A tároló most már képes felderíteni az előfizetésen belüli virtuális gépeket.

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

### <a name="selecting-the-relevant-azure-vm"></a>A megfelelő Azure Virtuális gép kiválasztása

 Megerősítheti, hogy a "gyorsítótárazás" az előfizetés alatt [lévő összes védhető elem listázásával](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) történik, és keresse meg a kívánt virtuális gép a válaszban. [A művelet válasza](#example-responses-1) arról is tájékoztatást nyújt, hogy a Helyreállítási szolgáltatások hogyan azonosítják a virtuális gépeket.  Ha már ismeri a mintát, kihagyhatja ezt a lépést, és közvetlenül folytathatja a [védelem engedélyezését.](#enabling-protection-for-the-azure-vm)

Ez a művelet *get* művelet.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

A *GET* URI rendelkezik az összes szükséges paraméterrel. Nincs szükség további kéréstörzsre.

#### <a name="responses"></a><a name="responses-1"></a>Válaszok

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses"></a><a name="example-responses-1"></a>Példa válaszok

A *GET-kérelem* beküldése után a rendszer 200 (OK) választ ad vissza.

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
> A *GET* válaszban szereplő értékek száma 200-ra korlátozódik egy "oldal" esetében. Használja a "nextLink" mezőt a következő válaszkészlet URL-címének lekérni.

A válasz tartalmazza az összes nem védett Azure `{value}` virtuális gépek listáját, és mindegyik tartalmazza az Azure Recovery Service által a biztonsági mentés konfigurálásához szükséges összes információt. A biztonsági mentés `{name}` beállításához `{virtualMachineId}` jegyezze `{properties}` fel a mezőt és a mezőt a szakaszban. Az alábbi módon két változót hozhat létre ezekből a mezőértékekből.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" +`{name}`
- `{virtualMachineId}`később a [kérelem törzsében használatos](#example-request-body)

A példában a fenti értékek a következőkre mutatnak:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Az Azure virtuális gép védelmének engedélyezése

Miután a megfelelő virtuális gép "gyorsítótárazott" és "azonosított", válassza ki a házirendet védeni. Ha többet szeretne tudni a tárolóban meglévő szabályzatokról, olvassa el a [list Policy API című listát.](https://docs.microsoft.com/rest/api/backup/backuppolicies/list) Ezután válassza ki a [megfelelő házirendet](/rest/api/backup/protectionpolicies/get) a házirend nevére hivatkozva. Házirendek létrehozásához olvassa el a [házirend-oktatóanyag létrehozása](backup-azure-arm-userestapi-createorupdatepolicy.md)című fájlt. Az alábbi példában a "DefaultPolicy" beállítás van kiválasztva.

A védelem engedélyezése egy aszinkron *PUT* PUT-művelet, amely "védett elemet" hoz létre.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` és a fent épített. Az `{fabricName}` "Azure". Példánkban ez a következőket jelenti:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

Védett elem létrehozásához az alábbiakban a kérelem törzsösszetevői.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem erőforrás tulajdonságai         |

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket a [VÉDETT elem REST API-dokumentumának létrehozása](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body)című dokumentum tartalmazza.

##### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérelemtörzs a védett elem létrehozásához szükséges tulajdonságokat határozza meg.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

A `{sourceResourceId}` a `{virtualMachineId}` fent említett [válasz a lista védett elemek](#example-responses-1).

#### <a name="responses"></a>Válaszok

A védett elem létrehozása [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 200 (OK) értéket ad vissza, amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Elfogadva     |         |     Elfogadva    |

##### <a name="example-responses"></a>Példa válaszok

Miután elküldte a PUT kérelmet a védett elemek létrehozására vagy frissítésére, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *PUT* request for protected item creation or update, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy egyszerű *GET* paranccsal.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Miután a művelet befejeződött, 200 (OK) értéket ad vissza a védett elem tartalmával a választörzsben.

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

Ez megerősíti, hogy a virtuális gép védettségengedélyezve van, és az első biztonsági mentés a házirend-ütemezés szerint aktiválódik.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Igény szerinti biztonsági mentés aktiválása egy védett Azure virtuális géphez

Miután egy Azure virtuális gép van konfigurálva a biztonsági mentés, biztonsági mentések történik, mint egy a szabályzat ütemezése. Megvárhatja az első ütemezett biztonsági mentést, vagy bármikor elindíthatja az igény szerinti biztonsági mentést. Az igény szerinti biztonsági mentések megőrzése elkülönül a biztonsági mentési házirend megőrzésétől, és megadható egy adott dátum-idő. Ha nincs megadva, akkor azt feltételezik, hogy 30 nap az igény szerinti biztonsági mentés eseményindítójának napjától.

Az igény szerinti biztonsági mentés aktiválása *postaművelet.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

A `{containerName}` `{protectedItemName}` és a [fent](#responses-1)épített . Az `{fabricName}` "Azure". Példánkban ez a következőket jelenti:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

Az igény szerinti biztonsági mentés aktiválásához a következő összetevők a kérelem törzse.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource tulajdonságai         |

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket a [REST API-dokumentum védett elemekhez való biztonsági másolatkészítési dokumentumban](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)találja.

#### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérelem törzse meghatározza a védett elem biztonsági mentésének elindításához szükséges tulajdonságokat. Ha a megőrzés nincs megadva, a biztonsági mentési feladat aktiválásától 30 napig megmarad.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Válaszok

Az igény szerinti biztonsági mentés aktiválása [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 200 (OK) értéket ad vissza, amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|202 Elfogadva     |         |     Elfogadva    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>Példa válaszok

Miután elküldte a POST kérést egy igény szerinti biztonsági mentésre, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *POST* request for an on-demand backup, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy egyszerű *GET* paranccsal.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Miután a művelet befejeződött, 200 -ot (OK) ad vissza az eredményül kapott biztonsági mentési feladat azonosítójával a választörzsben.

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

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, nyomon kell követni a [rest API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Védett Azure-virtuális gép biztonsági mentési konfigurációjának módosítása

### <a name="changing-the-policy-of-protection"></a>A védelmi politika megváltoztatása

A virtuális gép védelmét szolgáló házirend módosításához ugyanazt a formátumot [használhatja,](#enabling-protection-for-the-azure-vm)mint a védelem engedélyezése. Csak adja meg az új házirend-azonosítót [a kérelem törzsében,](#example-request-body) és küldje el a kérelmet. Például: A testVM házirendjének "DefaultPolicy" -ről "ProdPolicy" értékre való módosításához adja meg a "ProdPolicy" azonosítót a kérelemtörzsben.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A válasz ugyanazt a formátumot követi, mint [avédelem engedélyezése](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>A védelem leállítása, de a meglévő adatok megőrzése

A védett virtuális gép védelmének eltávolításához, de a már biztonsági másolatot, távolítsa el a szabályzatot a kérelem törzsében, és küldje el a kérelmet. A házirenddel való társítás eltávolítása után a biztonsági mentések már nem aktiválódnak, és nem jönnek létre új helyreállítási pontok.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A válasz ugyanazt a formátumot követi, mint az [igény szerinti biztonsági mentés aktiválásához.](#example-responses-3) Az eredményül bejönő feladatot nyomon kell követni a [REST API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

### <a name="stop-protection-and-delete-data"></a>A védelem leállítása és az adatok törlése

A védett virtuális gép védelmének eltávolításához és a biztonsági mentési adatok törléséhez hajtson végre egy [itt](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)részletezett törlési műveletet.

A védelem leállítása és az adatok törlése *TÖRLÉSi* művelet.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` és a [fent](#responses-1)épített . `{fabricName}`"Azure". Példánkban ez a következőket jelenti:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>Válaszok

*A DELETE* védelem [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 204 (NoContent) a művelet befejezésekor.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|204 NoContent     |         |  Nincs tartalom       |
|202 Elfogadva     |         |     Elfogadva    |

> [!IMPORTANT]
> A véletlen törlési forgatókönyvek elleni védelem érdekében a Recovery Services-tároló ban elérhető egy [helyreállítható törlési funkció.](use-restapi-update-vault-properties.md#soft-delete-state) Ha a tároló helyreállítható törlési állapota engedélyezve van, akkor a törlési művelet NEM törli azonnal az adatokat. 14 napig őrzik, majd véglegesen megtisztítják. A 14 napos időszakra az ügyfélnek nem kell fizetnie a tárolásért. A törlési művelet visszavonásához olvassa el a [visszavonás-törlés című szakaszt.](#undo-the-stop-protection-and-delete-data)

### <a name="undo-the-stop-protection-and-delete-data"></a>A stop védelem visszavonása és adatok törlése

A véletlen törlés visszavonása hasonló a biztonsági másolat elemének létrehozásához. A törlés visszavonása után az elem megmarad, de a jövőbeli biztonsági mentések nem aktiválódnak.

A visszavonás visszavonása olyan *PUT* művelet, amely nagyon hasonlít [a házirend módosításához](#changing-the-policy-of-protection) és/vagy [a védelem engedélyezéséhez.](#enabling-protection-for-the-azure-vm) Csak adja meg a szándékot, hogy visszavonja a törlésa a változó *isRehydrate* [a kérelem törzsében,](#example-request-body) és küldje el a kérelmet. Például: A testVM törlésének visszavonásához a következő kérelemtörzset kell használni.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

A válasz ugyanazt a formátumot követi, mint az [igény szerinti biztonsági mentés aktiválásához.](#example-responses-3) Az eredményül bejönő feladatot nyomon kell követni a [REST API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

## <a name="next-steps"></a>További lépések

[Adatok visszaállítása egy Azure virtuális gép biztonsági mentéséről.](backup-azure-arm-userestapi-restoreazurevms.md)

Az Azure Backup REST API-król az alábbi dokumentumokban talál további információt:

- [Az Azure Recovery Services szolgáltatóREST API-ja](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
