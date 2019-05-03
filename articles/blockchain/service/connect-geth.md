---
title: Az Azure Blockchain-szolgáltatáshoz való csatlakozáshoz Geth használata
description: Az Azure Blockchain-szolgáltatás használatával Geth hálózat csatlakoztatása
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026920"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Gyors útmutató: A tranzakció a csomóponthoz csatlakozás Geth használatával

Geth egy Go Ethereum ügyfél segítségével csatlakoztassa az Azure Blockchain-szolgáltatás tranzakció csomóponton Geth példányra.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítés [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Az Azure Blockchain tag létrehozása](create-member.md)

## <a name="get-the-geth-connection-string"></a>Geth kapcsolati karakterláncának lekérése

Az Azure Portalon találhatja a Geth kapcsolati karakterláncot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain-szolgáltatás tag. Válassza ki **tranzakció csomópontok** és az alapértelmezett tranzakció csomópont hivatkozására.

    ![Válassza ki az alapértelmezett tranzakció csomópont](./media/connect-geth/transaction-nodes.png)

1. Válassza ki **kapcsolati karakterláncok**.
1. Másolja a kapcsolati karakterláncot a **HTTPS (hozzáférési kulcs: 1)**. A parancs a következő szakaszban szükség van.

    ![Kapcsolati sztring](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Csatlakozás Geth

1. Nyisson meg egy parancssort vagy a rendszerhéjat.
1. Használja a Geth csatolása alárendelt parancs a tranzakció csomóponton futó Geth példányhoz csatolni. Illessze be a kapcsolati karakterláncot az attach parancs argumentumaként. Például:

    ```
    geth attach <connection string>
    ```

1. Miután csatlakozott a tranzakció csomópont Ethereum-konzol, meghívhatja a weben 3 JavaScript Dapp API vagy a felügyeleti API-t.

    Ha például a következő API használatával találja meg a chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Ebben a példában a chainId 297.

    ![Az Azure Blockchain-szolgáltatás lehetőséget](./media/connect-geth/geth-attach.png)

1. A konzolon bontsa a kapcsolatot, írja be a `exit`.

## <a name="next-steps"></a>További lépések

Ebben a rövid, amellyel a Geth ügyfél csatlakoztatása az Azure Blockchain-szolgáltatás tranzakció csomóponton Geth példányra. Próbálja ki a következő oktatóanyaggal, üzembe helyezése és a egy tranzakció Truffle használatával küldjön.

> [!div class="nextstepaction"]
> [Egy tranzakció küldése](send-transaction.md)