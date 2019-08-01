---
title: Csatlakozás Truffle használatával
description: Kapcsolódás Azure Blockchain Service networkhez a szarvasgomba használatával
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698466"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Gyors útmutató: A szarvasgomba használata az Azure Blockchain Service networkhez való kapcsolódáshoz

A szarvasgomba egy blockchain fejlesztési környezet, amellyel csatlakozhat egy Azure Blockchain szolgáltatás-csomóponthoz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Azure Blockchain-tag létrehozása](create-member.md)
* A [szarvasgomba](https://github.com/trufflesuite/truffle)telepítése. A szarvasgombához több eszközt kell telepíteni, beleértve a [Node. js](https://nodejs.org)-t, a [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)-t.
* Telepítse a [Python-2.7.15](https://www.python.org/downloads/release/python-2715/). A Python szükséges a Web3.
* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.
* Telepítse a [Visual Studio Code soliding bővítményt](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

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

### <a name="transaction-node-endpoint-addresses"></a>Tranzakciós csomópont végpontjának címei

1. A Azure Portal navigáljon minden tranzakciós csomóponthoz, és válassza a **tranzakciós csomópontok >** a kapcsolódási karakterláncok lehetőséget.
1. Másolja és mentse a végponti URL-címet a https-ről **(1. hozzáférési kulcs)** minden tranzakciós csomóponthoz. Az oktatóanyag későbbi részében szüksége lesz az intelligens szerződés konfigurációs fájljához tartozó végponti címekre.

    ![Tranzakció végpontjának címe](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Konfigurációs fájl szerkesztése

1. Indítsa el a Visual Studio Code-ot, és nyissa meg a szarvasgomba Project Directory mappát a **fájl > mappa megnyitása** menü használatával.
1. Nyissa meg a szarvasgomba konfigurációs fájlt `truffle-config.js`.
1. Cserélje le a fájl tartalmát a következő konfigurációs adatokra. Adjon hozzá egy változót, amely tartalmazza a végponti címeket. Cserélje le a szögletes zárójelet az előző fejezetekben összegyűjtött értékekre.

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

1. Mentse a módosításokat a `truffle-config.js`következőre:.

## <a name="connect-to-transaction-node"></a>Csatlakozás tranzakciós csomóponthoz

A *Web3* használatával kapcsolódjon a tranzakciós csomóponthoz.

1. Az alapértelmezett tranzakciós csomóponthoz való kapcsolódáshoz használja a szarvasgomba konzolt.

    ``` bash
    truffle console --network defaultnode
    ```

    A szarvasgomba csatlakozik az alapértelmezett tranzakciós csomóponthoz, és egy interaktív konzolt biztosít.

    A **web3** objektum metódusait hívhatja a tranzakciós csomóponttal való interakcióhoz.

1. Hívja meg a **getBlockNumber** metódust az aktuális blokk számának visszaadásához.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Példa a kimenetre:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Lépjen ki a szarvasgomba fejlesztői konzolból.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szarvasgomba-projektet az Azure Blockchain szolgáltatás alapértelmezett tranzakciós csomópontjához való kapcsolódáshoz.

Próbálja ki a következő oktatóanyagot, hogy a szarvasgomba használatával küldjön tranzakciót a konzorcium blockchain-hálózatára.

> [!div class="nextstepaction"]
> [Tranzakció küldése](send-transaction.md)
