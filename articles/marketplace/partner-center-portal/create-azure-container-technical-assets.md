---
title: Az Azure Container által kínált technikai fogalmak – Microsoft kereskedelmi piactér
description: Technikai erőforrások és irányelvek a tárolók Azure Marketplace-en való konfigurálásának megkönnyítéséhez.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700979"
---
# <a name="create-an-azure-container-offer"></a>Azure Container-ajánlat létrehozása

Ez a cikk az Azure Marketplace-en elérhető tárolók létrehozására szolgáló technikai forrásokat és javaslatokat tartalmaz.

## <a name="before-you-begin"></a>Előkészületek

A gyors útmutatókkal, oktatóanyagokkal és mintákkal kapcsolatban tekintse meg a [Azure Container instances dokumentációját](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.

A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure hálózatkezelésének](https://azure.microsoft.com/services/?filter=networking) gyakorlati ismerete
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
- A [JSON](https://www.json.org/)működésének ismerete.

## <a name="suggested-tools"></a>Javasolt eszközök

A tároló rendszerképének kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)-vel.

Javasoljuk, hogy adja hozzá ezeket az eszközöket a fejlesztési környezethez:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Tekintse át az elérhető eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/) oldalon. Ha a Visual studiót használja, tekintse át a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en elérhető eszközöket.

## <a name="create-the-container-image"></a>A tároló rendszerképének létrehozása

További információkért tekintse meg a következő oktatóanyagokat:

- [Oktatóanyag: tároló-rendszerkép létrehozása a Azure Container Instances való üzembe helyezéshez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Oktatóanyag: tároló-lemezképek létrehozása és üzembe helyezése a felhőben Azure Container Registry feladatokkal](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>További lépések

- [Hozza létre a tároló ajánlatát](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
