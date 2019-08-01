---
title: Intelligens szerződések használata az Azure Blockchain Service-ben
description: Útmutató arról, hogyan használható az Azure Blockchain Service egy intelligens szerződés üzembe helyezéséhez és a függvények egy tranzakción keresztüli végrehajtásához.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705131"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>Oktatóanyag: Intelligens szerződések használata az Azure Blockchain Service-ben

Ebben az oktatóanyagban a Ethereum készült Azure Blockchain Development Kit használatával hozzon létre és helyezzen üzembe egy intelligens szerződést, majd hajtson végre egy intelligens szerződési funkciót egy konzorciumi Blockchain-hálózat tranzakcióján keresztül.

A Ethereum készült Azure Blockchain Development Kit a következő célokra használható:

> [!div class="checklist"]
> * Kapcsolódás az Azure Blockchain Service Consortium Blockchain-taghoz
> * Intelligens szerződés létrehozása
> * Intelligens szerződés üzembe helyezése
> * Intelligens szerződési funkció végrehajtása tranzakción keresztül
> * Szerződés állapotának lekérdezése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [gyors útmutató: Blockchain-tag létrehozása a Azure Portal](create-member.md) vagy [a gyors útmutató használatával: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI-vel](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Az Azure Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Adja hozzá a Python. exe fájlt az elérési úthoz. Az Azure Blockchain Development Kit esetében a Python elérési útja szükséges.
* [Szarvasgomba](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Az Azure Blockchain Development Kit-környezet ellenőrzése

Az Azure Blockchain Development Kit ellenőrzi, hogy teljesülnek-e a fejlesztési környezet előfeltételei. A fejlesztési környezet ellenőrzése:

A vs Code parancs palettáján válassza **az Azure Blockchain: Kezdőlap**megjelenítése.

Az Azure Blockchain Development Kit olyan érvényesítési parancsfájlt futtat, amely körülbelül egy percet vesz igénybe. Megtekintheti a kimenetet a **terminal > új terminál**lehetőség kiválasztásával. A terminál menüsorában válassza a **kimenet** fület és az **Azure Blockchain** a legördülő menüben. A sikeres érvényesítés az alábbi képhez hasonlóan néz ki:

![Érvényes fejlesztői környezet](./media/send-transaction/valid-environment.png)

 Ha hiányzik egy szükséges eszköz, az **Azure Blockchain Development Kit – Preview** nevű új lap felsorolja a telepíteni kívánt alkalmazásokat és az eszközök letöltésére vonatkozó hivatkozásokat.

![Fejlesztői csomaghoz szükséges alkalmazások](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>Kapcsolódás a konzorcium tagjához

A konzorcium tagjaihoz az Azure Blockchain Development Kit VS Code bővítmény használatával csatlakozhat. A konzorciumhoz való csatlakozás után intelligens szerződéseket állíthat össze, építhet ki és helyezhet üzembe egy Azure Blockchain Service Consortium-tag számára.

Ha nincs hozzáférése az Azure Blockchain Service Consortium egyik tagjához, hajtsa végre [az előfeltételként szükséges rövid útmutatót: Blockchain-tag létrehozása a Azure Portal](create-member.md) vagy [a gyors útmutató használatával: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI](create-member-cli.md)használatával.

1. A Visual Studio Code (VS Code) Explorer ablaktáblán bontsa ki az **Azure Blockchain** bővítményt.
1. Válassza **a kapcsolódás konzorciumhoz**lehetőséget.

   ![Kapcsolódás a konzorciumhoz](./media/send-transaction/connect-consortium.png)

    Ha az Azure-hitelesítésre kéri, kövesse az utasításokat a böngészőben való hitelesítéshez.
1. Válassza a **Kapcsolódás az Azure Blockchain Service consortiumhoz** lehetőséget a parancssor legördülő menüben.
1. Válassza ki az Azure Blockchain Service Consortium-taghoz társított előfizetést és erőforráscsoportot.
1. Válassza ki a konzorciumot a listából.

A konzorcium és a blockchain tagjai a Visual Studio Explorer oldalsó sávján jelennek meg.

![A konzorcium megjelenik az Explorerben](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>Intelligens szerződés létrehozása

A Ethereum készült Azure Blockchain Development Kit a Project templates és a szarvasgomba eszközt használja a szerződések készítéséhez, elkészítéséhez és üzembe helyezéséhez.

1. A vs Code parancs palettáján válassza **az Azure Blockchain: Új szilárdtest-projekt**.
1. Válassza az alapszintű **projekt létrehozása**lehetőséget.
1. Hozzon létre egy nevű `HelloBlockchain` új mappát, és **válassza az új projekt elérési útja lehetőséget**.

Az Azure Blockchain Development Kit létrehoz és inicializál egy új szilárdtest-projektet. Az alapszintű projekt tartalmaz egy minta **HelloBlockchain** intelligens szerződést, valamint az összes szükséges fájlt, amely az Azure Blockchain szolgáltatásban a konzorciumi tag felépítésére és üzembe helyezésére szolgál. A projekt létrehozása több percet is igénybe vehet. Az Azure Blockchain kimenetének kiválasztásával nyomon követheti a VS Code termináljának előrehaladását.

A projekt szerkezete a következő példához hasonlóan néz ki:

   ![Szilárdtest-projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Intelligens szerződés készítése

Az intelligens szerződések a projekt **szerződések** könyvtárában találhatók. Az intelligens szerződések fordítása előtt üzembe helyezheti azokat egy blockchain. A **szerződések összeállítása** paranccsal lefordíthatja a projektben lévő összes intelligens szerződést.

1. A VS Code Explorer oldalsávján bontsa ki a projekt **szerződések** mappáját.
1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések létrehozása** elemét.

    ![Szerződések készítése](./media/send-transaction/build-contracts.png)

Az Azure Blockchain Development Kit a szarvasgomba használatával fordítja le az intelligens szerződéseket.

![Kimenet fordítása](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Intelligens szerződés üzembe helyezése

A szarvasgomba áttelepítési parancsfájlok használatával helyezi üzembe a szerződéseit egy Ethereum-hálózaton. A Migrálás a projekt áttelepítési könyvtárában található JavaScript-fájlok.

1. Az intelligens szerződés üzembe helyezéséhez kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések telepítése** menüpontját.
1. Válassza ki az Azure Blockchain Consortium **-hálózatot a truffle-config. js fájlból**. A projekt létrehozásakor a konzorcium blockchain-hálózata hozzá lett adva a projekt szarvasgomba konfigurációs fájljához.
1. Válassza a **hívóbetűje**előállítása lehetőséget. Válasszon egy fájlnevet, és mentse a hívóbetűje fájlt a projekt mappájába. Például: `myblockchainmember.env`. A hívóbetűje-fájl egy Ethereum titkos kulcs létrehozásához használható a blockchain-tag számára.

Az Azure Blockchain Development Kit a szarvasgomba használatával hajtja végre az áttelepítési parancsfájlt a szerződések a Blockchain való üzembe helyezéséhez.

![A szerződés sikeresen telepítve](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Szerződési függvény meghívása

A **HelloBlockchain** -szerződés **SendRequest hívás** funkciója módosítja a **RequestMessage** állapot változót. Egy blockchain-hálózat állapotának módosítása tranzakción keresztül történik. Létrehozhat egy parancsfájlt, amely a **SendRequest hívás** függvényt tranzakción keresztül hajtja végre.

1. Hozzon létre egy új fájlt a szarvasgomba-projekt gyökerében, `sendrequest.js`és nevezze el. Adja hozzá a következő Web3 JavaScript-kódot a fájlhoz.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Ha az Azure Blockchain Development Kit létrehoz egy projektet, a rendszer létrehozza a szarvasgomba konfigurációs fájlt a konzorcium Blockchain hálózati végpontjának részleteivel. Nyissa meg a **truffle-config. js fájlt** a projektben. A konfigurációs fájl két hálózatot listáz: egy névvel ellátott fejlesztést és egyet a konzorcium nevével.
1. A VS Code terminál ablaktábláján a szarvasgomba használatával hajtsa végre a szkriptet a konzorcium blockchain-hálózatán. A terminál ablaktáblájának menüsorában válassza a **terminál** fület és a PowerShellt a legördülő menüben.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Cserélje \<le a\> blockchain hálózatot a **truffle-config. js**fájlban definiált blockchain-hálózat nevére.

A szarvasgomba végrehajtja a szkriptet a blockchain-hálózaton.

![Parancsprogram kimenete](./media/send-transaction/execute-transaction.png)

Ha egy tranzakción keresztül hajtja végre a szerződés függvényét, a tranzakció addig nem lesz feldolgozva, amíg létre nem jön egy blokk. Az olyan függvények, amelyeket egy tranzakción keresztül kell végrehajtani, visszatérési érték helyett tranzakció-azonosítót adnak vissza.

## <a name="query-contract-state"></a>Szerződés állapotának lekérdezése

Az intelligens szerződési függvények az állapot változóinak aktuális értékét adhatják vissza. Adjon hozzá egy függvényt egy állapotjelző változó értékének visszaadásához.

1. A **HelloBlockchain. Sol**-ben adjon hozzá egy **getMessage** függvényt a **HelloBlockchain** intelligens szerződéshez.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    A függvény az állapot változójában tárolt üzenetet adja vissza a szerződés aktuális állapota alapján.

1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, majd válassza a **szerződések létrehozása** lehetőséget a menüből az intelligens szerződés módosításainak fordításához.
1. A telepítéshez kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések telepítése** menüpontját.
1. Ezután hozzon létre egy szkriptet a paranccsal a **getMessage** függvény meghívásához. Hozzon létre egy új fájlt a szarvasgomba-projekt gyökerében, `getmessage.js`és nevezze el. Adja hozzá a következő Web3 JavaScript-kódot a fájlhoz.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. A VS Code terminál ablaktábláján a szarvasgomba használatával hajtsa végre a szkriptet a blockchain-hálózaton. A terminál ablaktáblájának menüsorában válassza a **terminál** fület és a PowerShellt a legördülő menüben.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Cserélje \<le a\> blockchain hálózatot a **truffle-config. js**fájlban definiált blockchain-hálózat nevére.

A szkript lekérdezi az intelligens szerződést a getMessage függvény meghívásával. A rendszer visszaadja a **RequestMessage** állapot változó aktuális értékét.

![Parancsprogram kimenete](./media/send-transaction/execute-get.png)

Figyelje meg, hogy az érték nem **Hello, blockchain!** . Ehelyett a visszaadott érték helyőrző. A szerződés módosításakor és központi telepítésekor a Szerződés új szerződési címen kap, és az állapot változói az intelligens szerződés konstruktorában vannak hozzárendelve. A szarvasgomba minta **2_deploy_contracts. js** áttelepítési parancsfájl üzembe helyezi az intelligens szerződést, és argumentumként átadja a helyőrző értékét. A konstruktor a **RequestMessage** állapot változót a helyőrző értékre állítja, és a függvény a visszaadott értéket adja vissza.

1. A **RequestMessage** állapot változó beállításához és az érték lekérdezéséhez futtassa újra a **SendRequest hívás. js** és a **GetMessage. js** parancsfájlokat.

    ![Parancsprogram kimenete](./media/send-transaction/execute-set-get.png)

    a **SendRequest hívás. js** beállítja a **RequestMessage** állapot változót a Hello értékre **, blockchain!** a és a **GetMessage. js** lekérdezi a **RequestMessage** State változó értékére vonatkozó szerződést, és a következőt adja vissza: **Hello, blockchain!** .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforrásokat úgy `myResourceGroup` , hogy törli a blockchain-tag előfeltételeinek *létrehozása* előfeltételként létrehozott erőforráscsoportot.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy minta-szilárdtest projektet az Azure Blockchain Development Kit használatával. Az Azure Blockchain Service-ben üzemeltetett blockchain Consortium-hálózat tranzakcióján keresztül létrehozott és telepített egy intelligens szerződést.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazások fejlesztése az Azure Blockchain Service használatával](develop.md)
