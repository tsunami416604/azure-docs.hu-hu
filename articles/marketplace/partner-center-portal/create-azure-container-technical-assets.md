---
title: Készítse elő az Azure Container technikai eszközeit
description: Ez a cikk ismerteti a lépéseket és a követelmények egy tároló ajánlat konfigurálása az Azure Marketplace-en.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266018"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Készítse elő az Azure Container technikai eszközeit

> [!IMPORTANT]
> Az Azure Container-ajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a Container technikai eszközeinek előkészítése a Cloud Partner Portal számára az ajánlatok kezeléséhez című, a [Tároló technikai eszközeinek előkészítése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) című részben található utasításokat.

Ez a cikk ismerteti a lépéseket és a követelmények egy tároló ajánlat konfigurálása az Azure Marketplace-en.

## <a name="before-you-begin"></a>Előkészületek

A rövid útmutatók, oktatóanyagok és minták, lásd: [Azure Container Instances.](https://docs.microsoft.com/azure/container-instances)

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges.

A megoldástartománymellett a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

- Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete
- [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
- Az [Azure virtuális gépek,](https://azure.microsoft.com/services/virtual-machines/)az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking)
- Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) munkaismerete
- A [JSON](https://www.json.org/)munkaismerete.

## <a name="suggested-tools"></a>Javasolt eszközök

A tárolórendszerkép kezeléséhez válassza az alábbi parancsfájlok egyikét vagy mindkettőt:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Javasoljuk, hogy adja hozzá ezeket az eszközöket a fejlesztői környezethez:

- [Azure Storage Explorer](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/) lapon. Visual Studio használata esetén tekintse át a [Visual Studio Piactéren](https://marketplace.visualstudio.com/)elérhető eszközöket.

## <a name="create-the-container-image"></a>A tárolórendszerkép létrehozása

További információt az alábbi oktatóanyagokban talál:

- [Oktatóanyag: Hozzon létre egy tárolórendszerképet az Azure Container Instances üzembe helyezéséhez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Oktatóanyag: Az Azure Container Registry Tasks használatával tárolórendszerképeket hozhat létre és helyezhet üzembe a felhőben.](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-step"></a>Következő lépés

- [Hozza létre a container ajánlatot.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)
