---
title: Az Azure-fájlmegosztások biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági,
description: Megtudhatja, hogy miként ment mentés az Azure-fájlmegosztásokról a Recovery Services Vaultban a REST API használatával
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248098"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Azure-fájlmegosztás biztonsági mentése az Azure Biztonsági másolat segédprogramjával a Rest API-n keresztül

Ez a cikk ismerteti, hogyan készíthet biztonsági másolatot egy Azure-fájlmegosztásról az Azure Backup használatával REST API-n keresztül.

Ez a cikk feltételezi, hogy már létrehozott egy helyreállítási szolgáltatások tárolóját és házirendjét a fájlmegosztás biztonsági mentésének konfigurálásához. Ha még nem, olvassa el a [hozzon létre egytárolót,](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) és [hozzon létre házirend](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) REST API oktatóanyagok új tárolók és szabályzatok létrehozásához.

Ebben a cikkben a következő forrásokat használjuk:

- **RecoveryServicesVault**: *azurefilesvault*

- **Házirend:** *schedule1*

- **Erőforráscsoport**: *azurefiles*

- **Tárfiók**: *testvault2*

- **Fájlmegosztás**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Nem védett Azure-fájlmegosztás biztonsági másolatának konfigurálása REST API használatával

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Tárfiókok felderítése nem védett Azure-fájlmegosztásokkal

A tárolónak fel kell derítenie az összes Azure storage-fiókot az előfizetésben olyan fájlmegosztásokkal, amelyekről a Recovery Services-tantárolóba lehet biztonsági másolatot adni. Ez a [frissítési művelettel](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)aktiválódik. Ez egy aszinkron *POST-művelet,* amely biztosítja, hogy a tároló megkapja az aktuális előfizetésben lévő összes nem védett Azure File-megosztás okainak legújabb listáját, és "gyorsítótárazza" azokat. Miután a fájlmegosztás "gyorsítótárazott", a helyreállítási szolgáltatások hozzáférhetnek a fájlmegosztáshoz és megvédhetik azt.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

A POST `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}`a `{fabricName}` , , és paramétereivel rendelkezik. A példánkban a különböző paraméterek értéke a következő:

- `{fabricName}`az *Azure*

- `{vaultName}`*az azurefilesvault*

- `{vaultresourceGroupName}`*az azurefiles*

- $filter=backupManagementType eq "AzureStorage"

Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelemtörzsre.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Válaszok

