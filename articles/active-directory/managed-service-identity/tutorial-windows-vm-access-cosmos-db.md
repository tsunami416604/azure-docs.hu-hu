---
title: Az Azure Cosmos DB elérése Windows VM-beli felügyeltszolgáltatás-identitással
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Cosmos DB-hez egy Windows VM-beli, rendszer által hozzárendelt felügyeltszolgáltatás-identitással.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 5f7a0f2bd6820ce65490ae9241dac519fb635da2
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885458"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-cosmos-db"></a>Oktatóanyag: Az Azure Cosmos DB elérése Windows VM-beli felügyeltszolgáltatás-identitással

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá a Cosmos DB-hez egy windowsos virtuális gép (VM) rendszer által hozzárendelt identitásával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Cosmos DB-fiók létrehozása
> * Windows VM felügyeltszolgáltatás-identitás hozzáférésének biztosítása a Cosmos DB-fiók hozzáférési kulcsaihoz
> * Hozzáférési jogkivonat lekérése a Windows VM felügyeltszolgáltatás-identitásával az Azure Resource Manager meghívásához
> * Hozzáférési kulcsok lekérése az Azure Resource Managerből Cosmos DB-hívások indításához

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Bejelentkezés az Azure Portalra](https://portal.azure.com)

- [Windows rendszerű virtuális gép létrehozása](/azure/virtual-machines/windows/quick-create-portal)

- [Rendszer által hozzárendelt identitás engedélyezése a virtuális gépen](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása 

Ha még nincs fiókja, hozzon létre egy Cosmos DB-fiókot. Ezt a lépést kihagyhatja, ha egy meglévő Cosmos DB-fiókot használ. 

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Kattintson az **Adatbázisok**, majd az **Azure Cosmos DB** lehetőségre. Ekkor megjelenik egy új, „Új fiók” nevű panel.
3. Adja meg a Cosmos DB-fiók **azonosítóját**, amelyet később használni fog.  
4. Az **API** értéke legyen „SQL”. Az ebben az oktatóanyagban ismertetett megközelítést más API-típusokkal is használhatja, de az itt szereplő lépések az SQL API-ra vonatkoznak.
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.  Válasszon ki egy olyan **helyet**, ahol a Cosmos DB elérhető.
6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Gyűjtemény létrehozása Cosmos DB-fiókban

Adjon hozzá egy adatgyűjteményt a Cosmos DB-fiókhoz, amelyet a későbbi lépések során lekérdezhet.

1. Lépjen az újonnan létrehozott Cosmos DB-fiókra.
2. Az **Áttekintés** lapon kattintson a **+/Gyűjtemény hozzáadása** gombra. Ekkor megjelenik a „Gyűjtemény hozzáadása” panel.
3. Adja meg a gyűjtemény adatbázis- és gyűjteményazonosítóját, válasszon ki egy tárkapacitást, adjon meg partíciókulcsot és átviteli sebességet, majd kattintson az **OK** gombra.  Ebben az oktatóanyagban elég a „Teszt” kifejezést használni az adatbázis és a gyűjtemény azonosítójaként, kiválasztani egy rögzített kapacitást és a legalacsonyabb átviteli sebességet (400 RU/s).  

## <a name="grant-windows-vm-managed-service-identity-access-to-the-cosmos-db-account-access-keys"></a>Windows VM felügyeltszolgáltatás-identitás hozzáférésének biztosítása a Cosmos DB-fiók hozzáférési kulcsaihoz

A Cosmos DB nem támogatja natív módon az Azure AD-hitelesítést. A felügyeltszolgáltatás-identitás használatával azonban lekérheti a Cosmos DB hozzáférési kulcsát a Resource Managerből, és azzal elérheti a Cosmos DB-t. Ebben a lépésben hozzáférést biztosít a felügyeltszolgáltatás-identitás számára a Cosmos DB-fiók kulcsaihoz.

Ha a PowerShell-lel szeretne hozzáférést adni a felügyeltszolgáltatás-identitásnak a Cosmos DB-fiókhoz az Azure Resource Managerben, frissítse a környezet `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` és `<COSMOS DB ACCOUNT NAME>` értékét. Cserélje le az `<MSI PRINCIPALID>` elemet a [Linux VM MSI PrincipalId azonosítójának lekérésével kapcsolatos szakaszban](#retrieve-the-principalID-of-the-linux-VM's-MSI) a `az resource show` által visszaadott `principalId` tulajdonságra.  A Cosmos DB a részletesség két szintjét támogatja a hozzáférési kulcsok használatakor: a fiók írási/olvasási, illetve írásvédett hozzáférését.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkört, ha a fiók írási/olvasási kulcsait szeretné lekérni, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkört, ha írásvédett hozzáférést szeretne a fiókhoz:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-managed-service-identity-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a Windows VM felügyeltszolgáltatás-identitásával az Azure Resource Manager meghívásához

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk. 

Ebben a részben az Azure Resource Manager PowerShell-parancsmagokat kell használnia.  Ha nincs telepítve, a folytatás előtt [töltse le a legújabb verziót](https://docs.microsoft.com/powershell/azure/overview).

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) legújabb verzióját is telepítenie kell a Windows VM-en.

1. Az Azure Portalon lépjen a **Virtuális gépek** lapra, keresse meg a Windows rendszerű virtuális gépet, majd kattintson az **Áttekintés** lap tetején található **Csatlakozás** gombra. 
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.
4. A Powershell Invoke-WebRequest parancsával küldjön kérést a helyi felügyeltszolgáltatás-identitási végpontra, hogy lekérjen egy hozzáférési jogkivonatot az Azure Resource Managerhez.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > A „resource” paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    
    Ezután nyerje ki a „Content” elemet, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hozzáférési kulcsok lekérése az Azure Resource Managerből Cosmos DB-hívások indításához

Most PowerShell használatával hívjuk meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal a Cosmos DB-fiók hozzáférési kulcsának lekéréséhez. Amint megkaptuk a hozzáférési kulcsot, a Cosmos DB lekérdezhetővé válik. A `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` és `<COSMOS DB ACCOUNT NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. Az `<ACCESS TOKEN>` paraméter értékét cserélje le a korábban lekért hozzáférési jogkivonattal.  Ha olvasási/írási kulcsokat szeretne lekérni, akkor `listKeys` típusú kulcsműveleteket használjon.  Ha írásvédett kulcsokat kíván lekérni, akkor pedig `readonlykeys` típusúakat:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
A válasz a kulcsok listáját tartalmazza.  Ha például írásvédett kulcsokat kap:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Most, hogy rendelkezik a Cosmos DB-fiók hozzáférési kulcsával, átadhatja azt egy Cosmos DB SDK-nak, és hívásokat indíthat a fiók elérése érdekében.  Átadhatja például a hozzáférési kulcsot az Azure CLI-nek.  A(z) <COSMOS DB CONNECTION URL> elemet az Azure Portalon, a Cosmos DB-fiók panelének **Áttekintés** lapjáról szerezheti be.  Cserélje le a(z) <ACCESS KEY> elemet az így beszerzett értékre:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Ez a CLI-parancs a gyűjtemény részleteit adja vissza:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan hozhat létre Windows felügyeltszolgáltatás-identitásokat a Cosmos DB eléréséhez.  További információ a Cosmos DB-ről:

> [!div class="nextstepaction"]
>[Az Azure Cosmos DB áttekintése](/azure/cosmos-db/introduction)


