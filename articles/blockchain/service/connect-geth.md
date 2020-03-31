---
title: Az Azure Blockchain szolgáltatáshoz való csatoláshoz a Geth használatával
description: Csatolás geth-példányhoz az Azure Blockchain szolgáltatás tranzakciós csomópontján
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455827"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Rövid útmutató: Geth használatával csatolja az Azure Blockchain szolgáltatás tranzakciós csomópontjának

Ebben a rövid útmutatóban a Geth-ügyfél segítségével csatolja a Geth-példány egy Azure Blockchain szolgáltatás tranzakciós csomópontján. A csatlakoztatás után a Geth JavaScript konzol segítségével hívja meg a web3 JavaScript Dapp API-t.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) telepítése
* Teljes [rövid útmutató: Blockchain-tag létrehozása az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service Blockchain-tagot az Azure CLI használatával](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Geth kapcsolati karakterlánc beszerezése

Beszerezheti az Azure Blockchain szolgáltatás tranzakciós csomópontjának Geth-kapcsolati karakterláncát az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az Azure Blockchain szolgáltatás tagot. Válassza **a Tranzakciócsomópontok és** az alapértelmezett tranzakciócsomópont-kapcsolat lehetőséget.

    ![Alapértelmezett tranzakciócsomópont kiválasztása](./media/connect-geth/transaction-nodes.png)

1. Válassza **a Kapcsolati karakterláncok lehetőséget**.
1. Másolja a kapcsolati karakterláncot a **HTTPS protokollból (1. hozzáférési kulcs)**. A következő szakaszhoz szükség van a karakterláncra.

    ![Kapcsolati sztring](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Csatlakozás geth-hez

1. Nyisson meg egy parancssort vagy rendszerhéjat.
1. Használja a Geth csatolása alkövetel a tranzakciócsomópont futó Geth-példányához csatolásához. Illessze be a kapcsolati karakterláncot argumentumként a csatolási alparancshoz. Példa:

    ``` bash
    geth attach <connection string>
    ```

1. Miután csatlakozott a tranzakciós csomópont Ethereum konzoljához, hívhatja a web3 JavaScript Dapp API-t vagy a felügyeleti API-t.

    Például a következő API-t, hogy megtudja, a chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Ebben a példában a chainId 661.

    ![Az Azure Blockchain szolgáltatás lehetősége](./media/connect-geth/geth-attach.png)

1. A konzolról való `exit`leválasztáshoz írja be a következőt:

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Geth-ügyfél használatával egy Geth-példányhoz kapcsolódott egy Azure Blockchain Service tranzakciós csomóponton. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum használatával intelligens szerződésfunkció létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához egy tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, létrehozása és üzembe helyezése az Azure Blockchain Szolgáltatásban](send-transaction.md)
