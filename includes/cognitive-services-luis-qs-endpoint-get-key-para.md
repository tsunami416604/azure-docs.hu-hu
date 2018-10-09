---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: fcf3bf29e2cdf89bdc93c7ebac313e5d9a9c18f0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044017"
---
Az előrejelzési végponthoz való hozzáférés egy végpontkulccsal lehetséges. Ebben a rövid útmutatóban használja a LUIS-fiókjához társított ingyenes indulókulcsot. 
 
1. Jelentkezzen be LUIS-fiókjával. 

    [![Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról](media/cognitive-services-luis/app-list.png "Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról")](media/cognitive-services-luis/app-list.png)

2. Válassza ki a nevét a jobb felső menüben, majd válassza a **Settings** (Beállítások) lehetőséget.

    ![A LUIS felhasználói beállítások menü elérése](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Másolja az **Authoring key** (Szerzői kulcs) értékét. Később még használni fogja a rövid útmutató során. 

    [![Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól](media/cognitive-services-luis/get-user-authoring-key.png "Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól")](media/cognitive-services-luis/get-user-authoring-key.png)

    A szerzői kulcs havonta korlátlan számú ingyenes kérelmet biztosít a tartalomkészítési API felé, és akár 1000 lekérdezést az előrejelzési végpont API-ja felé az összes LUIS-alkalmazáshoz. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->