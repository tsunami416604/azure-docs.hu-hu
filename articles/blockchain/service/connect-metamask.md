---
title: Az Azure Blockchain-szolgáltatás hálózat MetaMask csatlakoztatása
description: Az Azure Blockchain-szolgáltatás hálózati MetaMask használatával csatlakozhat, és üzembe helyezése egy intelligens szerződést.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: db029cee6edcd14d29c83964e5bf75aa45077e7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026899"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Gyors útmutató: MetaMask használatával csatlakozhat, és a egy intelligens szerződés üzembe helyezése

Ez a rövid útmutatóban az Azure Blockchain-szolgáltatás hálózati csatlakozhat, és üzembe helyezése egy intelligens szerződés Remix használatával MetaMask fogja használni. Metamask egy webböngésző-bővítmény egy távolítsuk wallet kezeléséhez, és végezze el az intelligens szerződés műveleteket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Blockchain tag létrehozása](create-member.md)
* Telepítés [MetaMask böngészőbővítmény](https://metamask.io)
* Hozzon létre egy MetaMask [wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Végpont-címének lekéréséhez

Az Azure Blockchain-szolgáltatás végpont cím a blockchain-hálózathoz való kapcsolódáshoz szükséges. Megtalálhatja a végpont címe és hozzáférési kulcsok az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain-szolgáltatás tag. Válassza ki **tranzakció csomópontok** és az alapértelmezett tranzakció csomópont hivatkozására.

    ![Válassza ki az alapértelmezett tranzakció csomópont](./media/connect-metamask/transaction-nodes.png)

1. Válassza ki **kapcsolati karakterláncok > hozzáférési kulcsok**.
1. Másolja a végpont címét **HTTPS (hozzáférési kulcs: 1)**. A cím a következő szakaszban szükség van.

    ![Kapcsolati sztring](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Csatlakozás MetaMask

1. Nyissa meg a MetaMask böngészőbővítmény, és jelentkezzen be.
1. A hálózati legördülő listában válassza ki a **egyéni RPC**.

    ![Egyéni RPC](./media/connect-metamask/custom-rpc.png)

1. A **új hálózat > új RPC URL-cím**, adja meg a végpont címe az előző szakaszban másolt.
1. Kattintson a **Mentés** gombra.

    Ha a kapcsolódás sikeres volt, a magánhálózaton a hálózat legördülő listában jelenik meg.

    ![Új hálózati](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Az intelligens szerződés üzembe helyezése

Remix egy böngészőalapú Solidity fejlesztési környezetet. MetaMask és segítségével Remix együtt, telepítheti és az intelligens szerződések műveleteket.

1. A böngészőjében lépjen a `https://remix.ethereum.org` helyre.
1. Válassza a **Futtatás** lehetőséget. 

    MetaMask beállítása a **környezet** való **szúrhatja be a weben 3** és **fiók** a hálózathoz.

    ![A Futtatás lap](./media/connect-metamask/injected-web3.png)

1. Válassza ki **új fájl létrehozása**.

    Nevezze el az új fájl `simple.sol`.

    ![Fájl létrehozása](./media/connect-metamask/create-file.png)

    Kattintson az **OK** gombra.

1. A Remix szerkesztőben illessze be a következő **egyszerű intelligens szerződés** kódot.

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

    A **egyszerű szerződés** nevű állapot változó deklarálása **egyenleg**. Nincsenek definiálva két függvényt. A **hozzáadása** függvény egy számot ad **egyenleg**. A **első** értékét adja vissza a függvény **egyenleg**.

1. Fordítása a szerződést, válassza ki a **összeállítása > Start fordítása**. Sikeres művelet esetén egy zöld szerződésnévvel rendelkező jelenik meg.

    ![Compile](./media/connect-metamask/compile.png)

1. A szerződés végrehajtásához válassza ki a **futtatása** fülre. Válassza ki a **egyszerű** majd szerződés **telepítés**.

    ![Egyéni RPC](./media/connect-metamask/deploy.png)

1. MetaMask megjelenik egy értesítés a tranzakció végrehajtásához nincs elég alapok értesítést küld.

    Egy nyilvános blockchain-hálózatot kell fizetnie a tranzakciós költség távolítsuk. Mivel ez egy magánhálózaton lévő konzorcium, beállíthatja a gáz ár nulla.

1.  Válassza ki **gáz díj > szerkesztése > speciális**állítsa be a **gáz ár** 0-ra.

    ![Gáz ár](./media/connect-metamask/gas-price.png)

    Kattintson a **Mentés** gombra.

1. Válassza ki **megerősítése** központi telepítése a blockchain a intelligens szerződést.
1. Az a **üzembe helyezett szerződések** szakaszban bontsa ki a **egyszerű** szerződés.

    ![Üzembe helyezett szerződés](./media/connect-metamask/deployed-contract.png)

    Két művelet **hozzáadása** és **első** , amelyek leképezése a szerződésben meghatározott függvényeket.

1. Végrehajtásához egy **hozzáadása** tranzakció blokkláncon, adjon meg egy számot adja meg, majd válassza ki **hozzáadása**.
1. Amikor központilag telepítette a szerződés hasonlóan, MetaMask megjelenik egy értesítés a tranzakció végrehajtásához nincs elég alapok értesítést küld.

    Mivel ez egy magánhálózaton lévő konzorcium, azt is nullára gáz ár.

1.  Válassza ki **gáz díj > szerkesztése > speciális**állítsa be a **gáz ár** 0, és válassza **mentése**.
1. Válassza ki **megerősítése** a blockchain a tranzakció végrehajtásához.
1. Válassza ki **első** művelet. Ez a csomópont adatokat lekérdezni egy hívás. A tranzakció nem szükséges.
1. A blockchain a Remix hibakeresési panelén láthatja a tranzakció részleteit.

    ![Előzmények hibakeresése](./media/connect-metamask/debug.png)

    Megtekintheti a **egyszerű** létrehozását, a tranzakciós szerződés **simple.add**, és irányuló hívás **simple.get**.

1. Megtekintheti a tranzakciós előzményekben a MetaMask is. Nyissa meg a MetaMask webböngésző-bővítmény.
1. Az a **előzmények** szakaszban láthatja, hogy a telepített szerződés és a tranzakciók naplózása.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban a MetaMask webböngésző-bővítmény az Azure Blockchain-szolgáltatás tranzakció a csomóponthoz csatlakozás, üzembe helyezését egy intelligens szerződés és egy tranzakció küldeni a blockchain használt. Próbálja ki a következő oktatóanyaggal, üzembe helyezése és a egy tranzakció Truffle használatával küldjön.

> [!div class="nextstepaction"]
> [Egy tranzakció küldése](send-transaction.md)