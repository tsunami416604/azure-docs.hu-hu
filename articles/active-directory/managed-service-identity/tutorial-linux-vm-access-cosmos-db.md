---
title: Az Azure Cosmos DB elérése Linux VM-beli felügyeltszolgáltatás-identitással
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Cosmos DB-hez egy Linux VM-beli, rendszer által hozzárendelt felügyeltszolgáltatás-identitással.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: af148cd8b3eececb258057a8bf6a78216ec0e50a
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258330"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-cosmos-db"></a>Oktatóanyag: Az Azure Cosmos DB elérése Linux VM-beli felügyeltszolgáltatás-identitással 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Ez az oktatóanyag bemutatja, hogyan hozhat létre és használhat Linux VM-beli felügyeltszolgáltatás-identitást. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása a következő engedélyezésével:
> * Cosmos DB-fiók létrehozása
> * Gyűjtemény létrehozása Cosmos DB-fiókban
> * Felügyeltszolgáltatás-identitás hozzáférésének megadása Azure Cosmos DB-példány számára
> * A Linux VM-beli felügyeltszolgáltatás-identitás `principalID` paraméterének lekérése
> * Hozzáférési jogkivonat lekérése, majd azzal az Azure Resource Manager meghívása
> * Hozzáférési kulcsok lekérése az Azure Resource Managerből Cosmos DB-hívások indításához

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com).

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

A jelen oktatóanyagban a parancssori példaszkriptek futtatása kétféleképpen végezhető el:

