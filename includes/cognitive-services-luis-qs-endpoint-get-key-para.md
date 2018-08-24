---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 25a70d314ecb690a34e89a3dff7d2c4fad8b7a00
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820194"
---
Az előrejelzési végponthoz való hozzáférést az egy végponti kulcs van megadva. Ebben a rövid útmutatóban használja a LUIS-fiókjához társított ingyenes alapszintű kulcsot. 
 
1. Jelentkezzen be a LUIS-fiókjával. 

    [![Képernyőkép a Language Understanding (LUIS) alkalmazások listájának](media/cognitive-services-luis/app-list.png "Alkalmazáslista képernyőképe a Language Understanding (LUIS)")](media/cognitive-services-luis/app-list.png)

2. A jobb felső menüben válassza ki a nevét, majd válassza ki **beállítások**.

    ![A LUIS-felhasználói beállítások menüpont](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Másolja az értéket, a **szerzői műveletek kulcs**. Ez a rövid útmutató későbbi szakaszában fogja használni. 

    [![Képernyőkép a Language Understanding (LUIS) felhasználói beállítások](media/cognitive-services-luis/get-user-authoring-key.png "képernyőképe a Language Understanding (LUIS) felhasználói beállítások")](media/cognitive-services-luis/get-user-authoring-key.png)

    Az Authoring Tool kulcs lehetővé teszi, hogy az Authoring Tool API-hoz, és legfeljebb 1000 lekérdezéseket a LUIS-alkalmazások havi előrejelzési végponti API ingyenes korlátlan kéréseket. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->