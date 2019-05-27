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
ms.openlocfilehash: ef8dae8219eaf1a85a5c112705517b992e25a50f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124068"
---
Az előrejelzési végponthoz való hozzáférés egy végpontkulccsal lehetséges. Ebben a rövid útmutatóban használja a LUIS-fiókjához társított ingyenes indulókulcsot. 
 
1. Jelentkezzen be LUIS-fiókjával. 

    [![Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról](media/cognitive-services-luis/app-list.png "Képernyőkép a Language Understanding (LUIS) alkalmazáslistájáról")](media/cognitive-services-luis/app-list.png)

2. Válassza ki a nevét a jobb felső menüben, majd válassza a **Settings** (Beállítások) lehetőséget.

    ![A LUIS felhasználói beállítások menü elérése](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Másolja az **Authoring key** (Szerzői kulcs) értékét. Később még használni fogja a rövid útmutató során. 

    [![Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól](media/cognitive-services-luis/get-user-authoring-key.png "Képernyőkép a Language Understanding (LUIS) felhasználói beállításairól")](media/cognitive-services-luis/get-user-authoring-key.png)

    A szerzői kulcs havonta korlátlan számú ingyenes kérelmet biztosít a tartalomkészítési API felé, és akár 1000 lekérdezést az előrejelzési végpont API-ja felé az összes LUIS-alkalmazáshoz. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
