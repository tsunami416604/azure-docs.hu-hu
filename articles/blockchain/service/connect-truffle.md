---
title: Csatlakozás Truffle használatával
description: Az Azure Blockchain-szolgáltatás használatával Truffle hálózat csatlakoztatása
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 692bac0e0474ead7a0acd0887c3c10055edf8553
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399991"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Gyors útmutató: Truffle használatával csatlakozni egy az Azure Blockchain-szolgáltatás hálózat

Truffle egy segítségével az Azure Blockchain-szolgáltatás a csomóponthoz csatlakozás blokklánc-fejlesztési környezetre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Blockchain tag létrehozása](create-member.md)
* Telepítés [Truffle](https://github.com/trufflesuite/truffle). Truffle igényel a telepítendő eszközöket is beleértve [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Telepítés [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python weben 3 van szükség.

## <a name="create-truffle-project"></a>Truffle projekt létrehozása

1. Nyissa meg a Node.js parancssort vagy a rendszerhéjat.
1. Módosítsa a könyvtárat, ahová a Truffle projektkönyvtárban hozzon létre.
1. Hozzon létre egy könyvtárat a projekthez, és az elérési utat módosítsa arra az új címtárra. Például:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. A Truffle projekt inicializálása.

    ``` bash
    truffle init
    ```

1. Telepítse a weben Ethereum JavaScript API 3 a projektmappában. Verzió weben 3 verzió 1.0.0-beta.37 jelenleg szükséges.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    A telepítés során npm figyelmeztetés jelenhet meg.

1. Interaktív fejlesztői konzol indítása Truffle.

    ``` bash
    truffle develop
    ```

    Truffle létrehoz egy helyi fejlesztési blockchain, és a egy interaktív konzolt kínál.

## <a name="connect-to-transaction-node"></a>Csatlakozás tranzakciós csomóponthoz

Használat *weben 3* a tranzakció-csomóponthoz szeretne csatlakozni. Beszerezheti a *weben 3* kapcsolati karakterláncot az Azure Portalról.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain-szolgáltatás tag. Válassza ki **tranzakció csomópontok** és az alapértelmezett tranzakció csomópont hivatkozására.

    ![Válassza ki az alapértelmezett tranzakció csomópont](./media/connect-truffle/transaction-nodes.png)

1. Válassza ki **mintakód > weben 3**.
1. Másolja a JavaScript a **HTTPS (hozzáférési kulcs: 1)** . A kód a Truffle interaktív fejlesztői konzol van szükség.

    ![Weben 3 kód](./media/connect-truffle/web3-code.png)

1. Illessze be a JavaScript-kódot az előző lépésben a Truffle interaktív fejlesztői konzolon. A kód létrehoz egy weben 3-objektumot, amely az Azure Blockchain-szolgáltatás tranzakció csomópont csatlakozik.

    Példa a kimenetre:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    A módszerek segítségével meghívhatja a **weben 3** objektumot a tranzakció csomópont kommunikáljon.

1. Hívja a **getBlockNumber** metódus az aktuális blokk számának visszaadása.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Példa a kimenetre:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Lépjen ki a Truffle fejlesztői konzolon.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Truffle projektet az Azure Blockchain-szolgáltatás alapértelmezett tranzakció csomóponthoz szeretne csatlakozni.

Próbálja ki a következő oktatóanyaggal, Truffle használható egy tranzakció küldéséhez a consortium blockchain-hálózat.

> [!div class="nextstepaction"]
> [Egy tranzakció küldése](send-transaction.md)