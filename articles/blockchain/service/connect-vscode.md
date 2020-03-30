---
title: Az Azure Blockchain szolgáltatáshoz való csatlakozás hoz a Visual Studio-kód dal
description: Csatlakozás Egy Azure Blockchain Service konzorciumi hálózathoz az Azure Blockchain Development Kit for Ethereum bővítmény használatával a Visual Studio-kódban
ms.date: 03/26/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 269a3e419186daddf1e6a41a54e52e688f4bc7e3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348579"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Rövid útmutató: A Visual Studio-kód használatával csatlakozhat egy Azure Blockchain Service konzorciumi hálózathoz

Ebben a rövid útmutatóban telepíti és használhatja az Azure Blockchain Development Kit for Ethereum Visual Studio Code (VS Code) bővítményt, hogy az Azure Blockchain Szolgáltatás konzorciumához kapcsolódjon. Az Azure Blockchain Development Kit leegyszerűsíti az intelligens szerződések létrehozásának, csatlakoztatásának, létrehozásának és üzembe helyezésének módját az Ethereum blockchain főkönyvekben.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: Blockchain-tag létrehozása az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service Blockchain-tagot az Azure CLI használatával](create-member-cli.md)
* [Visual Studio kód](https://code.visualstudio.com/Download)
* [Azure Blockchain fejlesztői készlet az Ethereum bővítményhez](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x vagy újabb](https://nodejs.org)
* [Git 2.10.x vagy újabb](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Add hozzá a python.exe-t az útvonaladhoz. A Python 2.7.15-ös verziója az útvonalon van szükség az Azure Blockchain Development Kit.
* [Szarvasgomba 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows rendszeren telepítve van c++ fordító a csomópont-gyp modulhoz. Az MSBuild eszközöket használhatja:

* Ha a Visual Studio 2017 telepítve van, konfigurálja az npm-et úgy, hogy az MSBuild eszközöket használja a paranccsal`npm config set msvs_version 2017 -g`
* Ha a Visual Studio 2019 telepítve van, állítsa be az MS buildeszközök elérési útját az npm-hez. Például: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Ellenkező esetben telepítse az önálló VS `npm install --global windows-build-tools` Build eszközöket egy emelt szintű *Futtatás rendszergazdai parancshéjként* használatával.

A csomópont-gyp-ről további információt a [GitHub csomópont-gyp tárházában](https://github.com/node-gyp)talál.

### <a name="verify-azure-blockchain-development-kit-environment"></a>Az Azure Blockchain Development Kit környezet ellenőrzése

Az Azure Blockchain Development Kit ellenőrzi, hogy a fejlesztői környezet előfeltételei teljesültek-e. A fejlesztői környezet ellenőrzése:

A VS Code parancspaletta közül válassza az **Azure Blockchain: Welcome Page megjelenítése**lehetőséget.

Az Azure Blockchain Development Kit egy érvényesítési parancsfájlt futtat, amely körülbelül egy percet vesz igénybe. A kimenetet az **Új terminál >** kiválasztásával tekintheti meg. A terminál menüsorában válassza a **Kimenet** és az **Azure Blockchain** elemet a legördülő menüben. A sikeres ellenőrzés a következő képhez hasonlóan néz ki:

![Érvényes fejlesztői környezet](./media/connect-vscode/valid-environment.png)

 Ha hiányzik egy szükséges eszköz, egy új lap nevű **Azure Blockchain Development Kit - Preview** felsorolja a szükséges eszközöket a letöltési linkeket.

![Fejlesztői készlet szükséges alkalmazások](./media/connect-vscode/required-apps.png)

A rövid útmutató folytatása előtt telepítse a hiányzó előfeltételeket.

## <a name="connect-to-consortium-member"></a>Csatlakozás konzorciumi taghoz

Az Azure Blockchain Development Kit VS Code bővítmény használatával csatlakozhat a konzorciumi tagokhoz. Miután csatlakozott egy konzorciumhoz, intelligens szerződéseket fordíthat, hozhat létre és helyezhet üzembe egy Azure Blockchain Service-konzorciumtagnak.

Ha nem fér hozzá az Azure Blockchain Service konzorciumának egy tagjához, végezze el az előfeltételhez szükséges [rövid útmutatót: Hozzon létre egy blokklánc-tagot az Azure Portalon](create-member.md) vagy a [gyorsútmutatóban: Hozzon létre egy Azure Blockchain Service blockchain-tagot az Azure CLI használatával.](create-member-cli.md)

1. A VS Code intéző ablaktáblán bontsa ki az **Azure Blockchain** bővítményt.
1. Válassza **a Csatlakozás a hálózathoz**lehetőséget.

   ![Csatlakozás a hálózathoz](./media/connect-vscode/connect-consortium.png)

    Ha a rendszer kéri az Azure-hitelesítést, kövesse az utasításokat a böngésző használatával történő hitelesítéshez.
1. Válassza az **Azure Blockchain Szolgáltatás** a parancspaletta legördülő menüben.
1. Válassza ki az Azure Blockchain Service konzorciumi tagjához társított előfizetési és erőforráscsoportot.
1. Válassza ki a konzorciumot a listából.

A konzorcium és a blokklánc tagjai a VS Code explorer oldalsávján vannak felsorolva.

![Konzorcium jelenik meg a felfedező](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Azure Blockchain Development Kit for Ethereum VS Code bővítményt használta az Azure Blockchain Szolgáltatás konzorciumához való csatoláshoz. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum használatával intelligens szerződésfunkció létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához egy tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, létrehozása és üzembe helyezése az Azure Blockchain Szolgáltatásban](send-transaction.md)