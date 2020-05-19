---
title: A v3 előrejelzési végpont beszerzése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589136"
---
1. A LUIS portálon, a **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü), az **előrejelzési erőforrások** lapon másolja a példában szereplő **lekérdezést** az oldal aljára.

    Illessze be az URL-címet egy új böngésző lapra.

    Az URL-címben az alkalmazás azonosítója, kulcsa és a tárolóhely neve szerepel. A v3 előrejelzési végpont URL-címe a következőre hasonlít:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

