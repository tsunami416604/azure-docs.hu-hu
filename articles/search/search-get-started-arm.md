---
title: A szolgáltatás üzembe helyezéséhez használjon Azure Resource Manager-sablont
titleSuffix: Azure Cognitive Search
description: Az Azure-erőforrás-kezelő sablon használatával gyorsan üzembe helyezhet egy Azure Cognitive Search szolgáltatáspéldányt.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607623"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Rövid útmutató: Kognitív keresés telepítése Erőforrás-kezelő sablon használatával

Ez a cikk végigvezeti a folyamatot egy Resource Manager-sablon használatával egy Azure Cognitive Search-erőforrás üzembe helyezéséhez az Azure Portalon.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-cognitive-search-service"></a>Kognitív keresési szolgáltatás létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure-sablonokból származik.](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices)

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

A sablonban definiált Azure-erőforrás:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): Azure Cognitive Search szolgáltatás létrehozása

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cognitive Search erőforrást.

[![Üzembe helyezés az Azure-ban](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

A portál egy olyan űrlapot jelenít meg, amely lehetővé teszi a paraméterértékek egyszerű megadását. Egyes paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel. Meg kell adnia az előfizetést, az erőforráscsoportot, a helyet és a szolgáltatás nevét. Ha a Cognitive Services egy [AI-bővítési](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) folyamat, például a bináris képfájlok szöveges elemzéséhez, válasszon egy helyet, amely mind a Cognitive Search és a Cognitive Services. Mindkét szolgáltatásnak ugyanabban a régióban kell lennie a ai-bővítési számítási feladatokhoz. Miután kitöltötte az űrlapot, el kell fogadnia a feltételeket, majd a telepítés befejezéséhez válassza a vásárlás gombot.

> [!div class="mx-imgBorder"]
> ![Az Azure Portal sablonmegjelenítése](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Amikor a központi telepítés befejeződött, hozzáférhet az új erőforráscsoporthoz és az új keresési szolgáltatáshoz a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Más kognitív keresési rövid útmutatók és oktatóanyagok erre a rövid útmutatóra épülnek. Ha azt tervezi, hogy folytatja a munkát a további rövid útmutatókkal és oktatóanyagokkal, érdemes lehet ezt az erőforrást a helyén hagyni. Ha már nincs szükség, törölheti az erőforráscsoportot, amely törli a Cognitive Search szolgáltatást és a kapcsolódó erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Cognitive Search szolgáltatást egy Azure Resource Manager-sablon használatával, és érvényesítette a központi telepítést. Ha többet szeretne megtudni a Cognitive Search és az Azure Resource Manager, folytassa az alábbi cikkeket.

 - Az [Azure Cognitive Search áttekintésének elolvasása](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Index létrehozása](https://docs.microsoft.com/azure/search/search-get-started-portal) a keresési szolgáltatáshoz
 - [Keresési alkalmazás létrehozása](https://docs.microsoft.com/azure/search/search-create-app-portal) a portálvarázslóval
 - [Hozzon létre egy skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) információt az adatokból


