---
title: A MetaMask összekötése egy Azure Blockchain Service Network szolgáltatással
description: Kapcsolódjon az Azure Blockchain Service networkhez a MetaMask használatával, és helyezzen üzembe egy intelligens szerződést.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/12/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: b89e75d406a738fb685bb3294dca8d79a2b9170c
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966561"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Gyors útmutató: Intelligens szerződés összekapcsolásának és üzembe helyezésének MetaMask használata

Ebben a rövid útmutatóban a MetaMask használatával csatlakozhat egy Azure Blockchain Service networkhez, és a Remix használatával üzembe helyezhet egy intelligens szerződést. Az Metamask egy böngésző-bővítmény, amely egy éteri pénztárcát kezel, és intelligens szerződéses műveleteket hajt végre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [gyors útmutató: Blockchain-tag létrehozása a Azure Portal](create-member.md) vagy [a gyors útmutató használatával: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI-vel](create-member-cli.md)
* A [MetaMask-tallózó bővítmény](https://metamask.io) telepítése
* MetaMask- [mobiltárca](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) létrehozása

## <a name="get-endpoint-address"></a>Végponti címek beolvasása

A Blockchain hálózathoz való kapcsolódáshoz az Azure Blockchain szolgáltatás végpontjának címe szükséges. A végponti címeket és a hozzáférési kulcsokat a Azure Portal találja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-szolgáltatás tagjához. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/connect-metamask/transaction-nodes.png)

1. Válassza a **kapcsolati karakterláncok > hozzáférési kulcsok**elemet.
1. Másolja a végponti címeket a https-ről **(1. hozzáférési kulcs)** . A következő szakasz címe szükséges.

    ![Kapcsolati sztring](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>MetaMask összekötése

1. Nyissa meg a MetaMask böngésző bővítményét, és jelentkezzen be.
1. A hálózat legördülő menüben válassza az **Egyéni RPC**lehetőséget.

    ![Egyéni RPC](./media/connect-metamask/custom-rpc.png)

1. Az **új hálózat > új RPC URL-** cím mezőben adja meg az előző szakaszból másolt végponti címet.
1. Kattintson a **Mentés** gombra.

    Ha a kapcsolatok sikeresek voltak, a magánhálózat a hálózat legördülő listájában jelenik meg.

    ![Új hálózat](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Intelligens szerződés üzembe helyezése

A Remix egy böngészőalapú szilárdtest-fejlesztési környezet. A MetaMask és a Remix együttes használatával az intelligens szerződéseket üzembe helyezheti, és műveleteket hajthat végre.

1. A böngészőjében lépjen a `https://remix.ethereum.org` helyre.
1. Válassza az **új fájl** elemet a **Kezdőlap** lap **fájl**területén.

    Nevezze el az új `simple.sol`fájlt.

    ![Fájl létrehozása](./media/connect-metamask/create-file.png)

    Kattintson az **OK** gombra.
1. A Remix Editorban illessze be a következő **egyszerű intelligens szerződés** kódját.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Az **egyszerű szerződés** deklarál egy **Balance**nevű állapotjelző változót. Két függvény van definiálva. A **Hozzáadás** függvény egy számot ad hozzá az **egyenleghez**. A **Get** függvény az **Egyenleg**értékét adja vissza.
1. A szerződés fordításához először válassza a szilárdtest-fordító ablaktáblát, majd válassza a **Simple. Sol fordítása**lehetőséget. 

    ![Lefordítani](./media/connect-metamask/compile.png)

1. Válassza a **központi telepítés & Futtatás** ablaktáblát, majd állítsa be a **környezetet** a **Web3** beadásához, hogy a blockchain-tag MetaMask-kapcsolaton keresztül kapcsolódjon.

    ![Futtatás lap](./media/connect-metamask/injected-web3.png)

1. Válassza ki az **egyszerű** szerződést, majd a **telepítés**lehetőséget.

    ![Üzembe helyezés](./media/connect-metamask/deploy.png)


1. Egy MetaMask értesítés jelenik meg, amely figyelmezteti, hogy nem rendelkezik elegendő összeggel a tranzakció végrehajtásához.

    Nyilvános blockchain-hálózat esetén a tranzakciós díj megfizetéséhez éterre van szükség. Mivel ez egy konzorciumban található magánhálózat, a gáz árát nullára állíthatja.

1.  Válassza ki a **gáz díjat > a > speciális szerkesztése**lehetőséget, állítsa a **gázárak** 0-ra.

    ![Gázárak](./media/connect-metamask/gas-price.png)

    Kattintson a **Mentés** gombra.

1. Válassza a **megerősítés** lehetőséget az intelligens szerződés blockchain való telepítéséhez.
1. A **központilag telepített szerződések** szakaszban bontsa ki az **egyszerű** szerződést.

    ![Központilag telepített szerződés](./media/connect-metamask/deployed-contract.png)

    A szerződésben meghatározott függvényekhez két művelet **hozzáadására** és **lekérésére** van lehetőség.

1. Ha blockchain szeretne **hozzáadni** egy tranzakciót, adja meg a hozzáadni kívánt számot, majd válassza a **Hozzáadás**lehetőséget. Előfordulhat, hogy a Remixből a gáz-becslési hiba üzenet jelenik meg. A tranzakciót olyan privát blockchain küldi, amely nem igényel gázt. Válassza a **tranzakció küldése** lehetőséget a tranzakció kényszerítéséhez.
1. A szerződés központi telepítésekor a MetaMask értesítés jelenik meg, amely figyelmezteti, hogy nem rendelkezik elegendő összeggel a tranzakció végrehajtásához.

    Mivel ez egy konzorcium privát hálózata, a gáz árát nulla értékre állíthatja.

1.  Válassza a **gáz díja > a > speciális szerkesztése**lehetőséget, állítsa a **gáz árát** 0 értékre, majd válassza a **Mentés**lehetőséget.
1. Válassza a **megerősítés** lehetőséget a tranzakció blockchain való elküldéséhez.
1. Válassza a **Get** művelet lehetőséget. Ez a csomópont-adat lekérdezési hívása. Nincs szükség tranzakcióra.
1. A Remix hibakeresés paneljén megtekintheti a tranzakciók részleteit a blockchain.

    ![Hibakeresési előzmények](./media/connect-metamask/debug.png)

    Megtekintheti az **egyszerű** szerződések létrehozását, a tranzakciót **egyszerűre. adja hozzá**, és hívja a **Simple. Get**.

1. A MetaMask a tranzakciók előzményeit is megtekintheti. Nyissa meg a MetaMask böngésző bővítményét.
1. Az **Előzmények** szakaszban láthatja a központilag telepített szerződések és tranzakciók naplóját.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a MetaMask böngésző bővítményét használta egy Azure Blockchain szolgáltatás-tranzakciós csomóponthoz való kapcsolódáshoz, egy intelligens szerződés üzembe helyezéséhez és egy tranzakció elküldéséhez a Blockchain. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum és a szarvasgomba használatával egy intelligens szerződési funkció egy tranzakción keresztül történő létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése a Visual Studio Code használatával](send-transaction.md)