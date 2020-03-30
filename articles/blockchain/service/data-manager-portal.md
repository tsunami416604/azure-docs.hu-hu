---
title: A Blockchain Data Manager konfigurálása az Azure Portal használatával - Azure Blockchain Szolgáltatás
description: Az Azure Portalon keresztül létrehozhatja és kezelheti a Blockchain Data Manager for Azure Blockchain Szolgáltatást.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 03c22a7a23f1579a846746f21ce048b3425399c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273162"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>A Blockchain Data Manager konfigurálása az Azure Portallal

Konfigurálja a Blockchain Data Manager for Azure Blockchain Service-t a blokklánc-adatok rögzítéséhez és elküldéséhez egy Azure Event Grid topic-témakörbe.

A Blockchain Data Manager-példány konfigurálásához:

* Blockchain Data Manager-példány létrehozása egy Azure Blockchain Szolgáltatás tranzakciócsomóponthoz
* A blokklánc-alkalmazások hozzáadása

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: Blockchain-tag létrehozása az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service Blockchain-tagot az Azure CLI használatával](create-member-cli.md)
* [Eseményrács témakörének](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* Információ [az Eseménykezelőkről az Azure Event Gridben](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Példány létrehozása

A Blockchain Data Manager-példány csatlakozik és figyel egy Azure Blockchain Service tranzakciós csomópontot. Csak a tranzakciócsomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre kapcsolatot. Egy példány rögzíti az összes nyers blokk és nyers tranzakciós adatokat a tranzakciócsomópont.

A kimenő kapcsolat blockchain adatokat küld az Azure Event Grid. Egyetlen kimenő kapcsolatot konfigurál, amikor létrehozza a példányt. A Blockchain Data Manager több Eseményrács-témakör kimenő kapcsolatot támogat bármely adott Blockchain Data Manager-példányhoz. Blockchain adatokat küldhet egyetlen célhelyre, vagy küldhet blokklánc-adatokat több célhelyre. Másik cél hozzáadásához csak adjon hozzá további kimenő kapcsolatokat a példányhoz.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az Azure Blockchain Service-tagot, amelyhez csatlakozni szeretne a Blockchain Data Managerhez. Válassza a **Blockchain Data Manager**lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain-adatkezelő hozzáadása](./media/data-manager-portal/add-instance.png)

    Adja meg a következő részleteket:

    Beállítás | Leírás
    --------|------------
    Név | Adjon meg egy csatlakoztatott Blockchain Data Manager egyedi nevét. A Blockchain Data Manager neve kisbetűket és számokat tartalmazhat, és legfeljebb 20 karakter hosszú.
    Tranzakciócsomópont | Válasszon egy tranzakciócsomópontot. Csak az olvasási hozzáféréssel rendelkező tranzakciócsomópontok jelennek meg.
    Kapcsolat neve | Adja meg annak a kimenő kapcsolatnak az egyedi nevét, amelybe a blokklánc-tranzakció adatait küldi el.
    Eseményrács végpontja | Válasszon egy eseményrács-témakört ugyanabban az előfizetésben, mint a Blockchain Data Manager-példány.

1. Válassza **az OK gombot.**

    A Blockchain Data Manager-példány létrehozása kevesebb mint egy percet vesz igénybe. A példány üzembe helyezése után automatikusan elindul. A futó Blockchain Data Manager-példány rögzíti a blokklánc-eseményeket a tranzakciócsomópontról, és adatokat küld a kimenő kapcsolatoknak.

    Az új példány megjelenik az Azure Blockchain Service-tag Blockchain Data Manager-példányainak listájában.

    ![A Blockchain Data Member példányainak listája](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Blockchain alkalmazás hozzáadása

Ha hozzáad egy blockchain alkalmazást, a Blockchain Data Manager dekódolja az alkalmazás esemény- és tulajdonságállapotát. Ellenkező esetben csak a nyers blokk és a nyers tranzakciós adatok küldése. A Blockchain Data Manager a szerződéscímek üzembe helyezésekor is felderíti a szerződéscímeket. Több blockchain alkalmazást is hozzáadhat egy Blockchain Data Manager-példányhoz.

> [!IMPORTANT]
> Jelenleg a Solidity [tömbtípusokat](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) deklaráló blokklánc-alkalmazások nem támogatottak teljes mértékben. A tömb- vagy leképezéstípusként deklarált tulajdonságok nem lesznek dekódolva *a ContractPropertiesMsg* vagy *a DecodedContractEventsMsg* üzenetekben.

A Blockchain Data Manager intelligens szerződésa ABI-t igényel, és az alkalmazás hozzáadásához üzembe helyezte bytecode fájlt.

### <a name="get-contract-abi-and-bytecode"></a>Szerződés ABI és bytecode bekéselése

A szerződés ABI határozza meg az intelligens szerződés interfészek. Leírja, hogyan lehet kölcsönhatásba lépni az intelligens szerződéssel. Az Azure [Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) segítségével másolja a szerződés ABI a vágólapra.

1. A Visual Studio Kódkezelő ablaktábláján bontsa ki a Solidity projekt **build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Szerződés ABI másolása lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Szerződés másolása ABI-kijelöléssel](./media/data-manager-portal/abi-devkit.png)

    A szerződés ABI másolja a vágólapra.

1. Mentse az **abi** tömböt JSON fájlként. Például *abi.json*. A fájlt egy későbbi lépésben használhatja.

A Blockchain Data Manager megköveteli az intelligens szerződés üzembe helyezett bytecode-ot. Az üzembe helyezett bytecode eltér az intelligens szerződés bytecode. Az Azure blockchain fejlesztői csomag bővítmény használatával másolja a bytecode a vágólapra.

1. A Visual Studio Kódkezelő ablaktábláján bontsa ki a Solidity projekt **build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Tranzakcióbájtmásolási bájkód lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Tranzakcióbájkód másolása beállítással](./media/data-manager-portal/bytecode-devkit.png)

    A bytecode a vágólapra kerül.

