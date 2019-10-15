---
title: Az Azure Blockchain Development Kit for Ethereum – Azure Blockchain Service használata
description: Kapcsolódás Azure Blockchain Service Consortium-hálózathoz a Visual Studio Code-ban található Ethereum-bővítményhez készült Azure Blockchain Development Kit használatával
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329305"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Gyors útmutató: Azure Blockchain Service Consortium-hálózathoz való kapcsolódás a Visual Studio Code használatával

Ebben a rövid útmutatóban a Ethereum Visual Studio Code-bővítményhez készült Azure Blockchain Development Kit használatával telepítheti és használhatja az Azure Blockchain szolgáltatásban található konzorciumhoz való csatolást. Az Azure Blockchain Development Kit leegyszerűsíti a Ethereum-főkönyvekben található intelligens szerződések létrehozását, összekapcsolását, létrehozását és üzembe helyezését. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Az Azure Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Adja hozzá a Python. exe fájlt az elérési úthoz. Az Azure Blockchain Development Kit esetében a Python elérési útja szükséges.
* [Szarvasgomba](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Az Azure Blockchain Development Kit-környezet ellenőrzése

Az Azure Blockchain Development Kit ellenőrzi, hogy teljesülnek-e a fejlesztési környezet előfeltételei. A fejlesztési környezet ellenőrzése:

A VS Code parancs palettáján válassza az **Azure Blockchain: Kezdőlap megjelenítése**lehetőséget.

Az Azure Blockchain Development Kit olyan érvényesítési parancsfájlt futtat, amely körülbelül egy percet vesz igénybe. Megtekintheti a kimenetet a **terminal > új terminál**lehetőség kiválasztásával. A terminál menüsorában válassza a **kimenet** fület és az **Azure Blockchain** a legördülő menüben. A sikeres érvényesítés az alábbi képhez hasonlóan néz ki:

![Érvényes fejlesztői környezet](./media/connect-vscode/valid-environment.png)

 Ha hiányzik egy szükséges eszköz, az **Azure Blockchain Development Kit – Preview** nevű új lap felsorolja a telepíteni kívánt alkalmazásokat és az eszközök letöltésére vonatkozó hivatkozásokat.

![Fejlesztői csomaghoz szükséges alkalmazások](./media/connect-vscode/required-apps.png)

A rövid útmutató folytatása előtt telepítse a hiányzó előfeltételeket.

## <a name="connect-to-consortium-member"></a>Kapcsolódás a konzorcium tagjához

A konzorcium tagjaihoz az Azure Blockchain Development Kit VS Code bővítmény használatával csatlakozhat. A konzorciumhoz való csatlakozás után intelligens szerződéseket állíthat össze, építhet ki és helyezhet üzembe egy Azure Blockchain Service Consortium-tag számára.

Ha nincs hozzáférése az Azure Blockchain Service Consortium egyik tagjához, hajtsa végre az előfeltételként szükséges rövid útmutatót [: hozzon létre egy Blockchain-tagot a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI használatával ](create-member-cli.md).

1. A Visual Studio Code (VS Code) Explorer ablaktáblán bontsa ki az **Azure Blockchain** bővítményt.
1. Válassza **a kapcsolódás konzorciumhoz**lehetőséget.

   ![Kapcsolódás a konzorciumhoz](./media/connect-vscode/connect-consortium.png)

    Ha az Azure-hitelesítésre kéri, kövesse az utasításokat a böngészőben való hitelesítéshez.
1. Válassza a **Kapcsolódás az Azure Blockchain Service consortiumhoz** lehetőséget a parancssor legördülő menüben.
1. Válassza ki az Azure Blockchain Service Consortium-taghoz társított előfizetést és erőforráscsoportot.
1. Válassza ki a konzorciumot a listából.

A konzorcium és a blockchain tagjai a Visual Studio Explorer oldalsó sávján jelennek meg.

![A konzorcium megjelenik az Explorerben](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Azure Blockchain fejlesztői készletet használta a Ethereum Visual Studio Code-bővítményhez, hogy az Azure Blockchain Service-ben lévő konzorciumhoz csatoljon. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum és a szarvasgomba használatával egy intelligens szerződési funkció egy tranzakción keresztül történő létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése a Visual Studio Code használatával](send-transaction.md)