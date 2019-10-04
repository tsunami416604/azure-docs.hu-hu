---
title: A geth használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz
description: Kapcsolódás Azure Blockchain Service networkhez a geth használatával
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931776"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Gyors útmutató: A Geth használata egy tranzakciós csomóponthoz való kapcsolódáshoz

A geth egy go Ethereum-ügyfél, amely egy Azure Blockchain szolgáltatásbeli tranzakciós csomóponton található geth-példányhoz csatolható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [geth](https://github.com/ethereum/go-ethereum/wiki/geth) telepítése
* Teljes [gyors útmutató: Blockchain-tag létrehozása a Azure Portal](create-member.md) vagy [a gyors útmutató használatával: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI-vel](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>A geth-kapcsolatok karakterláncának beolvasása

A geth-kapcsolatok karakterlánca a Azure Portalban található.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-szolgáltatás tagjához. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/connect-geth/transaction-nodes.png)

1. Válassza a **kapcsolatok karakterláncok**lehetőséget.
1. Másolja a kapcsolati karakterláncot a https-ről **(1. hozzáférési kulcs)** . A következő szakaszhoz szükség van a parancsra.

    ![Kapcsolati sztring](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Kapcsolódás a geth

1. Nyisson meg egy parancssort vagy a rendszerhéjat.
1. A geth csatolása alparancs használatával csatlakoztassa a futó geth-példányhoz a tranzakciós csomóponton. Illessze be a kapcsolatok karakterláncát argumentumként a csatolási alparancshoz. Például:

    ```
    geth attach <connection string>
    ```

1. Miután csatlakozott a tranzakciós csomópont Ethereum-konzolhoz, meghívhatja a web3 JavaScript Dapp API-t vagy a felügyeleti API-t.

    A chainId megkereséséhez például használja a következő API-t.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Ebben a példában a chainId a 297.

    ![Az Azure Blockchain szolgáltatás lehetősége](./media/connect-geth/geth-attach.png)

1. A konzolról való leválasztáshoz `exit`írja be a következőt:.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a geth-ügyfelet használta egy geth-példányhoz való csatoláshoz egy Azure Blockchain szolgáltatás-tranzakciós csomóponton. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum és a szarvasgomba használatával egy intelligens szerződési funkció egy tranzakción keresztül történő létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése a Visual Studio Code használatával](send-transaction.md)