---
title: A MetaMask csatlakoztatása Azure Blockchain service hálózathoz
description: Csatlakozzon egy Azure Blockchain Service-hálózathoz a MetaMask használatával, és telepítsen egy intelligens szerződést.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205111"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Rövid útmutató: Intelligens egyezmény csatlakoztatása és üzembe helyezése a MetaMask segítségével

Ebben a rövid útmutatóban a MetaMask segítségével csatlakozhat egy Azure Blockchain Service-hálózathoz, és a Remix segítségével intelligens szerződést helyezhet üzembe. Metamask egy böngésző kiterjesztés kezelésére Ether pénztárca és végre intelligens szerződés műveleteket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: Blockchain-tag létrehozása az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service Blockchain-tagot az Azure CLI használatával](create-member-cli.md)
* A [MetaMask böngészőbővítmény](https://metamask.io) telepítése
* MetaMask [pénztárca](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) létrehozása

## <a name="get-endpoint-address"></a>Végpont címének beszereznie

Az Azure Blockchain Service végpontcímére van szüksége a blockchain hálózathoz való csatlakozáshoz. A végpont címe és a hozzáférési kulcsok az Azure Portalon találhatók.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg az Azure Blockchain szolgáltatás tagját.
1. Válassza **a Tranzakciócsomópontok és** az alapértelmezett tranzakciócsomópont-kapcsolat lehetőséget.

    ![Alapértelmezett tranzakciócsomópont kiválasztása](./media/connect-metamask/transaction-nodes.png)

1. Válassza **a Kapcsolati karakterláncok > Access billentyűket.**
1. Másolja a végpont címét **https-ből (1. hozzáférési kulcs)**.

    ![Kapcsolati sztring](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Metamaszk csatlakoztatása

1. Nyissa meg a MetaMask böngészőbővítményt, és jelentkezzen be.
1. A hálózat legördülő menüben válassza az **Egyéni RPC**lehetőséget.

    ![Egyéni RPC](./media/connect-metamask/custom-rpc.png)

1. Az **Új hálózat > Új RPC URL-cím**beillesztése a fent másolt végpontcímet.
1. Kattintson a **Mentés** gombra.

    Ha a kapcsolat sikeres volt, a magánhálózat megjelenik a hálózat legördülő menüben.

    ![Új hálózat](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Intelligens szerződés üzembe helyezése

Remix egy böngésző-alapú Solidity fejlesztői környezetben. A MetaMask és a Remix együttes használatával intelligens szerződéseket helyezhet üzembe, és műveleteket is végrehajthat.

1. A böngészőjében lépjen a `https://remix.ethereum.org` helyre.
1. Válassza az **Új fájl** lehetőséget a **Kezdőlap** lap **Fájl csoportjában.**

    Nevezze el `simple.sol`az új fájlt .

    ![Fájl létrehozása](./media/connect-metamask/create-file.png)

    Válassza **az OK gombot.**
1. A Remix szerkesztőben illessze be a következő **egyszerű intelligens szerződéskódot.**

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

    Az **egyszerű szerződés** egy **egyenleg**nevű állapotváltozót deklarál. Két függvény van definiálva. A **hozzáadási** funkció egy számot ad hozzá az **egyensúlyhoz.** A **get** függvény az egyenleg értékét adja **eredményül.**
1. A szerződés fordításához először jelölje ki a Solidity compiler ablaktáblát, majd a **Fordítás simple.sol**lehetőséget.

    ![Összeállít](./media/connect-metamask/compile.png)

1. Válassza ki **a Központi telepítés & Futtatás** ablaktábláját, majd állítsa a **környezetet** **injektált web3-ra,** hogy a MetaMask segítségével csatlakozzon a blokklánc-taghoz.

    ![Futtatás lap](./media/connect-metamask/injected-web3.png)

1. Jelölje ki az **egyszerű** szerződést, majd **telepítse a szerződést.**

    ![Üzembe helyezés](./media/connect-metamask/deploy.png)


1. A MetaMask értesítés figyelmezteti Önt, hogy nincs elegendő pénz a tranzakció végrehajtásához.

    Egy nyilvános blockchain hálózat, akkor kell Ether fizetni a tranzakciós költség. Mivel ez egy konzorciummagánhálózata, a gázárat nullára állíthatja.

1.  Válassza **a Gázdíj > A szerkesztés > a Speciális**lehetőséget, állítsa a **gázárat 0-ra.**

    ![Gázár](./media/connect-metamask/gas-price.png)

    Kattintson a **Mentés** gombra.

1. Válassza **a Megerősítés** lehetőséget az intelligens szerződés blockchainre való üzembe helyezéséhez.
1. Az **Üzembe helyezett szerződések** szakaszban bontsa ki az **egyszerű** szerződést.

    ![Üzembe helyezett szerződés](./media/connect-metamask/deployed-contract.png)

    Két művelet, **hozzáadása** és **legete**, leképezése a szerződésben meghatározott függvényekhez.

1. Ha hozzá szeretne adni egy **tranzakciót** a blokkláncon, adjon meg egy hozzáadni kívánt számot, majd válassza a **Hozzáadás**lehetőséget. Előfordulhat, hogy a Remix gázbecslési hibaüzenetet kap: "A tranzakciót egy olyan privát blokkláncnak küldi, amely nem igényel gázt." Válassza **a Tranzakció küldése lehetőséget** a tranzakció kényszerítéséhez.
1. A szerződés üzembe helyezésekor a MetaMask értesítés figyelmezteti, hogy nincs elegendő pénz a tranzakció végrehajtásához.

    Mivel ez egy konzorciumi magánhálózat, a gázárat nullára állíthatjuk.

1. Válassza **a Gázdíj > A Szerkesztés > a Speciális**lehetőséget, állítsa a **Gázárat 0-ra,** és válassza a **Mentés**lehetőséget.
1. Válassza a **Megerősítés** lehetőséget, ha a tranzakciót a blokkláncba szeretné küldeni.
1. Válassza **a Művelet beszedése** lehetőséget. Ez a csomópontadatok lekérdezésére irányuló hívás. Nincs szükség tranzakcióra.

A Remix hibakeresési ablaktáblája a blokkláncon végrehajtott tranzakciók részleteit jeleníti meg:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Ha meg szeretné tekinteni a tranzakcióelőzményeket a MetaMask alkalmazásban, nyissa meg a MetaMask böngészőbővítményt, és keresse meg az **Előzmények** szakaszban az üzembe helyezett szerződés és tranzakciók naplóját.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a MetaMask böngészőbővítményt használta az Azure Blockchain szolgáltatás tranzakciós csomóponthoz való csatlakozáshoz, az intelligens szerződés üzembe helyezéséhez és a blokkláncba való küldéséhez. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum és a Truffle használatával intelligens szerződésfunkció létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához egy tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, létrehozása és üzembe helyezése az Azure Blockchain Szolgáltatásban](send-transaction.md)