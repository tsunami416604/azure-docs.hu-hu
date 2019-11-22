---
title: A geth használata az Azure Blockchain szolgáltatáshoz való csatlakoztatáshoz
description: Csatolás geth-példányhoz az Azure Blockchain szolgáltatás tranzakciós csomópontján
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 1c285a7b5cc04aa330e9f68fdb82cfd099a19e03
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285299"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Gyors útmutató: Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz való csatolás geth használata

Ebben a rövid útmutatóban egy Azure Blockchain szolgáltatás-tranzakciós csomóponton található geth-példányhoz csatolja a geth-ügyfelet. Miután csatolta, a geth JavaScript-konzollal hívhat meg egy web3 JavaScript Dapp API-t.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [geth](https://github.com/ethereum/go-ethereum/wiki/geth) telepítése
* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Geth-kapcsolatok karakterláncának beolvasása

A Azure Portal Azure Blockchain szolgáltatásbeli tranzakciós csomópontjához tartozó geth-alapú kapcsolatok karakterláncát kérheti le.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az Azure Blockchain-szolgáltatás tagját. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/connect-geth/transaction-nodes.png)

1. Válassza a **kapcsolatok karakterláncok**lehetőséget.
1. Másolja a kapcsolati karakterláncot a https-ről **(1. hozzáférési kulcs)** . A következő szakaszhoz szüksége lesz a karakterláncra.

    ![Kapcsolati sztring](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Kapcsolódás a geth

1. Nyisson meg egy parancssort vagy a rendszerhéjat.
1. A geth csatolása alparancs használatával csatlakoztassa a futó geth-példányhoz a tranzakciós csomóponton. Illessze be a kapcsolatok karakterláncát argumentumként a csatolási alparancshoz. Például:

    ``` bash
    geth attach <connection string>
    ```

1. Miután csatlakozott a tranzakciós csomópont Ethereum-konzolhoz, meghívhatja a web3 JavaScript Dapp API-t vagy a felügyeleti API-t.

    A chainId megkereséséhez például használja a következő API-t.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Ebben a példában a chainId a 661.

    ![Az Azure Blockchain szolgáltatás lehetősége](./media/connect-geth/geth-attach.png)

1. A konzolról való leválasztáshoz írja be a következőt: `exit`.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a geth-ügyfelet használta egy geth-példányhoz való csatoláshoz egy Azure Blockchain szolgáltatás-tranzakciós csomóponton. Próbálja ki a következő oktatóanyagot a Ethereum készült Azure Blockchain Development Kit használatával egy intelligens szerződési funkció létrehozásához, összeállításához, üzembe helyezéséhez és végrehajtásához tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése a Visual Studio Code használatával](send-transaction.md)
