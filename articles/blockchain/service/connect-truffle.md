---
title: Csatlakozás Truffle használatával
description: Az Azure Blockchain-szolgáltatás használatával Truffle hálózat csatlakoztatása
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 037f37d6a8e1c41579403dbf7c9dd265efbb5d10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026955"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Gyors útmutató: Truffle használatával csatlakozni egy az Azure Blockchain-szolgáltatás hálózat

Truffle egy segítségével az Azure Blockchain-szolgáltatás a csomóponthoz csatlakozás blokklánc-fejlesztési környezetre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Blockchain tag létrehozása](create-member.md)
* Truffle igényel a telepítendő eszközöket is beleértve [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), és [Truffle](https://github.com/trufflesuite/truffle).

    Gyorsan beállíthatja a Windows 10-es, telepítse a [Ubuntu on Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) egy terminál Unix Bash-rendszerhéjból telepítse [Truffle](https://github.com/trufflesuite/truffle). Az Ubuntu on Windows terjesztési tartalmazza a Node.js és Git.

## <a name="create-truffle-project"></a>Truffle projekt létrehozása

1. Nyisson meg egy Bash rendszerhéj terminált.
1. Módosítsa a könyvtárat, ahová a Truffle projektkönyvtárban hozzon létre. Például:

    ``` bash
    cd /mnt/c
    ```

1. Hozzon létre egy könyvtárat a projekthez, és az elérési utat módosítsa arra az új címtárra. Például:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Telepítse a weben Ethereum JavaScript API 3 a projektmappában. Verzió weben 3 verzió 1.0.0-beta.37 jelenleg szükséges.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    A telepítés során npm figyelmeztetés jelenhet meg.

1. A Truffle projekt inicializálása.

    ``` bash
    truffle init
    ```

1. Interaktív fejlesztői konzol indítása Truffle.

    ``` bash
    truffle develop
    ```

    Truffle létrehoz egy helyi fejlesztési blockchain, és a egy interaktív konzolt kínál.

## <a name="connect-to-transaction-node"></a>Tranzakció a csomóponthoz csatlakozás

Használjuk a weben 3 a tranzakció-csomóponthoz szeretne csatlakozni. Megtekintheti a weben 3 kapcsolati karakterláncot az Azure Portalról.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain-szolgáltatás tag. Válassza ki **tranzakció csomópontok** és az alapértelmezett tranzakció csomópont hivatkozására.

    ![Válassza ki az alapértelmezett tranzakció csomópont](./media/connect-truffle/transaction-nodes.png)

1. Válassza ki **mintakód > weben 3**.
1. Másolja a JavaScript a **HTTPS (hozzáférési kulcs: 1)**. A kód a Truffle interaktív fejlesztői konzol van szükség.

    ![Weben 3 kód](./media/connect-truffle/web3-code.png)

1. Illessze be a JavaScript-kódot az előző lépésben a Truffle interaktív fejlesztői konzolon. A kód létrehoz egy weben 3-objektumot, amely az Azure Blockchain-szolgáltatás tranzakció csomópont csatlakozik.

    Példa a kimenetre:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    truffle(develop)>
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