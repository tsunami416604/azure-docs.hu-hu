---
title: Technikai eszközök Azure-alkalmazás létrehozása |} Az Azure Marketplace-en
description: Hozzon létre egy Azure-alkalmazás ajánlat technikai eszközök.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942983"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Az Azure-alkalmazás technikai eszközök előkészítése

Ez a cikk bemutatja az erőforrások a technikai eszközök előkészítése az Azure-alkalmazás ajánlatban.

## <a name="before-you-begin"></a>Előzetes teendők

Tekintse át a következő videót, [létrehozása Megoldássablonokkal, és a felügyelt alkalmazások az Azure Marketplace-en](https://channel9.msdn.com/Events/Build/2018/BRK3603), hogyan hozzon létre egy Azure Resource Manager-sablon egy Azure application megoldás, és ezután hogyan meghatározásához áttekintése Ezt követően tegye közzé az alkalmazást az ajánlat az Azure piactéren.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Tekintse át a következő Azure-alkalmazás dokumentációját, amely biztosítja az útmutatóink, Oktatóanyagaink és Példáink segítségével.

- [Megismerheti az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Gyorsútmutatók:

  - [Az Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/)
  - [Azure-Gyorssablonok GitHub-sablonok](https://github.com/azure/azure-quickstart-templates)
  - [Felügyeltalkalmazás-definíció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Szolgáltatási katalógus-alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Oktatóanyagok:

  - [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Példák:

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Alapvető technikai ismereteit

Tervezése, létrehozása és tesztelése, ezek az eszközök időt vesz igénybe, és az Azure platform és a hozhat létre az ajánlat technológiák műszaki ismerete szükséges.

A mérnöki csapathoz a következő Microsoft-technológiákkal kapcsolatos alapos kell rendelkeznie:

- Alapvető ismeretekkel [Azure-szolgáltatások](https://azure.microsoft.com/services/)
- Hogyan [tervezését és tervezhet meg Azure-alkalmazások](https://azure.microsoft.com/solutions/architecture/)
- Gyakorlati ismerete [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage), és [az Azure-hálózatot](https://azure.microsoft.com/services/?filter=networking)
- Gyakorlati ismerete [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Gyakorlati ismerete [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Javasolt eszközök

Válassza ki az egyik vagy mindkét kezeléséhez az Azure-alkalmazásokat a következő parancsfájl-kezelési környezetekben:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Javasoljuk, hogy a következő eszközöket ad hozzá a fejlesztési környezetet:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [A Visual Studio Code](https://code.visualstudio.com/) a következő kiterjesztésű:

  - Mellék: [Azure Resource Manager-eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Mellék: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Mellék: [JSON prettify](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Emellett javasoljuk, hogy az elérhető eszközök áttekintése a [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldal és a Visual Studio használata a [Visual Studio-piactér](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>További lépések

[Azure-alkalmazásajánlat létrehozása](./cpp-create-offer.md)

