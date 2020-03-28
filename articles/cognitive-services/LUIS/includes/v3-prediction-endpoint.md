---
title: A V3 előrejelzési végpontjának be- és bekésezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287747"
---
1. A LUIS portálon a **Kezelés** szakaszban (jobb felső menü), az **Azure Resources** lapon (bal oldali menüben), az **Előrejelzés ierőforrások** lapon másolja a **példa lekérdezés** a lap alján.

    Illessze be az URL-címet egy új böngészőlapra.

    Az URL-cím rendelkezik az alkalmazásazonosítóval, a kulcsés a helyhely nevével. A V3 előrejelzési végpont URL-címe így néz ki:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

