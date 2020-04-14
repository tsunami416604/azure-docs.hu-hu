---
title: A Blockchain Data Manager konfigurálása az Azure CLI - Azure Blockchain szolgáltatás használatával
description: Blockchain Data Manager azure blockchain szolgáltatás létrehozása és kezelése az Azure CLI használatával
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: e490803fabeed7d6234bd6984acbfb9f5270e0c0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254410"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>A Blockchain Data Manager konfigurálása az Azure CLI-vel

Konfigurálja a Blockchain Data Manager for Azure Blockchain Service-t, hogy rögzítse a blokklánc-adatokat egy Azure Event Grid topic-nak.

A Blockchain Data Manager-példány konfigurálásához:

* Blockchain Manager-példány létrehozása
* Bevitel létrehozása egy Azure Blockchain Szolgáltatás tranzakciócsomóponthoz
* Kimenet létrehozása Azure Event Grid-témakörhöz
* Blockchain alkalmazás hozzáadása
* Példány indítása

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a legújabb [Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli) és a használatával jelentkezett `az login`be.
* Teljes [rövid útmutató: A Visual Studio-kód használatával csatlakozhat egy Azure Blockchain Service konzorciumi hálózathoz.](connect-vscode.md) Az Azure Blockchain Service *Standard* tier használata esetén ajánlott blockchain data manager használata.
* [Eseményrács témakörének](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* Információ [az Eseménykezelőkről az Azure Event Gridben](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a CLI helyi telepítését és használatát szeretné használni, ez a rövid útmutató az Azure CLI 2.0.51-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Példány létrehozása

A Blockchain Data Manager-példány egy Azure Blockchain Service tranzakciós csomópontot figyel. Egy példány rögzíti az összes nyers blokk és nyers tranzakciós adatokat a tranzakciócsomópont. Blockchain Data Manager közzéteszi **a RawBlockAndTransactionMsg** üzenetet, amely egy szuperhalmaza visszaad web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) és [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) lekérdezések.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Erőforráscsoport neve, ahol létre kell hozni a Blockchain Data Manager példányt. |
| név | A Blockchain Data Manager-példány neve. |
| erőforrás-típus | A Blockchain Data Manager-példány erőforrástípusa a **Microsoft.blockchain/watchers.** |
| teljes objektum | Azt jelzi, hogy a tulajdonságok a figyelő erőforrás beállításait tartalmazzák. |
| properties | JSON-formátumú karakterlánc, amely a figyelő erőforrás tulajdonságait tartalmazza. Karakterláncként vagy fájlként is átadható.  |

### <a name="create-instance-examples"></a>Példák létrehozása

JSON konfigurációs példa egy Blockchain Manager-példány létrehozásához az **USA keleti régiójában.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol létre kell hozni a figyelő erőforrást |
| properties | A figyelő erőforrás létrehozásakor beállítandó tulajdonságok |

Hozzon létre egy *mywatcher* nevű Blockchain Data Manager-példányt egy JSON-karakterlánc használatával a konfigurációhoz.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Hozzon létre egy *MyWatcher* nevű Blockchain Data Manager-példányt JSON konfigurációs fájl használatával.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Bemenet létrehozása

A bemeneti adatok a Blockchain Data Managert egy Azure Blockchain Service tranzakciós csomóponthoz kapcsolják. Csak a tranzakciócsomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre kapcsolatot.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Erőforráscsoport neve, ahol létre kell hozni a bemeneti erőforrást. |
| név | A bemenet neve. |
| névtér | Használja a **Microsoft.Blockchain** szolgáltató névterét. |
| erőforrás-típus | A Blockchain Data Manager bemeneterőforrás-típusa **bemenet.** |
| Szülő | Annak a figyelőnek az elérési útja, amelyhez a bemenet társítva van. Például **a figyelők / mywatcher**. |
| teljes objektum | Azt jelzi, hogy a tulajdonságok a bemeneti erőforrás beállításait tartalmazzák. |
| properties | JSON-formátumú karakterlánc, amely a bemeneti erőforrás tulajdonságait tartalmazza. Karakterláncként vagy fájlként is átadható. |

### <a name="input-examples"></a>Példák beviteli példák

Konfigurációs JSON példa egy bemeneti erőforrás létrehozásához \<az\>USA *keleti régiójában,* amely a Blockchain taghoz kapcsolódik.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol létre kell hozni a bemeneti erőforrást. |
| inputType (beviteli típus) | Az Azure Blockchain service tag főkönyvi típusa. Jelenleg **az Ethereum** támogatott. |
| resourceId | Az a tranzakciós csomópont, amelyhez a bemenet csatlakozik. Cserélje \<le\>az \<Előfizetés-azonosítót , az Erőforráscsoportot\>és \<a Blokklánc-tagot\> a tranzakciócsomópont-erőforrás értékeire. A bemeneti adatok az Azure Blockchain service-tag alapértelmezett tranzakciócsomópontjához csatlakoznak. |

Hozzon létre egy *myInput* nevű bemenetet a *mywatcher* számára egy JSON-karakterlánc használatával a konfigurációhoz.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Hozzon létre egy *myWatcher* nevű *bemenetet* egy JSON konfigurációs fájl használatával.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Kimenet létrehozása

A kimenő kapcsolat blockchain adatokat küld az Azure Event Grid. Blockchain adatokat küldhet egyetlen célhelyre, vagy küldhet blokklánc-adatokat több célhelyre. A Blockchain Data Manager több Eseményrács-témakör kimenő kapcsolatot támogat bármely adott Blockchain Data Manager-példányhoz.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Erőforráscsoport neve, ahol létre kell hozni a kimeneti erőforrást. |
| név | A kimenet neve. |
| névtér | Használja a **Microsoft.Blockchain** szolgáltató névterét. |
| erőforrás-típus | A Blockchain Data Manager kimeneterőforrás-típusa **kimenet.** |
| Szülő | Annak a figyelőnek az elérési útja, amelyhez a kimenet társítva van. Például **a figyelők / mywatcher**. |
| teljes objektum | Azt jelzi, hogy a tulajdonságok a kimeneti erőforrás beállításait tartalmazzák. |
| properties | JSON-formátumú karakterlánc, amely a kimeneti erőforrás tulajdonságait tartalmazza. Karakterláncként vagy fájlként is átadható. |

### <a name="output-examples"></a>Kimeneti példák

Konfigurációs JSON példa egy kimeneti erőforrás létrehozásához az *USA keleti régiójában,* amely egy eseményrács-témakör nevű \<eseményrács-témakörhöz\>kapcsolódik.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol létre kell hozni a kimeneti erőforrást. |
| kimenettípusa | A kimenet típusa. Jelenleg az **EventGrid** támogatott. |
| resourceId | Az az erőforrás, amelyhez a kimenet csatlakozik. Cserélje \<le\>az \<Előfizetés-azonosítót , az Erőforráscsoportot\>és \<a Blokklánc-tagot\> az eseményrács-erőforrás értékeire. |

Hozzon létre egy myoutput nevű *kimenetet* a *mywatcher* számára, amely egy JSON konfigurációs karakterlánc használatával kapcsolódik egy eseményrács-témakörhöz.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

*Hozzon* létre egy *mywatcher* nevű kimenetet, amely JSON konfigurációs fájl használatával kapcsolódik egy eseményrács-témakörhöz.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Blockchain alkalmazás hozzáadása

Ha hozzáad egy blockchain alkalmazást, a Blockchain Data Manager dekódolja az alkalmazás esemény- és tulajdonságállapotát. Ellenkező esetben csak a nyers blokk és a nyers tranzakciós adatok küldése. A Blockchain Data Manager a szerződéscímek üzembe helyezésekor is felderíti a szerződéscímeket. Több blockchain alkalmazást is hozzáadhat egy Blockchain Data Manager-példányhoz.


> [!IMPORTANT]
> Jelenleg a Solidity [tömbtípusokat](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) deklaráló blokklánc-alkalmazások nem támogatottak teljes mértékben. A tömb- vagy leképezéstípusként deklarált tulajdonságok nem lesznek dekódolva *a ContractPropertiesMsg* vagy *a DecodedContractEventsMsg* üzenetekben.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Erőforráscsoport neve, ahol létre kell hozni a kiegyenlítési erőforrást. |
| név | Az alkalmazás neve. |
| névtér | Használja a **Microsoft.Blockchain** szolgáltató névterét. |
| erőforrás-típus | A Blockchain Data Manager alkalmazás erőforrástípusa **műtermékek.** |
| Szülő | Annak a figyelőnek az elérési útja, amelyhez az alkalmazás társítva van. Például **a figyelők / mywatcher**. |
| teljes objektum | Azt jelzi, hogy a tulajdonságok az alkalmazás-erőforrás beállításait tartalmazzák. |
| properties | JSON-formátumú karakterlánc, amely az alkalmazáserőforrás tulajdonságait tartalmazza. Karakterláncként vagy fájlként is átadható. |

### <a name="blockchain-application-examples"></a>Példák a Blockchain alkalmazásra

Konfigurációs JSON példa egy alkalmazás-erőforrás létrehozásához az *USA keleti régiójában,* amely figyeli a szerződés ABI és bytecode által meghatározott intelligens szerződést.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol létre kell hoznia az alkalmazás-erőforrást. |
| artifactType típus | Az alkalmazás típusa. Jelenleg **az EthereumSmartContract** támogatott. |
| abiFileUrl | Az intelligens szerződés ABI JSON-fájl URL-címe. A szerződéses ABI beszerzéséről és az URL-cím létrehozásáról a [Szerződés ABI és bytecode beszerzése,](data-manager-portal.md#get-contract-abi-and-bytecode) valamint [ABI- és bytecode URL-cím létrehozása című témakörben](data-manager-portal.md#create-contract-abi-and-bytecode-url)talál további információt. |
| bytecodeFileUrl | Url az intelligens szerződés üzembe helyezett bytecode JSON fájlt. Az intelligens szerződés üzembe helyezett bytecode beszerzéséről és az URL-cím létrehozásáról a [Szerződés ABI és bytecode beszerzése,](data-manager-portal.md#get-contract-abi-and-bytecode) valamint [Szerződés létrehozása ABI és bytecode URL című témakörben](data-manager-portal.md#create-contract-abi-and-bytecode-url)talál további információt. Megjegyzés: A Blockchain Data Manager megköveteli a **telepített bytecode-ot.** |
| queryTargetTypes | Közzétett üzenettípusok. A **ContractProperties** megadása közzéteszi *a ContractPropertiesMsg üzenettípust.* A **ContractEvents** megadása közzéteszi a *DecodedContractEventsMsg* üzenettípusát. Megjegyzés: *A RawBlockAndTransactionMsg* és *a RawTransactionContractCreationMsg* üzenettípusok mindig közzé vannak téve. |

Hozzon létre egy *myApplication* nevű alkalmazást a *mywatcher* számára, amely egy JSON-karakterlánc által definiált intelligens egyezményt figyel.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Hozzon létre egy *myApplication* nevű alkalmazást a *mywatcher* számára, amely egy JSON konfigurációs fájl használatával definiált intelligens egyezményt figyel meg.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Kezdő példány

Futtatásakor a Blockchain Manager-példány figyeli a blokklánc-eseményeket a megadott bemenetekből, és adatokat küld a megadott kimenetek.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Paraméter | Leírás |
|-----------|-------------|
| action | Használja **az indítást** a figyelő futtatásához. |
| Azonosítók | Figyelő erőforrás-azonosítója. Cserélje \<le\>az \<Előfizetés-azonosítót , az Erőforrás csoportot\>és \<a Figyelő nevét\> a figyelő erőforrás értékeire.|

### <a name="start-instance-example"></a>Példa a kezdőpéldányra

Indítsa el a *MyWatcher*nevű Blockchain Data Manager-példányt.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Leállítási példány

Blockchain Data Manager-példány leállítása.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Paraméter | Leírás |
|-----------|-------------|
| action | Használja **stop** megállítani a figyelőt. |
| Azonosítók | A figyelő neve. Cserélje \<le\>az \<Előfizetés-azonosítót , az Erőforrás csoportot\>és \<a Figyelő nevét\> a figyelő erőforrás értékeire. |

### <a name="stop-watcher-example"></a>Példa a stopfigyelőre

A *mywatcher*nevű példány leállítása.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Példány törlése

Blockchain Data Manager-példány törlése.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | A törlendő figyelő erőforráscsoport neve. |
| név | A törlendő figyelő neve. |
| erőforrás-típus | A Blockchain Data Manager figyelőjének erőforrástípusa a **Microsoft.blockchain/watchers.** |

### <a name="delete-instance-example"></a>Példa a példány törlése

Töröljön egy *mywatcher* nevű példányt a *myRG* erőforráscsoportból.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>További lépések

Próbálja ki a következő oktatóanyag létrehozása blockchain tranzakció üzenetkezelő segítségével Blockchain Data Manager és az Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Adatok küldése az Azure Cosmos DB-be a Blockchain Data Managerrel](data-manager-cosmosdb.md)
