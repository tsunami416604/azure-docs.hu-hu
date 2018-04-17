---
title: A Linux virtuális gép MSI Azure Cosmos DB eléréséhez használja
description: Ez az oktatóanyag végigvezeti egy System-Assigned felügyelt szolgáltatás identitásának (MSI) használata a Linux virtuális gép Azure Cosmos adatbázis elérésére.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: 507986e4fa83e1821b1d7a1938b356feee81e9d2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>A Linux virtuális gép MSI Azure Cosmos DB eléréséhez használja 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Ez az oktatóanyag bemutatja, hogyan létrehozása és használata a Linux virtuális gép MSI. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy Linux virtuális gép MSI engedélyezve
> * Cosmos DB-fiók létrehozása
> * Hozzon létre egy gyűjteményt a Cosmos DB-fiókban
> * Az MSI hozzáférést Azure Cosmos DB példánya
> * Beolvasni a `principalID` az a, a Linux virtuális gép MSI
> * Szereznie egy hozzáférési jogkivonatot, és hívja az Azure Resource Manager használatával
> * Tárelérési kulcsok beolvasása az Azure Resource Manager Cosmos DB hívásokat

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com) folytatása előtt.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy az Azure-portálon keresztül a **, próbálja** gomb, minden egyes kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben az oktatóanyagban hozzon létre egy új MSI engedélyezve van a Linux virtuális gép.

Az MSI-kompatibilis virtuális gép létrehozása:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuális Gépet az Azure-előfizetéshez társított:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, a virtuális Gépet, és azok kapcsolódó erőforrásait, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már van erőforráscsoport szeretné használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gép az [az virtuális gép létrehozása](/cli/azure/vm/#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* rendelkező egy olyan MSI Csomaghoz, amint azt a `--assign-identity` paraméter. A `--admin-username` és `--admin-password` paraméterek fiókot ad meg a rendszergazda felhasználó nevét és jelszavát virtuálisgép-bejelentkezés. Ezeket az értékeket a környezetének megfelelő frissítése: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12

## Create a Cosmos DB account 

If you don't already have one, create a Cosmos DB account. You can skip this step and use an existing Cosmos DB account. 

1. Click the **+/Create new service** button found on the upper left-hand corner of the Azure portal.
2. Click **Databases**, then **Azure Cosmos DB**, and a new "New account" panel  displays.
3. Enter an **ID** for the Cosmos DB account, which you use later.  
4. **API** should be set to "SQL." The approach described in this tutorial can be used with the other available API types, but the steps in this tutorial are for the SQL API.
5. Ensure the **Subscription** and **Resource Group** match the ones you specified when you created your VM in the previous step.  Select a **Location** where Cosmos DB is available.
6. Click **Create**.

## Create a collection in the Cosmos DB account

Next, add a data collection in the Cosmos DB account that you can query in later steps.

1. Navigate to your newly created Cosmos DB account.
2. On the **Overview** tab click the **+/Add Collection** button, and an "Add Collection" panel slides out.
3. Give the collection a database ID, collection ID, select a storage capacity, enter a partition key, enter a throughput value, then click **OK**.  For this tutorial, it is sufficient to use "Test" as the database ID and collection ID, select a fixed storage capacity and lowest throughput (400 RU/s).  

## Retrieve the `principalID` of the Linux VM's MSI

To gain access to the Cosmos DB account access keys from the Resource Manager in the following section, you need to retrieve the `principalID` of the Linux VM's MSI.  Be sure to replace the `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resource group in which you VM resides), and `<VM NAME>` parameter values with your own values.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
A válasz az alapértelmezett MSI (Megjegyzés: a principalID, mert használatban van a következő szakaszban) részleteit tartalmazza:

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>A Linux virtuális gép MSI hozzáférést biztosíthat a Cosmos DB tárelérési kulcsok

Cosmos DB natív módon támogatja az Azure AD-alapú hitelesítés.  Azonban egy olyan MSI Csomaghoz segítségével lekérni egy Cosmos DB hozzáférési kulcsot a Resource Manager, majd a kulcs Cosmos-adatbázis eléréséhez használja.  Ebben a lépésben megadta a rendszer MSI hozzáféréssel rendelkező, a kulcsokhoz, a Cosmos DB-fiókhoz.

A Cosmos DB-fiókba az Azure Resource Manager az Azure parancssori felület használatával MSI identitás hozzáférést engedélyez, frissítés a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` alkalmaz környezetében. Cserélje le `<MSI PRINCIPALID>` rendelkező a `principalId` tulajdonság által visszaadott a `az resource show` parancsot [beolvasni a Linux virtuális gép MSI a principalID](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB két részletességi támogatja a tárelérési kulcsok használata esetén: olvasási/írási hozzáfér a fiókjához, és olvasási hozzáférést ahhoz a fiókhoz.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkör, ha azt szeretné, hogy a fiók olvasási/írási kulcsokat, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkör, ha le szeretné kérdezni a fiók csak olvasható kulcsok:

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

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot a Linux virtuális gép MSI-fájl használatával, és hívja az Azure Resource Manager használatával

Az oktatóanyag a hátralévő működik a korábban létrehozott virtuális gépről.

Lépések elvégzéséhez szüksége van egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/install_guide). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](../../virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Linux virtuális gépet, majd a a **áttekintése** kattintson **Connect** tetején. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik. 
2. Csatlakoztassa a virtuális Gépet az SSH-ügyfél használatával.  
3. A következő kéri be a **jelszó** létrehozásakor hozzáadta a **Linux virtuális gép**. Meg kell majd lehet sikeres volt.  
4. CURL használatával szerezze be a hozzáférési tokent az Azure Resource Manager: 
     
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true   
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

Mostantól a CURL használatával hívja az erőforrás-kezelőt a hozzáférési jogkivonat lekérése az előző szakaszban a Cosmos DB hívóbetűre beolvasása. Ha a hozzáférési kulcs van, azt Cosmos DB lekérdezheti. Ügyeljen arra, hogy cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<COSMOS DB ACCOUNT NAME>` paraméterértékeket a saját értékekkel. Cserélje le a `<ACCESS TOKEN>` érték és a korábban kapott hozzáférési jogkivonat.  Ha lekéréséhez olvasási/írási kulcsok, kulcs művelet típust használjon `listKeys`.  Ha szeretné letölteni a csak olvasható kulcsokat, a kulcs művelet típust használjon `readonlykeys`:

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

- MSI áttekintését lásd: [felügyelt szolgáltatás identitásának (MSI) az Azure-erőforrások](overview.md).