1. Mentse a **bytecode** értéket JSON-fájlként. Például *bytecode.json*. A fájlt egy későbbi lépésben használhatja.

A következő példa a VS Code szerkesztőben megnyitott *abi.json* és *bytecode.json* fájlokat mutatja be. A fájloknak hasonlónak kell látszaniuk.

![Példa abi.json és bytecode.json fájlokra](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Szerződés ABI- és bytecode URL-címének létrehozása

A Blockchain Data Manager megköveteli, hogy a szerződés ABI és bytecode fájlok url-címből elérhetők legyenek az alkalmazás hozzáadásakor. Az Azure Storage-fiók használatával privát an-hetedi url-címet adhat meg.

#### <a name="create-storage-account"></a>Storage-fiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Szerződésfájlok feltöltése

1. Hozzon létre egy új tárolót a tárfiókhoz. Válassza **a Tárolók > a Tároló lehetőséget.**

    ![Tárfiók-tároló létrehozása](./media/data-manager-portal/create-container.png)

    | Mező | Leírás |
    |-------|-------------|
    | Név  | Nevezze el a tárolót. Például az *intelligens* |
    | Nyilvános hozzáférés szintje | Válassza a *Privát lehetőséget (nincs névtelen hozzáférés)* |

1. A tároló létrehozásához válassza az **OK** gombot.
1. Jelölje ki a tárolót, majd válassza **a Feltöltés**lehetőséget.
1. Válassza ki mindkét létrehozott JSON-fájlt a Szerződés ABI és a [bytecode leválasztása](#get-contract-abi-and-bytecode) szakaszban.

    ![Blob feltöltése](./media/data-manager-portal/upload-blobs.png)

    Válassza a **Feltöltés** lehetőséget.

#### <a name="generate-url"></a>URL létrehozása

Minden blobhoz hozzon létre egy közös hozzáférési aláírást.

1. Válassza ki az ABI JSON blobot.
1. SAS **létrehozása jelölőnégyzetet**
1. Állítsa be a kívánt hozzáférési aláírás elévülését, majd válassza **a Blob SAS-token és URL létrehozása**lehetőséget.

    ![SAS-jogkivonat létrehozása](./media/data-manager-portal/generate-sas.png)

1. Másolja a **Blob SAS URL-címét,** és mentse a következő szakaszra.
1. Ismételje meg az [URL-generálása](#generate-url) lépéseket a bytecode JSON blob.

### <a name="add-application-to-instance"></a>Alkalmazás hozzáadása a példányhoz

1. Válassza ki a Blockchain Data Manager-példányt a példánylistából.
1. Válassza a **Blockchain alkalmazások lehetőséget.**
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain alkalmazás hozzáadása](./media/data-manager-portal/add-application.png)

    Adja meg a blockchain alkalmazás nevét és az intelligens szerződés ABI és bytecode URL-eket.

    Beállítás | Leírás
    --------|------------
    Név | Adja meg a nyomon követendő blockchain alkalmazás egyedi nevét.
    Szerződés ABI | A Szerződés ABI-fájl URL-elérési útja. További információ: [Szerződés létrehozása ABI és bytecode URL.For more information, see Create contract ABI and bytecode URL.](#create-contract-abi-and-bytecode-url)
    Szerződés bytecode | A bytecode fájl URL-elérési útja. További információ: [Szerződés létrehozása ABI és bytecode URL.For more information, see Create contract ABI and bytecode URL.](#create-contract-abi-and-bytecode-url)

1. Válassza **az OK gombot.**

    Az alkalmazás létrehozása után az alkalmazás megjelenik a blockchain alkalmazások listájában.

    ![Blockchain alkalmazáslista](./media/data-manager-portal/artifact-list.png)

Törölheti az Azure Storage-fiókot, vagy használhatja több blokklánc-alkalmazás konfigurálásához. Ha törölni szeretné az Azure Storage-fiókot, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

## <a name="stop-instance"></a>Leállítási példány

Állítsa le a Blockchain Manager-példányt, ha le szeretné állítani a blokklánc-események rögzítését és a kimenő kapcsolatokra történő adatküldést. A példány leállításakor a Blockchain Data Manager nem számít fel díjat. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/blockchain-service) tartalmaz.

1. Nyissa meg **az Áttekintés lehetőséget,** és válassza **a Leállítás**lehetőséget.

    ![Leállítási példány](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>További lépések

Próbálja ki a következő oktatóanyag létrehozása blockchain tranzakció üzenetkezelő segítségével Blockchain Data Manager és az Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Adatok küldése az Azure Cosmos DB-be a Blockchain Data Managerrel](data-manager-cosmosdb.md)