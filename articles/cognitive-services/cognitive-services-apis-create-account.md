---
title: 'Rövid útmutató: A Cognitive Services-fiók létrehozása az Azure Portalon'
titleSuffix: Microsoft Docs
description: Megtudhatja, hogyan hozhat létre el az Azure Cognitive Services-fiókot.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: aahi
ms.reviewer: gibattag
ms.openlocfilehash: 232e0aa64eef4f6829813efff6e0abffd0a78518
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605130"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Rövid útmutató: A Cognitive Services-fiók létrehozása az Azure Portalon

Ez a rövid útmutató segítségével az Azure Cognitive Services használatának megkezdéséhez. Ezek a szolgáltatások szerepelnek az Azure által [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), amelyek lehetővé teszik, hogy kapcsolódjon egy vagy több számos Cognitive Services API-kat.

## <a name="prerequisites"></a>Előfeltételek

* Egy érvényes Azure-előfizetés. is [hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Hozzon létre, és az Azure Cognitive Services-erőforrás előfizetése

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és kattintson a **+ erőforrás létrehozása**.
    
    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Azure Marketplace-en, válassza a **mesterséges Intelligencia és Machine Learning**. Ha érdekli a szolgáltatás nem jelenik meg, kattintson a **összes** megtekintéséhez a Cognitive Services API-k teljes katalógusát.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Az a **létrehozás** lap, adja meg a következő információkat:

    |    |    |
    |--|--|
    | **Name (Név)** | Egy leíró nevet a cognitive services-erőforrás. Azt javasoljuk egy leíró nevet, például *MyNameFaceAPIAccount*. |
    | **Előfizetés** | Válasszon ki egy rendelkezésre álló Azure-előfizetéseit. |
    | **Hely** | A cognitive Services-példány helye. Különböző helyeken a késés bevezetni, de semmilyen hatást nem futásidejű rendelkezésre állását az erőforrást. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításokat, és a használat függ. További információkért tekintse meg az API-t [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Erőforráscsoport** | A [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) , amely tartalmazza a Cognitive Services-erőforrás. Hozzon létre egy új csoportot, vagy adja hozzá azt egy már létező csoporthoz. |

    ![Erőforrás-létrehozás képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Az erőforrás eléréséhez 

Miután létrehozta az erőforrást, hozzá tud férni az Azure irányítópultján Ha rögzítette azt. Ellenkező esetben megtalálja a **erőforráscsoportok**.

A végponti URL-cím és a kulcsot is használhat a Cognitive Services-erőforrás belül a **áttekintése** szakasz API készítésének megkezdéséhez hívja az alkalmazásokban.

![Erőforrások képernyő](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Számítógép Vision API -t C#-oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Lásd még

* [Rövid útmutató: Kézzel írt szöveg kinyerése egy rendszerképből](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Oktatóanyag: Hozzon létre egy alkalmazást, és alkalmas keretet biztosítanak az arcok a képen](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Egy egyéni keresési weblap létrehozása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Language Understanding (LUIS) integrálása a robot a Bot keretrendszer használatával](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
