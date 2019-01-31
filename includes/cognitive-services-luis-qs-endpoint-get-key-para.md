---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478683"
---
Az előrejelzési végponthoz való hozzáférés egy végpontkulccsal lehetséges. Ebben a rövid útmutatóban használja a LUIS-fiókjához társított ingyenes indulókulcsot. 
 
1. Jelentkezzen be LUIS-fiókjával. 

    [![Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról](media/cognitive-services-luis/app-list.png "Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról")](media/cognitive-services-luis/app-list.png)

2. Válassza ki a nevét a jobb felső menüben, majd válassza a **Settings** (Beállítások) lehetőséget.

    ![A LUIS felhasználói beállítások menü elérése](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Másolja az **Authoring key** (Szerzői kulcs) értékét. Később még használni fogja a rövid útmutató során. 

    [![Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól](media/cognitive-services-luis/get-user-authoring-key.png "Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól")](media/cognitive-services-luis/get-user-authoring-key.png)

    A szerzői kulcs havonta korlátlan számú ingyenes kérelmet biztosít a tartalomkészítési API felé, és akár 1000 lekérdezést az előrejelzési végpont API-ja felé az összes LUIS-alkalmazáshoz. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