- Az [Azure Cloud Shell](~/articles/cloud-shell/overview.md) használatával az Azure Portalon vagy a minden egyes kódblokk jobb felső sarkában megtalálható **Kipróbálom** gombbal.
- Ha inkább a helyi parancssori konzolt kívánja használni, [telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása új erőforráscsoportban

Ebben az oktatóanyagban egy új, a felügyeltszolgáltatás-identitással kompatibilis Linux rendszerű virtuális gépet hozunk létre.

A felügyeltszolgáltatás-identitással kompatibilis virtuális gép létrehozása:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. A használni kívánt fiók azon Azure-előfizetéshez legyen hozzárendelve, amely alatt üzembe fogja helyezni a virtuális gépet:

   ```azurecli-interactive
   az login
   ```

2. Az [az group create](/cli/azure/group/#az_group_create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az_vm_create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre egy felügyeltszolgáltatás-identitással, az `--assign-identity` paraméter által kérelmezett módon. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

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

## <a name="retrieve-the-principalid-of-the-linux-vms-managed-service-identity"></a>A Linux VM-beli felügyeltszolgáltatás-identitás `principalID` paraméterének lekérése

Ahhoz, hogy az ezt követő szakaszban a Resource Managerből is hozzáférhessen a Cosmos DB-fiók hozzáférési kulcsaihoz, le kell kérdeznie a Linux VM-beli felügyeltszolgáltatás-identitás `principalID` paraméterét.  Ne felejtse el a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (az az erőforráscsoport, amelyben a virtuális gép megtalálható) és `<VM NAME>` paraméterek értékeit a saját értékeire lecserélni.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
A válasz tartalmazza a rendszer által hozzárendelt felügyeltszolgáltatás-identitás részleteit (jegyezze fel a PrincipalId azonosító értékét, mivel a következő szakaszban használni fogja):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-managed-service-identity-access-to-the-cosmos-db-account-access-keys"></a>Linux VM felügyeltszolgáltatás-identitás hozzáférésének biztosítása a Cosmos DB-fiók hozzáférési kulcsaihoz

A Cosmos DB nem támogatja natív módon az Azure AD-hitelesítést. A felügyeltszolgáltatás-identitás használatával azonban lekérheti a Cosmos DB hozzáférési kulcsát a Resource Managerből, és azzal elérheti a Cosmos DB-t. Ebben a lépésben hozzáférést biztosít a felügyeltszolgáltatás-identitás számára a Cosmos DB-fiók kulcsaihoz.

Ha az Azure CLI használatával kíván hozzáférést adni a felügyeltszolgáltatás-identitás számára a Cosmos DB-fiókhoz az Azure Resource Managerben, frissítse a környezet `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` és `<COSMOS DB ACCOUNT NAME>` értékeit. Cserélje le az `<MSI PRINCIPALID>` elemet a [Linux VM MSI PrincipalId azonosítójának lekérésével kapcsolatos szakaszban](#retrieve-the-principalID-of-the-linux-VM's-MSI) a `az resource show` által visszaadott `principalId` tulajdonságra.  A Cosmos DB a részletesség két szintjét támogatja a hozzáférési kulcsok használatakor: a fiók írási/olvasási, illetve írásvédett hozzáférését.  Rendelje hozzá a `DocumentDB Account Contributor` szerepkört, ha a fiók írási/olvasási kulcsait szeretné lekérni, vagy rendelje hozzá a `Cosmos DB Account Reader Role` szerepkört, ha írásvédett hozzáférést szeretne a fiókhoz:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

A válasz tartalmazza a létrehozott szerepkör-hozzárendelés részleteit:

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

## <a name="get-an-access-token-using-the-linux-vms-managed-service-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a Linux rendszerű virtuális gép felügyeltszolgáltatás-identitásának használatával, majd az Azure Resource Manager meghívása a jogkivonat használatával

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk majd.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](https://msdn.microsoft.com/commandline/wsl/install_guide) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. Az Azure Portalon lépjen a **Virtuális gépek** felületre, keresse meg a Linux virtuális gépet, majd az **Áttekintés** lap tetején kattintson a **Csatlakozás** gombra. Másolja ki sztringet a virtuális géphez való csatlakozáshoz. 
2. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával.  
3. Ezután meg kell adnia majd a **Linux VM** létrehozásakor hozzáadott **Jelszót**. Ezzel sikeresen be kell tudnia jelentkezni.  
4. A CURL használatával kérjen le egy hozzáférési jogkivonatot az Azure Resource Manager számára: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > Az előző kérésben a „resource” (erőforrás) paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    > A következő válaszban az access_token elemet a helytakarékosság miatt rövidítve jelenik meg.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hozzáférési kulcsok lekérése az Azure Resource Managerből Cosmos DB-hívások indításához  

Most CURL használatával hívjuk meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal a Cosmos DB-fiók hozzáférési kulcsának lekéréséhez. Amint megkaptuk a hozzáférési kulcsot, a Cosmos DB lekérdezhetővé válik. A `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` és `<COSMOS DB ACCOUNT NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. Az `<ACCESS TOKEN>` paraméter értékét cserélje le a korábban lekért hozzáférési jogkivonattal.  Ha olvasási/írási kulcsokat szeretne lekérni, akkor `listKeys` típusú kulcsműveleteket használjon.  Ha írásvédett kulcsokat kíván lekérni, akkor pedig `readonlykeys` típusúakat:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Az előző URL-cím szövege megkülönbözteti a kis- és nagybetűket, ezért az erőforráscsoportok esetében is különböztesse meg ezeket a megfelelő hivatkozás érdekében. Ezenkívül fontos észben tartani, hogy ez egy POST és nem egy GET kérés, és a -d paraméterrel átadhat egy értéket a maximális hossz korlátozására (lehet NULL értékű is).  

A CURL-válasz visszaadja a kulcsok listáját:  Ha például írásvédett kulcsokat kap:  

```bash 
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

Az oktatóanyag bemutatta, hogyan használhat felügyeltszolgáltatás-identitásokat Linux virtuális gépeken a Cosmos DB eléréséhez.  További információ a Cosmos DB-ről:

> [!div class="nextstepaction"]
>[Az Azure Cosmos DB áttekintése](/azure/cosmos-db/introduction)

