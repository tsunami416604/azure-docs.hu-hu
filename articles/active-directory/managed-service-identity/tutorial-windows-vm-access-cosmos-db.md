---
title: A Windows virtuális gép MSI Azure Cosmos DB eléréséhez használja
description: Ez az oktatóanyag végigvezeti egy System-Assigned felügyelt szolgáltatás identitásának (MSI) használata a Windows virtuális gép Azure Cosmos adatbázis elérésére.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: ed225206e512ff64835d4d90b35bd44800e0a559
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595376"
---
# <a name="tutorial-use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>Oktatóanyag: A Windows virtuális gép MSI Azure Cosmos DB elérésére használhat.

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan létrehozása és használata a Windows virtuális gép MSI Cosmos-adatbázis eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy MSI engedélyezve van a Windows virtuális gép 
> * Cosmos DB-fiók létrehozása
> * A Cosmos DB tárelérési kulcsok GRANT Windows virtuális gép MSI elérésére
> * Szereznie egy hozzáférési jogkivonatot a Windows virtuális gép MSI hívni az Azure Resource Manager használatával
> * Tárelérési kulcsok beolvasása az Azure Resource Manager Cosmos DB hívásokat

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban azt hozzon létre egy új Windows virtuális Gépet.  A meglévő virtuális MSI is engedélyezheti.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3. Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gép.
4. Válassza ki a megfelelő **előfizetés** a virtuális gép meg a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** , amelyen a virtuális gép létrehozásához, **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások lapon hagyja az alapértelmezett beállításokat, majd kattintson **OK**.

   ![Kép helyettesítő szövege](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése 

A virtuális gép MSI hozzáférési jogkivonatok lekérni az Azure AD anélkül, hogy a hitelesítő adatok kerüljenek a kódot a teszi lehetővé. A színfalak engedélyezése egy virtuális gépen az Azure-portálon MSI két dolgot eredményez: regisztrálja a virtuális Gépet az Azure AD-hoz létre egy felügyelt, és konfigurálja a virtuális gép identitását.

1. Válassza ki a **virtuális gép** , hogy szeretné-e engedélyezze MSI-t.  
2. A bal oldali navigációs sávon kattintson **konfigurációs**. 
3. Látni **Szolgáltatásidentitás felügyelt**. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem. 
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  
   ![Kép helyettesítő szövege](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása 

Ha még nem rendelkezik egy, hozzon létre egy Cosmos DB fiókot. Ezt a lépést kihagyhatja, és meglévő Cosmos DB fiók. 

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **adatbázisok**, majd **Azure Cosmos DB**, és egy új "új fiók" panel jeleníti meg.
3. Adjon meg egy **azonosító** a Cosmos DB fiók, amely későbbi használatra.  
4. **API** kell beállítani az "SQL". Az ebben az oktatóanyagban leírt módszer is használható a rendelkezésre álló API típusú, de ez az oktatóanyag lépéseit az SQL API-hoz.
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.  Válassza ki a **hely** ahol Cosmos DB áll rendelkezésre.
6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Hozzon létre egy gyűjteményt a Cosmos DB-fiókban

Ezután adja hozzá a adatok gyűjtése a Cosmos DB fiók, amely a későbbi lépésekben kérdezhetők le.

1. Nyissa meg az újonnan létrehozott Cosmos DB fiók.
2. Az a **áttekintése** lapon kattintson a **/ gyűjtemény hozzáadása** gombra, és egy "gyűjtemény hozzáadása" kimenő diák panelen.
3. Adjon a gyűjtemény egy adatbázis Azonosítóját, a gyűjtemény Azonosítóját, jelöljön ki egy tárolási kapacitás, adjon meg egy partíciókulcsot, adjon meg egy átviteli értéket, majd kattintson **OK**.  A jelen oktatóanyag esetében is elegendő az használja a "Teszt" az adatbázis-azonosító és a gyűjtemény Azonosítóját, válasszon ki egy rögzített tárolási kapacitás és a legalacsonyabb átviteli sebesség (400 RU/mp).  

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>A Cosmos DB tárelérési kulcsok GRANT Windows virtuális gép MSI elérésére

Cosmos DB natív módon támogatja az Azure AD-alapú hitelesítés. Azonban egy Cosmos DB hozzáférési kulcsot a Resource Manager lekéréséhez egy olyan MSI Csomaghoz használja, és a kulcs Cosmos-adatbázis eléréséhez használja. Ebben a lépésben meg hozzáférést a MSI a kulcsokat a Cosmos DB-fiókhoz.

A Cosmos DB-fiókba az Azure Resource Manager PowerShell-lel MSI identitás hozzáférést engedélyez, frissítés a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` alkalmaz környezetében. Cserélje le `<MSI PRINCIPALID>` rendelkező a `principalId` tulajdonság által visszaadott a `az resource show` parancsot [beolvasni a Linux virtuális gép MSI a principalID](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB két részletességi támogatja a tárelérési kulcsok használata esetén: olvasási/írási hozzáfér a fiókjához, és olvasási hozzáférést ahhoz a fiókhoz.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkör, ha azt szeretné, hogy a fiók olvasási/írási kulcsokat, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkör, ha le szeretné kérdezni a fiók csak olvasható kulcsok:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot a Windows virtuális gép MSI hívni az Azure Resource Manager használatával

Az oktatóanyag a hátralévő azt fog működni a korábban létrehozott virtuális gépről. 

Szüksége lesz az Azure Resource Manager PowerShell-parancsmagok használatához a részében.  Ha nincs telepítve, ez a [a legújabb verzió letöltéséhez](https://docs.microsoft.com/powershell/azure/overview) folytatása előtt.

Telepítse a legújabb verzióját is kell [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a Windows virtuális gépén.

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Windows rendszerű virtuális gép, majd a a **áttekintése** kattintson **Connect** tetején. 
2. Adja meg a **felhasználónév** és **jelszó** számára, amely hozzá van, a Windows virtuális gép létrehozása után. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** a virtuális géppel, nyissa meg a Powershellt a távoli munkamenet.
4. A helyi MSI-végpont megszerezni egy hozzáférési jogkivonatot az Azure Resource Manager használatával Powershell Invoke-WebRequest, indítson egy lekérdezést.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > A "resource" paraméter értékének az Azure AD által várt pontosan egyeznie kell. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n.
    
    Ezután bontsa ki a "Tartalom" elemet, amely tárolja a $response objektum JavaScript Object Notation (JSON) formátumú karakterláncot. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután bontsa ki a hozzáférési jogkivonat a választ.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Tárelérési kulcsok beolvasása az Azure Resource Manager Cosmos DB hívásokat

Mostantól a PowerShell használatával hívja az erőforrás-kezelőt a hozzáférési jogkivonat lekérése az előző szakaszban a Cosmos DB hívóbetűre beolvasása. Ha a hozzáférési kulcs van, azt Cosmos DB lekérdezheti. Ügyeljen arra, hogy cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` paraméterértékeket a saját értékekkel. Cserélje le a `<ACCESS TOKEN>` érték és a korábban kapott hozzáférési jogkivonat.  Ha lekéréséhez olvasási/írási kulcsok, kulcs művelet típust használjon `listKeys`.  Ha szeretné letölteni a csak olvasható kulcsokat, a kulcs művelet típust használjon `readonlykeys`:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
A válasz a listán szereplő kulcsokra biztosítják.  Ha például csak olvasható kulcsok elérhetővé:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Most, hogy a hozzáférési kulcsot a Cosmos DB fiók adja át azt egy Cosmos DB SDK és a hívásokat a fiók eléréséhez.  Gyors például az Azure parancssori felület a hozzáférési kulcsot tud átadni.  Beszerezheti a <COSMOS DB CONNECTION URL> a a **áttekintése** lapját, amelyen a Cosmos DB-fiók panelen az Azure portálon.  Cserélje le a <ACCESS KEY> a fentiekben kapott érték:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

A parancssori felület parancs visszaadja a gyűjtemény adatait:

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

Ebben az oktatóanyagban megtudta, hogyan hozható létre Windows felügyelt Szolgáltatásidentitás Cosmos-adatbázis eléréséhez.  További információért lásd a Cosmos DB:

> [!div class="nextstepaction"]
>[Az Azure Cosmos DB áttekintése](/azure/cosmos-db/introduction)


