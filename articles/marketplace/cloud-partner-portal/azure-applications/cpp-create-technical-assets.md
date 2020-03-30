---
title: Azure-alkalmazás technikai eszközeinek létrehozása | Azure Piactér
description: Hozzon létre egy Azure-alkalmazásajánlat technikai eszközeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285265"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Készítse elő az Azure-alkalmazás technikai eszközeit

Ez a cikk ismerteti az erőforrásokat az Azure-alkalmazásajánlat technikai eszközeinek előkészítéséhez.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a következő videót, a [Building Solution Templates és a Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)című videót, amely áttekintést nyújt arról, hogyan írhat meg egy Azure Resource Manager-sablont egy Azure-alkalmazásmegoldás meghatározásához, majd hogyan teheti közzé ezt követően az alkalmazásajánlatot az Azure Piactéren.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Tekintse át az alábbi Azure-alkalmazás dokumentációt, amely rövid útmutatókat, oktatóanyagokat és mintákat tartalmaz.

- [Az Azure Resource Manager-sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Gyorsútmutatók:

  - [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure gyorsindítási sablonjai](https://github.com/azure/azure-quickstart-templates)
  - [Alkalmazásdefiníció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Szolgáltatáskatalógusban elérhető alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Oktatóanyagok:

  - [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Minták:

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges.

A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

- Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete
- [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
- Az [Azure virtuális gépek,](https://azure.microsoft.com/services/virtual-machines/)az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking)
- Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) munkaismerete
- A [JSON](https://www.json.org/) munkaismerete

## <a name="suggested-tools"></a>Javasolt eszközök

Az Azure-alkalmazás kezeléséhez válasszon az alábbi parancsfájl-kezelési környezetek egyikét vagy mindkettőt:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Javasoljuk, hogy adja hozzá a következő eszközöket a fejlesztői környezethez:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio-kód](https://code.visualstudio.com/) a következő bővítményekkel:

  - Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/tools/) lapon, és ha a Visual Studio t használja, a Visual Studio [Piacteret.](https://marketplace.visualstudio.com/)

## <a name="next-steps"></a>További lépések

[Azure-alkalmazásajánlat létrehozása](./cpp-create-offer.md)

