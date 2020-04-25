---
title: Azure-tárolók rendszerképének létrehozása – technikai eszközök | Azure piactér
description: Hozzon létre egy Azure-tárolóhoz tartozó technikai eszközöket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 68db606c9a01c4b1122f9b0cce620762485ca40a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148263"
---
# <a name="prepare-your-container-technical-assets"></a>Tárolók technikai eszközeinek előkészítése

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli Container-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az [Azure Container műszaki eszközeinek előkészítése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

Ez a cikk a tárolók Azure Marketplace-hez való konfigurálásának lépéseit és követelményeit ismerteti.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át a [Azure Container instances](https://docs.microsoft.com/azure/container-instances) dokumentációját, amely rövid útmutatókat, oktatóanyagokat és mintákat tartalmaz.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Az adategységek tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.
 
A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

-    Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése 
-    [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
-    Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking) együttműködésének ismerete
-    [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
-    A [JSON](https://www.json.org/) működésének ismerete

## <a name="suggested-tools"></a>Javasolt eszközök

A tároló rendszerképének kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Emellett javasoljuk a következő eszközök hozzáadását a fejlesztői környezethez:

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon elérhető eszközöket, és ha a Visual studiót használja, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="create-the-container-image"></a>A tároló rendszerképének létrehozása

További információért tekintse meg a következőket:

* [Oktatóanyag: tároló-rendszerkép létrehozása a Azure Container Instances való üzembe helyezéshez](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Oktatóanyag: tároló lemezképek létrehozása és üzembe helyezése a felhőben Azure Container Registry feladatokkal](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>További lépések

[A tároló ajánlat létrehozása](./cpp-create-offer.md)
