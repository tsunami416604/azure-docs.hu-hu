---
title: Intelligens szerződések létrehozása, összeállítása & üzembe helyezése oktatóanyag – Azure Blockchain Szolgáltatás
description: Oktatóanyag arról, hogyan használhatja az Azure Blockchain Development Kit for Ethereum bővítményt a Visual Studio Code-ban intelligens szerződés létrehozásához, létrehozásához és üzembe helyezéséhez az Azure Blockchain szolgáltatásban.
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74972852"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Oktatóanyag: Intelligens szerződések létrehozása, létrehozása és üzembe helyezése az Azure Blockchain szolgáltatásban

Ebben az oktatóanyagban használja az Azure Blockchain Development Kit for Ethereum bővítményt a Visual Studio Code-ban intelligens szerződés létrehozásához, létrehozásához és üzembe helyezéséhez az Azure Blockchain Szolgáltatáson. A fejlesztői készlet segítségével intelligens szerződésfüggvényt is végrehajthat egy tranzakción keresztül.

Az Azure Blockchain Development Kit for Ethereum segítségével:

> [!div class="checklist"]
> * Intelligens szerződés létrehozása
> * Intelligens szerződés üzembe helyezése
> * Intelligens szerződésfunkció végrehajtása tranzakción keresztül

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: A Visual Studio-kód használatával csatlakozhat az Azure Blockchain Service konzorciumi hálózatához](connect-vscode.md)
* [Visual Studio kód](https://code.visualstudio.com/Download)
* [Azure Blockchain fejlesztői készlet az Ethereum bővítményhez](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x vagy újabb](https://nodejs.org/download)
* [Git 2.10.x vagy újabb](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Add hozzá a python.exe-t az útvonaladhoz. Python 2.7.15-ös verziója az útvonalon van szükség az Azure Blockchain Development Kit.
* [Szarvasgomba 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows rendszeren telepítve van c++ fordító a csomópont-gyp modulhoz. Az MSBuild eszközöket használhatja:

* Ha a Visual Studio 2017 telepítve van, konfigurálja az npm-et úgy, hogy az MSBuild eszközöket használja a paranccsal`npm config set msvs_version 2017 -g`
* Ha a Visual Studio 2019 telepítve van, állítsa be az MS buildeszközök elérési útját az npm-hez. Például: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Ellenkező esetben telepítse az önálló VS `npm install --global windows-build-tools` Build eszközöket egy emelt szintű *Futtatás rendszergazdai parancshéjként* használatával.

A csomópont-gyp-ről további információt a [GitHub csomópont-gyp tárházában](https://github.com/node-gyp)talál.

## <a name="create-a-smart-contract"></a>Intelligens szerződés létrehozása

Az Azure Blockchain Development Kit for Ethereum projektsablonokat és szarvasgomba-eszközöket használ a szerződések állványzatának, létrehozásának és üzembe helyezésének elősegítésére. Mielőtt elkezdené, végezze el az [előfeltételi rövid útmutatót: A Visual Studio-kód használatával csatlakozzon egy Azure Blockchain Service konzorciumi hálózathoz.](connect-vscode.md) A rövid útmutató végigvezeti az Azure Blockchain Development Kit for Ethereum telepítésén és konfigurálásán.

1. A VS Code parancspaletta közül válassza az **Azure Blockchain: New Solidity Project (Új szilárdtest-projekt) lehetőséget.**
1. Válassza **az Alapprojekt létrehozása**lehetőséget.
1. Hozzon létre `HelloBlockchain` egy új nevű mappát, és **válassza ki az új projektelérési utat**.

Az Azure Blockchain fejlesztői készlet létrehoz és inicializál egy új Solidity projektet. Az alapprojekt tartalmaz egy minta **HelloBlockchain** intelligens szerződést, és az összes szükséges fájlt az Azure Blockchain Szolgáltatás konzorciumi tagjának létrehozásához és üzembe helyezéséhez. A projekt létrehozása több percig is eltarthat. Figyelheti a haladást a VS Code terminálpanelen az Azure Blockchain kimenetének kiválasztásával.

A projektszerkezet a következő példához hasonlóan néz ki:

   ![Szilárdsági projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Intelligens szerződés létrehozása

Az intelligens szerződések a projekt **szerződési** könyvtárában találhatók. Intelligens szerződéseket állít össze, mielőtt telepíti őket egy blokkláncba. A **Szerződések összeállítása** paranccsal lefordíthatja a projekt összes intelligens szerződését.

1. A VS Code explorer oldalsávján bontsa ki a **szerződések** mappát a projektben.
1. Kattintson a jobb gombbal **a HelloBlockchain.sol** elemre, és válassza a menü **Szerződésösszeállítás a parancsát.**

    ![A Létrehozás menü kiválasztása ](./media/send-transaction/build-contracts.png)

Az Azure Blockchain Development Kit szarvasgomba segítségével állítja össze az intelligens szerződéseket.

![Szarvasgomba fordító kimenet](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Intelligens szerződés üzembe helyezése

A Szarvasgomba áttelepítési parancsfájlok segítségével telepíti a szerződéseket egy Ethereum-hálózatra. Az áttelepítések a projekt **áttelepítési** könyvtárában található JavaScript-fájlok.

1. Az intelligens szerződés üzembe helyezéséhez kattintson a jobb gombbal **a HelloBlockchain.sol** elemre, és válassza a **menü Szerződések telepítése parancsát.**
1. Válassza ki az Azure Blockchain konzorciumi hálózatát a parancspalettán. A konzorciumi blokklánc-hálózat a projekt létrehozásakor került be a projekt Szarvasgomba-konfigurációs fájljába.
1. Válassza **a Mnemonic létrehozása**lehetőséget. Válasszon egy fájlnevet, és mentse a mnemonikus fájlt a projekt mappájába. Például: `myblockchainmember.env`. A mnemonikus fájl segítségével létrehoz egy Ethereum titkos kulcsot a blockchain tag.

Az Azure Blockchain Development Kit szarvasgomba használatával hajtja végre az áttelepítési parancsfájlt a szerződések blockchainre való üzembe helyezéséhez.

![Sikeresen üzembe helyezett szerződés](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Szerződésfüggvény hívása

A **HelloBlockchain-egyezmény** **SendRequest** függvénye módosítja a **RequestMessage** állapotváltozót. A blokklánc-hálózat állapotának módosítása tranzakción keresztül történik. Az Azure Blockchain Development Kit intelligens szerződés interakciós lap használatával meghívhatja a **SendRequest** függvényt egy tranzakción keresztül.

1. Az intelligens szerződéssel való interakcióhoz kattintson a jobb gombbal a **HelloBlockchain.sol** elemre, és válassza a menü **Intelligens szerződésinterakciós oldal megjelenítése** parancsát.

    ![Válassza az Intelligens egyezmény interakciós oldalának megjelenítése parancsot a menüből](./media/send-transaction/contract-interaction.png)

1. A kapcsolati tevékenység lapja lehetővé teszi az üzembe helyezett szerződésverzió, a hívási funkciók, az aktuális állapot megtekintését és a metaadatok megtekintését.

    ![Példa intelligens egyezmény kapcsolati tevékenységi lapja](./media/send-transaction/interaction-page.png)

1. Az intelligens szerződésfüggvény hívásához jelölje ki a szerződésműveletet, és adja át az argumentumokat. Válassza **a SendRequest** szerződés műveletet, és adja **hello, Blockchain!** a **requestMessage** paraméterhez. Válassza a **Végrehajtás** lehetőséget a **SendRequest** függvény tranzakción keresztüli hívásához.

    ![SendRequest művelet végrehajtása](./media/send-transaction/sendrequest-action.png)

A tranzakció feldolgozása után a kapcsolati tevékenység szakasz az állapotváltozásokat tükrözi.

![Szerződésállapot-módosítások](./media/send-transaction/contract-state.png)

A SendRequest függvény beállítja a **RequestMessage** és **az Állapot** mezőket. A **RequestMessage** jelenlegi állapota az az érv, amelyet átadott **a Hello, Blockchain**. Az **Állapot** mező értéke a **Kérés marad.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az `myResourceGroup` erőforrásokat a létrehozott erőforráscsoport törlésével a *Blokklánc-tag* előfeltételek gyorsindítása.

Az erőforráscsoport törlése:

1. Az Azure Portalon keresse meg az **Erőforráscsoportot** a bal oldali navigációs ablakban, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy minta Solidity projektet az Azure Blockchain Development Kit használatával. Intelligens szerződést épített ki és helyezett üzembe, amelyet az Azure Blockchain Szolgáltatáson üzemeltetett blockchain konzorciumi hálózaton keresztül egy tranzakciónak nevezett.

> [!div class="nextstepaction"]
> [Blockchain alkalmazások fejlesztése az Azure Blockchain szolgáltatás használatával](develop.md)
