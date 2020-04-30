---
title: A szolgáltatás üzembe helyezése Azure Resource Manager sablon használatával
titleSuffix: Azure Cognitive Search
description: Az Azure Resource Manager-sablon használatával gyorsan üzembe helyezhet egy Azure Cognitive Search Service-példányt.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80607623"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Gyors útmutató: Cognitive Search üzembe helyezése Resource Manager-sablon használatával

Ez a cikk végigvezeti egy Resource Manager-sablon használatának folyamatán, amely egy Azure Cognitive Search-erőforrás üzembe helyezését mutatja be a Azure Portalban.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-cognitive-search-service"></a>Cognitive Search szolgáltatás létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon [Azure-sablonokból](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices)származik.

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

A sablonban definiált Azure-erőforrás:

- [Microsoft. Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): Azure Cognitive Search szolgáltatás létrehozása

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cognitive Search-erőforrást.

[![Üzembe helyezés az Azure-ban](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

A portál megjelenít egy űrlapot, amely lehetővé teszi a paraméterek értékének egyszerű megadását. Bizonyos paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel. Meg kell adnia az előfizetését, az erőforráscsoportot, a helyet és a szolgáltatás nevét. Ha Cognitive Servicest szeretne használni egy AI- [dúsítási](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) folyamatban, például a bináris képfájlok elemzéséhez a szöveghez, válassza ki a Cognitive Search és a Cognitive Services egyaránt tartalmazó helyet. Mindkét szolgáltatásnak ugyanabban a régióban kell lennie az AI-bővítési munkaterhelések esetében. Az űrlap befejezése után el kell fogadnia a feltételeket és kikötéseket, majd a telepítés befejezéséhez a vásárlás gombot kell választania.

> [!div class="mx-imgBorder"]
> ![A sablon Azure Portal megjelenítése](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az üzembe helyezés befejezésekor elérheti az új erőforráscsoportot és az új keresési szolgáltatást a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatóban más Cognitive Search rövid útmutatók és oktatóanyagok is felépülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, érdemes lehet ezt az erőforrást helyben hagyni. Ha már nincs rá szükség, törölheti az erőforráscsoportot, amely törli a Cognitive Search szolgáltatást és a kapcsolódó erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Cognitive Search szolgáltatást egy Azure Resource Manager sablonnal, és ellenőrizte az üzemelő példányt. Ha többet szeretne megtudni a Cognitive Search és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

 - Az [Azure Cognitive Search áttekintése](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Index létrehozása](https://docs.microsoft.com/azure/search/search-get-started-portal) a keresési szolgáltatáshoz
 - [Keresési alkalmazás létrehozása](https://docs.microsoft.com/azure/search/search-create-app-portal) a portál varázsló használatával
 - [Készségkészlet létrehozása](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) adatok kinyeréséhez


