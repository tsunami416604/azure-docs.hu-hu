---
title: 'Azure Containers létrehozása technikai eszközök : Kapcsolatkép | Azure Piactér'
description: Hozzon létre egy Azure-tároló technikai eszközeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280065"
---
# <a name="prepare-your-container-technical-assets"></a>Tárolók technikai eszközeinek előkészítése

Ez a cikk ismerteti az Azure Marketplace-en egy tárolóajánlat konfigurálásának lépéseit és követelményeit.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át az [Azure Container Instances dokumentációt,](https://docs.microsoft.com/azure/container-instances) amely rövid útmutatókat, oktatóanyagokat és mintákat tartalmaz.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges.
 
A megoldástartománymellett a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

-   Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete 
-   [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
-   Az [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), az Azure [Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking)
-   Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) munkaismerete
-   A [JSON](https://www.json.org/) munkaismerete

## <a name="suggested-tools"></a>Javasolt eszközök

A tárolórendszerkép kezeléséhez válasszon az alábbi parancsfájlok egyikének vagy mindkettőnek:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Ezenkívül azt javasoljuk, hogy adja hozzá a következő eszközöket a fejlesztői környezethez:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio kód](https://code.visualstudio.com/)
    *   Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/tools/) lapon, és ha a Visual Studio t használja, a Visual Studio [Piacteret.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>A tárolórendszerkép létrehozása

További információt az alábbi témakörben talál:

* [Oktatóanyag: Hozzon létre egy tárolórendszerképet az Azure Container Instances üzembe helyezéséhez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Oktatóanyag: Tárolórendszerképek létrehozása és üzembe helyezése a felhőben az Azure Container Registry Tasks segítségével](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>További lépések

[A tárolóajánlat létrehozása](./cpp-create-offer.md)
