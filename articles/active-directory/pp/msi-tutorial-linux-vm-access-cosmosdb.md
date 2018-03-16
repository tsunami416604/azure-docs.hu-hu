---
title: "A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) használja a Linux virtuális gép Azure Cosmos DB eléréséhez"
description: "Ez az oktatóanyag végigvezeti egy User-Assigned felügyelt szolgáltatás identitásának (MSI) használata a Linux virtuális gép Azure Cosmos adatbázis elérésére."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dbb5e9e8f9accd618599010ab2bbb4a8760e534f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) használja a Linux virtuális gép Azure Cosmos DB eléréséhez 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan hozzon létre és egy felhasználó által hozzárendelt felügyelt szolgáltatás identitás (MSI) a Linux virtuális gép, majd használja az Azure Cosmos adatbázis elérésére. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy felhasználó lehet hozzárendelve felügyelt Szolgáltatásidentitás (MSI)
> * Rendelje hozzá a felhasználó által hozzárendelt MSI a Linux virtuális gépek
> * Az MSI hozzáférést Azure Cosmos DB példánya
> * Szereznie egy hozzáférési jogkivonatot, a felhasználó által hozzárendelt MSI-azonosítójának használatával, és Azure Cosmos DB hozzáférhetne

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy az Azure-portálon keresztül a **, próbálja** gomb, minden egyes kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure portálon, a [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben az oktatóanyagban létrehozhatunk egy új Linux virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1. Kattintson a **+ hozzon létre egy erőforrást** az Azure portál bal felső sarkában található.
2. Válassza ki **számítási**, majd válassza ki **Ubuntu Server 16.04 LTS VM**.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

    ![MSI Linux virtuális gép](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép meg a legördülő listában.
5. A **erőforráscsoport**, válassza a **hozzon létre új** és a virtuális gép adja meg az erőforráscsoport nevét. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megtekintéséhez válasszon **összes** , vagy módosítsa a lemez típusát támogatott szűrő. Az a **beállítások** paneljén megtartani az alapértelmezett beállításokat, majd kattintson **OK**.

## <a name="create-a-user-assigned-msi"></a>A felhasználó által hozzárendelt MSI-fájl létrehozása

1. A parancssori felület konzol (helyett egy Azure-felhő rendszerhéj munkamenet) használata, ha először jelentkezik be az Azure-bA. Használjon olyan fiókot, amelybe szeretné létrehozni az új MSI Azure-előfizetéssel társított:

    ```azurecli
    az login
    ```

2. Hozzon létre egy felhasználó által hozzárendelt MSI az [az identitás létrehozása](/cli/azure/identity#az_identity_create). A `-g` paraméter határozza meg az erőforráscsoportot, ahol az MSI-fájl jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A válasz tartalmazza a részleteket, a felhasználó által hozzárendelt MSI létrehozza, az alábbi példához hasonló (Megjegyzés: a `clientId` és `id` értékei az MSI-fájl, a későbbi lépésekben használva):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép

Egy alapértelmezett MSI eltérően a felhasználó által hozzárendelt MSI több Azure-erőforrások az ügyfelek által használható. Ebben az oktatóanyagban, rendelje hozzá egy virtuális. Is hozzárendelheti az egynél több virtuális géphez.

A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép [az vm hozzárendelése-identitás](/cli/azure/vm#az_vm_assign_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. Használja a `id` tulajdonságot adott vissza az előző lépésben a `--identities` paraméter értékét:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása 

Ha még nem rendelkezik egy, most hozzon létre egy Cosmos DB fiókot. Is ezt a lépést kihagyhatja, és egy meglévő Cosmos DB fiók használatával, ha szeretné. 

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

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>A felhasználó hozzárendelt MSI hozzáférést biztosíthat a Cosmos DB tárelérési kulcsok

Cosmos DB natív módon támogatja az Azure AD-alapú hitelesítés.  Azonban egy olyan MSI Csomaghoz segítségével lekérni egy Cosmos DB hozzáférési kulcsot a Resource Manager, majd a kulcs Cosmos-adatbázis eléréséhez használja.  Ebben a lépésben megadta a felhasználói MSI hozzáféréssel rendelkező, a kulcsokhoz, a Cosmos DB fiókhoz.

Először a MSI identitás hozzáférési jogot a Cosmos DB fióknak az Azure Resource Manager az Azure parancssori felület használatával. Frissítse az értékeket a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` alkalmaz környezetében. Cserélje le `<MSI PRINCIPALID>` rendelkező a `principalId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi).  Cosmos DB két részletességi támogatja a tárelérési kulcsok használata esetén: olvasási/írási hozzáfér a fiókjához, és olvasási hozzáférést ahhoz a fiókhoz.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkör, ha azt szeretné, hogy a fiók olvasási/írási kulcsokat, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkör, ha le szeretné kérdezni a fiók csak olvasható kulcsok:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

A válasz tartalmazza a szerepkör-hozzárendelés létrehozása a részletei:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot, a felhasználó hozzárendelt MSI-fájl használatával, és hívja az Azure Resource Manager használatával

Az oktatóanyag a hátralévő működik a korábban létrehozott virtuális gépről.

Lépések elvégzéséhez szüksége van egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/install_guide). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](../../virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Linux virtuális gépet, majd a a **áttekintése** kattintson **Connect** tetején. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik. 
2. Csatlakoztassa a virtuális Gépet az SSH-ügyfél használatával.  
3. A következő kéri be a **jelszó** létrehozásakor hozzáadta a **Linux virtuális gép**. Meg kell majd lehet sikeres volt.  
4. CURL használatával szerezze be a hozzáférési tokent az Azure Resource Manager.  

    A CURL kérés- és a hozzáférési token nem éri el.  Cserélje le <CLIENT ID> ClientID-azonosítóját az érték a felhasználó hozzárendelt MSI: 
    
    ```bash
    curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>" 
    ```
    
    > [!NOTE]
    > Az előző kérelem a "resource" paraméter értékének az Azure AD által várt pontosan egyeznie kell. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n.
    > A következő reagálva a access_token elem szerint lettek rövidítve kivonatosan mutatja.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Tárelérési kulcsok beolvasása az Azure Resource Manager Cosmos DB hívásokat  

Mostantól a CURL használatával hívja az erőforrás-kezelőt a hozzáférési jogkivonat azt lekérése az előző szakaszban a Cosmos DB hívóbetűre beolvasása. Ha a hozzáférési kulcs van, azt Cosmos DB lekérdezheti. Ügyeljen arra, hogy cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` paraméterértékeket a saját értékekkel. Cserélje le a `<ACCESS TOKEN>` érték és a korábban kapott hozzáférési jogkivonat.  Ha lekéréséhez olvasási/írási kulcsok, kulcs művelet típust használjon `listKeys`.  Ha szeretné letölteni a csak olvasható kulcsokat, a kulcs művelet típust használjon `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Az előzetes URL-címben a szöveg kis-és nagybetűket, ezért győződjön meg arról, ha ennek megfelelően igazodjon felső-kisbetűk az erőforráscsoportok használ. Ezenkívül fontos, hogy ez egy POST kérést nem GET kérést, és adja meg egy értéket a korlát - d, amelyek NULL is lehet rögzíteni.  

A CURL válasz lehetővé teszi a listán szereplő kulcsokra.  Ha például a csak olvasható kulcsok elérhetővé:  

```bash 
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

- MSI áttekintését lásd: [felügyelt szolgáltatás identitásának (MSI) az Azure-erőforrások](msi-overview.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
