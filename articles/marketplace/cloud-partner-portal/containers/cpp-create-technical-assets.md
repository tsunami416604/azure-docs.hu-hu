---
title: Azure-tárolók rendszerképének létrehozása – technikai eszközök | Azure piactér
description: Hozzon létre egy Azure-tárolóhoz tartozó technikai eszközöket.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819053"
---
# <a name="prepare-your-container-technical-assets"></a>A tároló technikai eszközeinek előkészítése

Ez a cikk a tárolók Azure Marketplace-hez való konfigurálásának lépéseit és követelményeit ismerteti.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a [Azure Container instances](https://docs.microsoft.com/azure/container-instances) dokumentációját, amely rövid útmutatókat, oktatóanyagokat és mintákat tartalmaz.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Az adategységek tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.
 
A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

-   Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése 
-   [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
-   Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking) együttműködésének ismerete
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
-   A [JSON](https://www.json.org/) működésének ismerete

## <a name="suggested-tools"></a>Javasolt eszközök

A tároló rendszerképének kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Emellett javasoljuk a következő eszközök hozzáadását a fejlesztői környezethez:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon elérhető eszközöket, és ha a Visual studiót használja, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="create-the-container-image"></a>A tároló rendszerképének létrehozása

További információért tekintse meg a következőket:

* [Oktatóanyag: tároló-rendszerkép létrehozása a Azure Container Instances való üzembe helyezéshez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Oktatóanyag: tároló lemezképek létrehozása és üzembe helyezése a felhőben Azure Container Registry feladatokkal](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>További lépések

[A tároló ajánlat létrehozása](./cpp-create-offer.md)
