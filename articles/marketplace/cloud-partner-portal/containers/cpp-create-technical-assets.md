---
title: Technikai adategységek létrehozása az Azure-tárolói rendszerképpel |} A Microsoft Docs
description: Hozzon létre egy Azure-tárolóhoz a technikai eszközök.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 56de300a95d750957494865f9871127b2a9c8283
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979777"
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
    *   Bővítmény: [Azure Resource Manager-eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Bővítmény: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Bővítmény: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Emellett javasoljuk, hogy az elérhető eszközök áttekintése a [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldal és a Visual Studio használata a [Visual Studio-piactér](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Hozza létre a tárolórendszerképet

- Hozzon létre, és konfigurálja a virtuális merevlemez (VHD) a tároló virtuális gép (VM). Ez a VHD operációs rendszert (Windows, Linux és Ubuntu) tároló tartalmazza. További adatlemezek lehet szükség.
- Konfigurálja a virtuális gép operációs rendszerének, a virtuális gép mérete, a portok megnyitásához és a bármely más csatolt lemez.
- Telepítse az alkalmazás és más szoftverek, amelyek az ajánlat szükséges. Például: adatbázis-szoftver, harmadik féltől származó szoftverekkel, vagy egy egyéni alkalmazást.

## <a name="next-steps"></a>További lépések

[A tároló ajánlat létrehozása](./cpp-create-offer.md)