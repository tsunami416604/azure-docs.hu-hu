---
title: A v3 előrejelzési végpont beszerzése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287747"
---
1. A LUIS-portál **kezelés** szakaszában (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü), az **előrejelzési erőforrások** lapon másolja a **példában szereplő lekérdezést** az oldal aljára.

    Illessze be az URL-címet egy új böngésző lapra.

    Az URL-címben az alkalmazás azonosítója, kulcsa és a tárolóhely neve szerepel. A v3 előrejelzési végpont URL-címe a következőre hasonlít:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

