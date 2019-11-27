---
title: A szarvasgomba használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz
description: Kapcsolódás Azure Blockchain Service networkhez a szarvasgomba használatával
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455796"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Rövid útmutató: a szarvasgomba használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz

Ebben a rövid útmutatóban a szarvasgomba-kapcsolatot használja egy Azure Blockchain szolgáltatás-tranzakciós csomóponthoz. Ezután a szarvasgomba interaktív konzollal meghívhatja a **web3** metódusokat a blockchain-hálózattal való kommunikációhoz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* A [szarvasgomba](https://github.com/trufflesuite/truffle)telepítése. A szarvasgombához több eszközt kell telepíteni, beleértve a [Node. js](https://nodejs.org)-t, a [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)-t.
* Telepítse a [Python-2.7.15](https://www.python.org/downloads/release/python-2715/). A Python szükséges a Web3.

## <a name="create-truffle-project"></a>Szarvasgomba-projekt létrehozása

1. Nyisson meg egy Node. js parancssort vagy rendszerhéjat.
1. Váltson arra a könyvtárra, ahol létre szeretné hozni a szarvasgomba Project könyvtárat.
1. Hozzon létre egy könyvtárat a projekthez, és módosítsa az elérési utat az új könyvtárba. Például:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. A szarvasgomba projekt inicializálása.

    ``` bash
    truffle init
    ```

1. Telepítse a Ethereum JavaScript API-web3 a projekt mappájába. Jelenleg a web3 Version 1.0.0-Beta. 37 verziója szükséges.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    A telepítés során NPM figyelmeztetéseket kaphat.
    
## <a name="configure-truffle-project"></a>Szarvasgomba-projekt konfigurálása

A szarvasgomba projekt konfigurálásához szükség van néhány tranzakciós csomópontra a Azure Portal.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Nyissa meg az Azure Blockchain-szolgáltatás tagját. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/connect-truffle/transaction-nodes.png)

1. Válassza a **kapcsolatok karakterláncok**lehetőséget.
1. Másolja a kapcsolati karakterláncot a https-ről **(1. hozzáférési kulcs)** . A következő szakaszhoz szüksége lesz a karakterláncra.

    ![Kapcsolati sztring](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Konfigurációs fájl szerkesztése

Ezután frissítenie kell a szarvasgomba konfigurációs fájlját a tranzakciós csomópont végpontjának használatával.

1. A **truffledemo** projekt mappában Nyissa meg a szarvasgomba konfigurációs fájlt `truffle-config.js` egy szerkesztőben.
1. Cserélje le a fájl tartalmát a következő konfigurációs adatokra. Adjon hozzá egy változót, amely tartalmazza a végponti címeket. A szögletes zárójelet cserélje le az előző szakaszból összegyűjtött értékekre.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Mentse `truffle-config.js`módosításait.

## <a name="connect-to-transaction-node"></a>Csatlakozás tranzakciós csomóponthoz

A *Web3* használatával kapcsolódjon a tranzakciós csomóponthoz.

1. Az alapértelmezett tranzakciós csomóponthoz való kapcsolódáshoz használja a szarvasgomba konzolt. A parancssorban vagy a rendszerhéjban futtassa a következő parancsot:

    ``` bash
    truffle console --network defaultnode
    ```

    A szarvasgomba csatlakozik az alapértelmezett tranzakciós csomóponthoz, és egy interaktív konzolt biztosít.

    A **web3** objektumon metódusokat hívhat meg a blockchain-hálózattal való kommunikációhoz.

1. Hívja meg a **getBlockNumber** metódust az aktuális blokk számának visszaadásához.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Példa a kimenetre:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a szarvasgomba az Azure Blockchain szolgáltatás alapértelmezett tranzakciós csomóponthoz való kapcsolódását használta, és az interaktív konzollal visszaküldte az aktuális Blockchain-blokk számát.

Próbálja ki a következő oktatóanyagot a Ethereum készült Azure Blockchain Development Kit használatával egy intelligens szerződési funkció létrehozásához, összeállításához, üzembe helyezéséhez és végrehajtásához tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése az Azure Blockchain Service-ben](send-transaction.md)
