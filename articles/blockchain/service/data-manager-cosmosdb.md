---
title: Az Azure Cosmos DB frissítése a Blockchain Data Manager használatával – Azure Blockchain szolgáltatás
description: Blockchain Data Manager for Azure Blockchain Service segítségével blockchain adatokat küldhet az Azure Cosmos DB-nek
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533181"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Oktatóanyag: Adatok küldése az Azure Cosmos DB-nek a Blockchain Data Manager használatával

Ebben az oktatóanyagban a Blockchain Data Manager for Azure Blockchain Service használatával rögzítheti a blokklánc-tranzakciós adatokat az Azure Cosmos DB-ben. A Blockchain Data Manager rögzíti, átalakítja és kézbesíti a blokklánc főkönyvi adatait az Azure Event Grid-témakörökszámára. Az Azure Event Grid ből egy Azure Logic App-összekötő használatával hozhat létre dokumentumokat egy Azure Cosmos DB-adatbázisban. Ha befejezte az oktatóanyag, felfedezheti blockchain tranzakciós adatok az Azure Cosmos DB Data Explorer.

[![Blockchain tranzakció részletei](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Blockchain adatkezelő példány létrehozása
> * Blockchain alkalmazás hozzáadása a tranzakció tulajdonságainak és eseményeinek dekódolásához
> * Hozzon létre egy Azure Cosmos DB-fiókot és adatbázist a tranzakciós adatok tárolásához
> * Hozzon létre egy Azure Logic Alkalmazást az Azure Event Grid-témakör és az Azure Cosmos DB csatlakoztatásához
> * Tranzakció küldése blokklánc-főkönyvbe
> * A dekódolt tranzakciós adatok megtekintése az Azure Cosmos DB-ben

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: Blockchain-tag létrehozása az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service Blockchain-tagot az Azure CLI használatával](create-member-cli.md)
* Teljes [rövid útmutató: A Visual Studio-kód használatával csatlakozhat egy Azure Blockchain Service konzorciumi hálózathoz.](connect-vscode.md) A rövid útmutatók segítségével telepítheti az [Azure Blockchain Development Kit for Ethereum-ot,](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) és üzembe helyezheti a blokklánc-fejlesztési környezetet.
* Teljes [oktatóanyag: Intelligens szerződések létrehozásához, összeállításához és üzembe helyezéséhez használja a Visual Studio-kódot.](send-transaction.md) Az oktatóanyag végigvezeti a minta intelligens szerződés létrehozásán.
* [Eseményrács témakörének](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* Információ [az Eseménykezelőkről az Azure Event Gridben](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Példány létrehozása

A Blockchain Data Manager-példány csatlakozik és figyel egy Azure Blockchain Service tranzakciós csomópontot. Egy példány rögzíti az összes nyers blokk és nyers tranzakciós adatokat a tranzakciócsomópont. A kimenő kapcsolat blockchain adatokat küld az Azure Event Grid. Egyetlen kimenő kapcsolatot konfigurál, amikor létrehozza a példányt.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az Azure Blockchain Szolgáltatás által létrehozott, az előfeltételként [létrehozott gyorsútmutatóban létrehozott tagot: Hozzon létre egy blokklánc-tagot az Azure Portalon](create-member.md)keresztül. Válassza a **Blockchain Data Manager**lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain-adatkezelő hozzáadása](./media/data-manager-cosmosdb/add-instance.png)

    Adja meg a következő részleteket:

    Beállítás | Példa | Leírás
    --------|---------|------------
    Név | mywatcher | Adjon meg egy csatlakoztatott Blockchain Data Manager egyedi nevét.
    Tranzakciócsomópont | saját blockchainmember | Válassza ki az előfeltételben létrehozott Azure Blockchain Szolgáltatás tag alapértelmezett tranzakciócsomópontját.
    Kapcsolat neve | kozmoszdb | Adja meg annak a kimenő kapcsolatnak az egyedi nevét, amelybe a blokklánc-tranzakció adatait küldi el.
    Eseményrács végpontja | myTopic | Válassza ki az előfeltételben létrehozott eseményrács-témakört. Megjegyzés: A Blockchain Data Manager-példánynak és az eseményrács témakörének ugyanabban az előfizetésben kell lennie.

1. Válassza **az OK gombot.**

    A Blockchain Data Manager-példány létrehozása kevesebb mint egy percet vesz igénybe. A példány üzembe helyezése után automatikusan elindul. A futó Blockchain Data Manager-példány rögzíti a blokklánc-eseményeket a tranzakciócsomópontról, és adatokat küld az eseményrácsnak.

## <a name="add-application"></a>Alkalmazás hozzáadása

Adja hozzá a **helloblockchain** blockchain alkalmazást, hogy a Blockchain Data Manager dekódolja az eseményt és a tulajdonságállapotot. A Blockchain Data Manager az alkalmazás hozzáadásához az intelligens szerződés ABI és bytecode fájlra van szükség.

### <a name="get-contract-abi-and-bytecode"></a>Szerződés ABI és bytecode beszerezni

A szerződés ABI határozza meg az intelligens szerződés interfészek. Leírja, hogyan lehet kölcsönhatásba lépni az intelligens szerződéssel. Az Azure [Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) segítségével másolja a szerződés ABI a vágólapra.

1. A Visual Studio Kódkezelő ablaktáblájában bontsa ki az előfeltételként létrehozott **helloblockchain** Solidity projekt **build/szerződések** mappáját: [Intelligens szerződések létrehozásához, létrehozásához és üzembe helyezéséhez használja a Visual Studio-kódot.](send-transaction.md)
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Szerződés ABI másolása lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Szerződés másolása ABI-kijelöléssel](./media/data-manager-cosmosdb/abi-devkit.png)

    A szerződés ABI másolja a vágólapra.

1. Mentse az **abi** tömböt JSON fájlként. Például *abi.json*. A fájlt egy későbbi lépésben használhatja.

A Blockchain Data Manager megköveteli az intelligens szerződés üzembe helyezett bytecode-ot. Az üzembe helyezett bytecode eltér az intelligens szerződés bytecode. Az Azure blockchain fejlesztői csomag bővítmény használatával másolja a bytecode a vágólapra.

1. A Visual Studio Kódkezelő ablaktábláján bontsa ki a Solidity projekt **build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadat-jsonfájljára. A fájlnév az intelligens szerződés neve, amelyet a **.json** kiterjesztés követ.
1. Válassza **a Tranzakcióbájtmásolási bájkód lehetőséget.**

    ![A Visual Studio Kód ablaktáblája a Tranzakcióbájkód másolása beállítással](./media/data-manager-cosmosdb/bytecode-devkit.png)

    A bytecode a vágólapra kerül.

1. Mentse a **bytecode** értéket JSON-fájlként. Például *bytecode.json*. A fájlt egy későbbi lépésben használhatja.

A következő példa a VS Code szerkesztőben megnyitott *abi.json* és *bytecode.json* fájlokat mutatja be. A fájloknak hasonlónak kell látszaniuk.

![Példa abi.json és bytecode.json fájlokra](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Szerződés ABI- és bytecode URL-címének létrehozása

A Blockchain Data Manager megköveteli, hogy a szerződés ABI és bytecode fájlok url-címből elérhetők legyenek az alkalmazás hozzáadásakor. Az Azure Storage-fiók használatával privát an-hetedi url-címet adhat meg.

#### <a name="create-storage-account"></a>Storage-fiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Szerződésfájlok feltöltése

1. Hozzon létre egy új tárolót a tárfiókhoz. Válassza **a Tárolók > a Tároló lehetőséget.**

    ![Tárfiók-tároló létrehozása](./media/data-manager-cosmosdb/create-container.png)

    | Beállítás | Leírás |
    |---------|-------------|
    | Név  | Nevezze el a tárolót. Például az *intelligens* |
    | Nyilvános hozzáférés szintje | Válassza a *Privát lehetőséget (nincs névtelen hozzáférés)* |

1. A tároló létrehozásához válassza az **OK** gombot.
1. Jelölje ki a tárolót, majd válassza **a Feltöltés**lehetőséget.
1. Válassza ki mindkét létrehozott JSON-fájlt a Szerződés ABI és a [bytecode leválasztása](#get-contract-abi-and-bytecode) szakaszban.

    ![Blob feltöltése](./media/data-manager-cosmosdb/upload-blobs.png)

    Válassza a **Feltöltés** lehetőséget.

#### <a name="generate-url"></a>URL létrehozása

Minden blobhoz hozzon létre egy közös hozzáférési aláírást.

1. Válassza ki az ABI JSON blobot.
1. SAS **létrehozása jelölőnégyzetet**
1. Állítsa be a kívánt hozzáférési aláírás elévülését, majd válassza **a Blob SAS-token és URL létrehozása**lehetőséget.

    ![SAS-jogkivonat létrehozása](./media/data-manager-cosmosdb/generate-sas.png)

1. Másolja a **Blob SAS URL-címét,** és mentse a következő szakaszra.
1. Ismételje meg az [URL-generálása](#generate-url) lépéseket a bytecode JSON blob.

### <a name="add-helloblockchain-application-to-instance"></a>Helloblockchain alkalmazás hozzáadása a példányhoz

1. Válassza ki a Blockchain Data Manager-példányt a példánylistából.
1. Válassza a **Blockchain alkalmazások lehetőséget.**
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain alkalmazás hozzáadása](./media/data-manager-cosmosdb/add-application.png)

    Adja meg a blockchain alkalmazás nevét és az intelligens szerződés ABI és bytecode URL-eket.

    Beállítás | Leírás
    --------|------------
    Név | Adja meg a nyomon követendő blockchain alkalmazás egyedi nevét.
    Szerződés ABI | A Szerződés ABI-fájl URL-elérési útja. További információ: [Szerződés létrehozása ABI és bytecode URL.For more information, see Create contract ABI and bytecode URL.](#create-contract-abi-and-bytecode-url)
    Szerződés bytecode | A bytecode fájl URL-elérési útja. További információ: [Szerződés létrehozása ABI és bytecode URL.For more information, see Create contract ABI and bytecode URL.](#create-contract-abi-and-bytecode-url)

1. Válassza **az OK gombot.**

    Az alkalmazás létrehozása után az alkalmazás megjelenik a blockchain alkalmazások listájában.

    ![Blockchain alkalmazáslista](./media/data-manager-cosmosdb/artifact-list.png)

Törölheti az Azure Storage-fiókot, vagy használhatja több blokklánc-alkalmazás konfigurálásához. Ha törölni szeretné az Azure Storage-fiókot, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

## <a name="create-azure-cosmos-db"></a>Create Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adatbázis és tároló hozzáadása

Az Azure Portalon az Adatkezelő segítségével létrehozhat egy adatbázist és egy tárolót.

1. Válassza a **Data Explorer elemet** az Azure Cosmos DB-fióklapján a bal oldali navigációs sávon, majd válassza az Új **tároló**lehetőséget.
1. A **Tároló hozzáadása** ablaktáblán adja meg az új tároló beállításait.

    ![Tárolóbeállítások hozzáadása](./media/data-manager-cosmosdb/add-container.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Adatbázis-azonosító | Adja meg **a blockchain-data-t** az új adatbázis neveként. |
    | Teljesítmény | Hagyja az átviteli értéket **400** kérelemegység/másodperc (RU/s) értéken. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.|
    | Tároló azonosítója | Írja be az **Üzenetek** nevet az új tároló neveként. |
    | Partíciókulcs | Használja **a /MessageType kapcsolót** partíciókulcsként. |

1. Válassza **az OK gombot.** Az Adatkezelő megjeleníti az új adatbázist és a létrehozott tárolót.

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Az Azure Logic Apps segítségével ütemezheti és automatizálhatja az üzleti folyamatokat és munkafolyamatokat, amikor integrálnia kell a rendszereket és szolgáltatásokat. Egy logikai alkalmazás segítségével csatlakoztathatja az Event Gridet az Azure Cosmos DB-hez.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Integráció** > **Logikai alkalmazás** lehetőséget.
1. Adja meg a logikai alkalmazás létrehozásának helyével kapcsolatos részleteket. Miután elkészült, válassza a **Létrehozás gombot.**

    A logikai alkalmazások létrehozásáról további információt az [Automatikus munkafolyamatok létrehozása az Azure Logic Apps alkalmazásokkal című témakörben talál.](../../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Miután az Azure üzembe helyezte az alkalmazást, válassza ki a logikai alkalmazás erőforrás.
1. A Logic Apps Designer **Sablonok csoportban**válassza az **Üres logikai alkalmazás lehetőséget.**

### <a name="add-event-grid-trigger"></a>Eseményrács hozzáadása eseményindító

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. Azure Event Grid-eseményindító használatával blockchain tranzakciós adatokat küld az Event Gridből a Cosmos DB-nek.

1. A Logic Apps Designerben keresse meg és válassza ki az Azure Event Grid-összekötőt. **Azure Event Grid**
1. Az **Eseményindítók** lapon válassza az **Erőforrás-esemény bekövetkezésekor**lehetőséget.
1. Hozzon létre egy API-kapcsolatot az Event Grid Topic.

    ![Eseményrács eseményindító-beállításai](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Előfizetés | Válassza ki az Eseményrács témakörét tartalmazó előfizetést. |
    | Erőforrás típusa | Válassza a **Microsoft.EventGrid.Topics lehetőséget.** |
    | Erőforrás neve | Válassza ki annak az Eseményrács-témakörnek a nevét, amelyben a Blockchain Data Manager tranzakciós adatüzeneteket küld. |

### <a name="add-cosmos-db-action"></a>Cosmos DB művelet hozzáadása

Adjon hozzá egy műveletet, amely en hozzon létre egy dokumentumot a Cosmos DB-ben minden tranzakcióhoz. Használja a tranzakciós üzenet típusát partíciókulcsként az üzenetek kategorizálásához.

1. Válassza az **Új lépés** lehetőséget.
1. A **Válasszon egy műveletet,** keresse meg az **Azure Cosmos DB**parancsot.
1. Válassza az **Azure Cosmos DB > műveletek > dokumentum létrehozása vagy frissítése**lehetőséget.
1. Hozzon létre egy API-kapcsolatot a Cosmos DB adatbázisával.

    ![Cosmos DB kapcsolat beállításai](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Kapcsolat neve | Válassza ki az Eseményrács témakörét tartalmazó előfizetést. |
    | DocumentDB-fiók | Válassza ki az [Azure Cosmos DB-fiók létrehozása](#create-azure-cosmos-db) szakaszban létrehozott DocumentDB-fiókot. |

1. Adja meg az Azure Cosmos DB **adatbázis-azonosítóját** és **gyűjteményazonosítóját,** amelyet korábban létrehozott az [Adatbázis és tároló hozzáadása](#add-a-database-and-container) szakaszban.

1. Válassza a **Dokumentum** beállítást. A *Dinamikus tartalom hozzáadása* előugró ablakban válassza a **Kifejezés** lehetőséget, és másolja és illessze be a következő kifejezést:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A kifejezés leadja az üzenet adatrészét, és az azonosítót időbélyegértékre állítja be.

1. Válassza **az Új paraméter hozzáadása lehetőséget,** és válassza a **Partíciókulcs értékét**.
1. Állítsa a **Partíciókulcs értékét** `"@{triggerBody()['data']['MessageType']}"`. Az értéket idézőjelek között kell körülvenni.

    ![Logic Apps Designer a Cosmos DB beállításaival](./media/data-manager-cosmosdb/create-action.png)

    Az érték a partíciókulcsot a tranzakcióüzenet típusára állítja be.

1. Kattintson a **Mentés** gombra.

A logikai alkalmazás figyeli az Eseményrács témakörét. Amikor egy új tranzakciós üzenetet küld a Blockchain Data Manager, a logikai alkalmazás létrehoz egy dokumentumot a Cosmos DB.When a new transaction message is sent from Blockchain Data Manager, the logic app creates a document in Cosmos DB.

## <a name="send-a-transaction"></a>Tranzakció küldése

Ezután küldjön egy tranzakciót a blokklánc főkönyvbe a létrehozott adatok teszteléséhez. Használja a **HelloBlockchain-szerződés** az előfeltételként létrehozott **SendRequest** függvényét: [Intelligens szerződések létrehozásához, létrehozásához és üzembe helyezéséhez használja a Visual Studio-kódot.](send-transaction.md)

1. Használja az Azure Blockchain Development Kit intelligens szerződés interakciós lap a **SendRequest** függvény hívásához. Kattintson a jobb gombbal **a HelloBlockchain.sol** elemre, és válassza a menü **Intelligens szerződés interakciós oldal megjelenítése** parancsát.

    ![Válassza az Intelligens egyezmény interakciós oldalának megjelenítése parancsot a menüből](./media/data-manager-cosmosdb/contract-interaction.png)

1. Válassza **a SendRequest** szerződés műveletet, és adja **hello, Blockchain!** a **requestMessage** paraméterhez. Válassza a **Végrehajtás** lehetőséget a **SendRequest** függvény tranzakción keresztüli hívásához.

    ![SendRequest művelet végrehajtása](./media/data-manager-cosmosdb/sendrequest-action.png)

A SendRequest függvény beállítja a **RequestMessage** és **az Állapot** mezőket. A **RequestMessage** jelenlegi állapota az az érv, amelyet átadott **a Hello, Blockchain**. Az **Állapot** mező értéke a **Kérés marad.**

## <a name="view-transaction-data"></a>Tranzakcióadatok megtekintése

Most, hogy csatlakoztatta a Blockchain Data Manager az Azure Cosmos DB, megtekintheti a blockchain tranzakciós üzenetek cosmos DB Data Explorer.

1. Nyissa meg a Cosmos DB Data Explorer nézetét. Például **cosmosdb-blockchain > Data Explorer > blockchain-data > Messages > Items**.

    ![Cosmos DB adatkezelő](./media/data-manager-cosmosdb/data-explorer.png)

    A Data Explorer felsorolja a Cosmos DB adatbázisban létrehozott blokklánc-adatüzeneteket.

1. Böngésszen az üzenetek között az elemazonosító kiválasztásával, és keresse meg az üzenetet a megfelelő tranzakciókivonattal.

    [![Blockchain tranzakció részletei](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A nyers tranzakciós üzenet a tranzakció részleteit tartalmazza. A tulajdonságadatok azonban titkosítva vannak.

    Mivel a HelloBlockchain intelligens szerződést adta hozzá a Blockchain Data Manager-példányhoz, a **rendszer egy ContractProperties** üzenettípust is küld, amely dekódolt tulajdonságadatokat tartalmaz.

1. Keresse meg a tranzakció **Hozadéktulajdonságai** üzenetet. Ez lesz a következő üzenet a listán.

    [![Blockchain tranzakció részletei](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A **DecodedProperties** tömb a tranzakció tulajdonságait tartalmazza.

Gratulálunk! Sikeresen létrehozott egy tranzakciós üzenetkezelőt a Blockchain Data Manager és az Azure Cosmos DB használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az oktatóanyaghoz használt erőforrásokat és erőforráscsoportokat. Erőforráscsoport törlése:

1. Az Azure Portalon keresse meg az **Erőforráscsoportot** a bal oldali navigációs ablakban, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

További információ a blokklánc-főkönyvekkel való integrációról.

> [!div class="nextstepaction"]
> [Az Ethereum Blockchain-összekötő használata az Azure Logic Apps alkalmazásokkal](ethereum-logic-app.md)
