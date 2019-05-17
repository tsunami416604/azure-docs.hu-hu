---
title: Technikai adategységek létrehozása az Azure-tárolói rendszerképpel |} Az Azure Marketplace-en
description: Hozzon létre egy Azure-tárolóhoz a technikai eszközök.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794344"
---
# <a name="prepare-your-container-technical-assets"></a>A tároló technikai eszközök előkészítése

Ez a cikk ismerteti a lépéseket és a egy tárolót az ajánlat az Azure Marketplace-en követelmények.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentáció, amellyel az útmutatóink, Oktatóanyagaink és Példáink segítségével.

## <a name="fundamental-technical-knowledge"></a>Alapvető technikai ismereteit

Tervezése, létrehozása és tesztelése, ezek az eszközök időt vesz igénybe, és az Azure platform és a hozhat létre az ajánlat technológiák műszaki ismerete szükséges.
 
A megoldás tartomány mellett a mérnöki csapathoz a következő Microsoft-technológián Tudásbázis kell rendelkeznie:

-   Alapvető ismeretekkel [Azure-szolgáltatások](https://azure.microsoft.com/services/) 
-   Hogyan [tervezését és tervezhet meg Azure-alkalmazások](https://azure.microsoft.com/solutions/architecture/)
-   Gyakorlati ismerete [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és [az Azure-hálózatot](https://azure.microsoft.com/services/?filter=networking)
-   Gyakorlati ismerete [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Gyakorlati ismerete [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Javasolt eszközök

Válassza ki az egyik vagy mindkét a tárolórendszerkép kezeléséhez a következő parancsfájl-kezelési környezetekben:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Emellett javasoljuk, hogy a következő eszközöket ad hozzá a fejlesztési környezetet:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Mellék: [Azure Resource Manager-eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Mellék: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Mellék: [JSON prettify](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Emellett javasoljuk, hogy az elérhető eszközök áttekintése a [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldal és a Visual Studio használata a [Visual Studio-piactér](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Hozza létre a tárolórendszerképet

További információ a következő jelenik meg:

* [Oktatóanyag: Hozzon létre egy tárolórendszerképet az Azure Container Instances szolgáltatásban való üzembe helyezéshez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Oktatóanyag: Hozhat létre és helyezhet üzembe rendszerképeket az Azure Container Registry feladatokat a felhőben](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>További lépések

[A tároló ajánlat létrehozása](./cpp-create-offer.md)
