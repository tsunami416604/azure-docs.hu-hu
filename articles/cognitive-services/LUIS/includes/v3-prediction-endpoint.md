---
title: A v3 előrejelzési végpont beszerzése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: d73508e8734883f5ffef205b6c1f03905f349f91
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316484"
---
1. A LUIS portálon, a **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü), az **előrejelzési erőforrások** lapon másolja a példában szereplő **lekérdezést** az oldal aljára.

    Illessze be az URL-címet egy új böngésző lapra.

    Az URL-címben az alkalmazás azonosítója, kulcsa és a tárolóhely neve szerepel. A v3 előrejelzési végpont URL-címe a következőre hasonlít:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

