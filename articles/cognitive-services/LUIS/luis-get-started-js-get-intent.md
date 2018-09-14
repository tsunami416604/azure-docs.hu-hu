---
title: Természetes nyelvű szöveg elemzése a Language Understanding (LUIS) szolgáltatásban Javascript használatával – Cognitive Services – Azure Cognitive Services | Microsoft Docs
description: Ebben a rövid útmutatóban elérhető nyilvános LUIS-alkalmazással határozza meg egy felhasználó szándékát egy beszélgetés szövegéből. JavaScript nyelven küldje el szövegként a felhasználó szándékát a nyilvános alkalmazás HTTP-előrejelzési végpontjára. A LUIS a végpontnál a nyilvános alkalmazás modelljét alkalmazza a természetes nyelvű szövegen a jelentés elemzése érdekében, amellyel meghatározza az általános szándékot, valamint kinyeri az alkalmazás témájában releváns adatokat.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769978"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Rövid útmutató: Szövegelemzés Javascript használatával

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Elemzés böngészővel

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Szövegelemzés Javascript használatával 

A Javascripttel hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. 

1. Másolja ki az alábbi kódot, és mentse egy HTML-fájlba:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Nyissa meg a fájlt egy böngészőben. Írja be a LUIS-végpont kulcsát az űrlapon, és válassza a **Submit** (Küldés) gombot.

    ![A böngészőben megjelenő HTML-minta a Home Automation alkalmazás LUIS-eredményeivel](./media/luis-get-started-js-get-intent/html-results.png)

    Az eredmény az űrlap alatt jelenik meg. 

## <a name="luis-keys"></a>LUIS-kulcsok

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Javascript-fájlt.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-javascript-add-utterance.md)
