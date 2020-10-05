---
title: A v3 előrejelzési végpont beszerzése
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545241"
---
1. A LUIS portálon, a **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü), az **előrejelzési erőforrások** lapon másolja a példában szereplő **lekérdezést** az oldal aljára.

    Illessze be az URL-címet egy új böngésző lapra.

    Az URL-címben az alkalmazás azonosítója, kulcsa és a tárolóhely neve szerepel. A v3 előrejelzési végpont URL-címe a következőre hasonlít:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

