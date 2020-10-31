---
title: A v3 előrejelzési végpont beszerzése
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128161"
---
1. A LUIS portálon, a **kezelés** szakaszban (a jobb felső menüben) az **Azure-erőforrások** lapon (bal oldali menü), az **előrejelzési erőforrások** lapon másolja a példában szereplő **lekérdezést** az oldal aljára. Az URL-címben az alkalmazás azonosítója, kulcsa és a tárolóhely neve szerepel. A v3 előrejelzési végpont URL-címének formátuma a (z): `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Példa lekérdezésre az előrejelzési erőforrások szakaszban" lightbox="../media/prediction-resources-example-query.png":::
    
    Illessze be az URL-címet egy új böngésző lapra. Ha nem látja az URL-címet, nem rendelkezik előrejelzési erőforrással, és létre kell hoznia egyet. 

    

    

