---
title: Azure-beli virtuális gép technikai eszközének létrehozása
description: Ez a cikk a tárolók Azure Marketplace-en való konfigurálásának lépéseit és követelményeit ismerteti.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730635"
---
# <a name="create-an-azure-vm-technical-asset"></a>Azure-beli virtuális gép technikai eszközének létrehozása

> [!IMPORTANT]
> Az Azure-beli tárolók felügyeletét az Cloud Partner Portalról a partneri központba helyezi át. Az ajánlatok migrálása előtt kövesse a [tároló technikai eszközeinek előkészítése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) a Cloud Partner Portal az ajánlatok kezeléséhez című témakör útmutatását.

Ez a cikk a tárolók Azure Marketplace-en való konfigurálásának lépéseit és követelményeit ismerteti.

## <a name="before-you-begin"></a>Előkészületek

A rövid útmutatók, oktatóanyagok és minták esetében lásd: [Azure Container instances](https://docs.microsoft.com/azure/container-instances).

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

## <a name="next-step"></a>Következő lépés

- [Hozza létre a tároló ajánlatát](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
