---
title: Azure-alkalmazások technikai eszközeinek létrehozása | Azure piactér
description: Hozzon létre egy Azure-alkalmazási ajánlathoz tartozó technikai eszközöket.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827601"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Az Azure-alkalmazások technikai eszközeinek előkészítése

Ez a cikk az Azure-alkalmazási ajánlat technikai eszközeinek előkészítéséhez szükséges erőforrásokat ismerteti.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át az alábbi videót, hozzon létre [megoldási sablonokat és felügyelt alkalmazásokat az Azure Marketplace-hez](https://channel9.msdn.com/Events/Build/2018/BRK3603), amely áttekintést nyújt egy Azure Resource Manager-sablon létrehozásáról egy Azure-alkalmazási megoldás definiálásához, majd a későbbi közzétételéhez az alkalmazás ajánlata az Azure Marketplace-en.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Tekintse át a következő Azure-alkalmazás dokumentációját, amely rövid útmutatók, oktatóanyagok és minták áttekintését nyújtja.

- [Azure Resource Manager sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Rövid útmutatók

  - [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates)
  - [Alkalmazás-definíció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [A Service Catalog alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Oktatóanyagok:

  - [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Minták

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Az adategységek tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.

A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure hálózatkezelésének](https://azure.microsoft.com/services/?filter=networking) gyakorlati ismerete
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
- A [JSON](https://www.json.org/) működésének ismerete

## <a name="suggested-tools"></a>Javasolt eszközök

Az Azure-alkalmazás kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Javasoljuk, hogy a következő eszközöket adja hozzá a fejlesztői környezethez:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) a következő kiterjesztésekkel:

  - Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon elérhető eszközöket, és ha a Visual studiót használja, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="next-steps"></a>További lépések

[Azure-alkalmazásajánlat létrehozása](./cpp-create-offer.md)