A "frissítési" művelet [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 200 (OK) a művelet befejezésekor.

##### <a name="example-responses"></a>Példa válaszok

A *POST-kérelem* benyújtását követően a rendszer 202(Elfogadva) választ ad vissza.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Az eredményül kapott művelet nyomon követése a "Hely" fejléc segítségével egy egyszerű *GET* paranccsal

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Miután az összes Azure Storage-fiókok felderítése, a GET parancs egy 200 (nincs tartalom) választ ad vissza. A tároló most már képes felderíteni minden tárfiókot fájlmegosztások, amelyek az előfizetésen belül biztonsági másolatot lehet.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>A Recovery Services-tárolóval védhető tárfiókok listájának beszerzése

Annak ellenőrzéséhez, hogy a "gyorsítótárazás" megtörtént-e, sorolja fel az összes védett tárfiókot az előfizetés alatt. Ezután keresse meg a kívánt tárfiókot a válaszban. Ez a [GET ProtectableContainers](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list) művelethasználatával történik.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

A *GET* URI rendelkezik az összes szükséges paraméterrel. Nincs szükség további kéréstörzsre.

Példa a választörzsre:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Mivel a *testvault2* tárfiókot a választörzsben a rövid névvel találjuk, a fent végrehajtott frissítési művelet sikeres volt. A helyreállítási szolgáltatások tárolómost már sikeresen felderítheti a nem védett fájlokat ugyanabban az előfizetésben lévő tárfiókokat.

### <a name="register-storage-account-with-recovery-services-vault"></a>Tárfiók regisztrálása a Recovery Services-tárolóval

Ez a lépés csak akkor szükséges, ha korábban nem regisztrálta a tárfiókot a tárolóval. A tárolóregisztrálhatja a [ProtectionContainers-Register műveletet.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Az URI változóit az alábbiak szerint állítsa be:

- {resourceGroupName} - *azurefiles*
- {fabricName} – *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} – Ez a name attribútum a GET ProtectableContainers művelet választörzsében.
   A példánkban a *StorageContainer; Tárolás; AzureFiles;testvault2*

>[!NOTE]
> Mindig vegye be a válasz névattribútumát, és töltse ki ezt a kérést. Ne kódolj, vagy hozzon létre a tárolónév formátumot. Ha létrehozza vagy kódolja, az API-hívás sikertelen lesz, ha a tároló névformátuma a jövőben megváltozik.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

A létrehozási kérelem törzse a következő:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket a [ProtectionContainers-Register című dokumentumban találja.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer)

Ez egy aszinkron művelet, és két választ ad vissza: "202 Elfogadva", amikor a műveletet elfogadják, és "200 OK", amikor a művelet befejeződött.  A művelet állapotának nyomon követéséhez használja a helyfejlécet a művelet legutóbbi állapotának beolvasásához.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Példa választörzsre, ha a művelet befejeződött:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Ellenőrizheti, hogy a regisztráció sikeres volt-e a *választörzsregisztrációs* paraméter értékéből. A mi esetünkben, ez azt mutatja, az állapot regisztrált *testvault2*, így a regisztrációs művelet sikeres volt.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Az összes nem védett fájl megosztásának lekérdezése tárfiók alatt

A [védelmi tárolók-lekérdezés](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) művelettel érdeklődhet a tárfiókvédett elemeiről. Ez egy aszinkron művelet, és az eredményeket a helyfejléc használatával kell nyomon követni.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Állítsa be a fenti URI változóit az alábbiak szerint:

- {vaultName} - *azurefilesvault*
- {fabricName} – *Azure*
- {containerName}- Tekintse meg a name attribútumot a GET ProtectableContainers művelet választörzsében. A példánkban a *StorageContainer; Tárolás; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Ha a kérelem sikeres, az "OK" állapotkódot adja vissza.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Válassza ki a biztonsági másolatot létrehozni kívánt fájlmegosztást

Az előfizetés alatt felsorolhatja az összes védhető elemet, és megkeresheti a get [backupprotectableItems](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) művelettel biztonsági másolatot készíteni kívánt fájlmegosztásról.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Az URI-t az alábbiak szerint kell megépíteni:

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq "AzureStorage"*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Mintaválasz:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

A válasz tartalmazza az összes nem védett fájlmegosztások listáját, és tartalmazza az Azure Recovery Service által a biztonsági mentés konfigurálásához szükséges összes információt.

### <a name="enable-backup-for-the-file-share"></a>Biztonsági mentés engedélyezése a fájlmegosztáshoz

Miután a megfelelő fájlmegosztást "azonosította" a rövid névvel, válassza ki a védeni kívánt házirendet. Ha többet szeretne megtudni a tárolóban meglévő szabályzatokról, olvassa el a [list Policy API című listát.](https://docs.microsoft.com/rest/api/backup/backuppolicies/list) Ezután válassza ki a [megfelelő házirendet](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) a házirend nevére hivatkozva. Házirendek létrehozásához olvassa el a [házirend-oktatóanyag létrehozása](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)című fájlt.

A védelem engedélyezése egy aszinkron *PUT* PUT-művelet, amely "védett elemet" hoz létre.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Állítsa be a **containername** és **a protecteditemname** változókat a GET backupprotectableitems művelet választörzsében lévő ID attribútum használatával.

A példánkban a védeni kívánt fájlmegosztás azonosítója a következő:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

Vagy hivatkozhat a védelmi tároló **névattribútumára** és a védett elemválaszokra.

>[!NOTE]
>Mindig vegye be a válasz névattribútumát, és töltse ki ezt a kérést. Ne kódolja, és ne hozza létre a tárolónév-vagy védett elemnév-formátumot. Ha létrehozza vagy kódolja, az API-hívás sikertelen lesz, ha a tárolónév-formátum vagy a védett elemnév formátuma a jövőben megváltozik.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Kérelemtörzs létrehozása:

A következő kérelemtörzs a védett elem létrehozásához szükséges tulajdonságokat határozza meg.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

A **sourceResourceId** a get backupprotectableItems válaszul **parentcontainerFabricID.**

Mintaválasz

A védett elem létrehozása egy aszinkron művelet, amely egy másik műveletet hoz létre, amelyet nyomon kell követni. Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 200 (OK) a művelet befejezésekor.

Miután elküldte a *PUT* kérelmet a védett elemek létrehozására vagy frissítésére, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy *GET* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Miután a művelet befejeződött, 200 (OK) értéket ad vissza a védett elem tartalmával a választörzsben.

Mintaválasz törzse:

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

Ez megerősíti, hogy a fájlmegosztás védettsége engedélyezve van, és az első biztonsági mentés a házirend-ütemezésnek megfelelően aktiválódik.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Igény szerinti biztonsági mentés aktiválása a fájlmegosztáshoz

Miután egy Azure-fájlmegosztás van konfigurálva a biztonsági mentés, a biztonsági mentések a házirend-ütemezés szerint futnak. Megvárhatja az első ütemezett biztonsági mentést, vagy bármikor elindíthatja az igény szerinti biztonsági mentést.

Az igény szerinti biztonsági mentés aktiválása postaművelet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

A(z) {containerName} és a{protectedItemName} a fenti módon épül fel a biztonsági mentés engedélyezése közben. Példánkban ez a következőket jelenti:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Kérelemtörzs létrehozása

Az igény szerinti biztonsági mentés aktiválásához a következő összetevők a kérelem törzse.

| Név       | Típus                       | Leírás                       |
| ---------- | -------------------------- | --------------------------------- |
| Tulajdonságok | AzurefilesharebackupReques | BackupRequestResource tulajdonságai |

A kérelemtörzs definícióinak teljes listáját és egyéb részleteket a [REST API-dokumentum védett elemekhez való biztonsági másolatkészítési dokumentumban](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)találja.

Példa a törzs igénylésére

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Válaszok

Az igény szerinti biztonsági mentés aktiválása [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, és 200 (OK) a művelet befejezésekor.

### <a name="example-responses"></a>Példa válaszok

Miután elküldte a POST kérést egy igény szerinti biztonsági mentésre, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *POST* request for an on-demand backup, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy *GET* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Miután a művelet befejeződött, 200 -ot (OK) ad vissza az eredményül kapott biztonsági mentési feladat azonosítójával a választörzsben.

#### <a name="sample-response-body"></a>Mintaválasz törzse

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Mivel a biztonsági mentési feladat egy hosszú ideig futó művelet, nyomon kell követni a [rest API-dokumentum használatával a figyelőfeladatokban leírtak szerint.](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan állíthatja vissza az [Azure-fájlmegosztásokat a Rest API használatával.](restore-azure-file-share-rest-api.md)
