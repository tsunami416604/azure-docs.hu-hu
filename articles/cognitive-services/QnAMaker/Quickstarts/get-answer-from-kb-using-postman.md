---
title: 'Gyors útmutató: Első válasz a Tudásbázis – QnA Maker a Postman használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti egy válasz lekérése a Tudásbázis Postman használatával.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270892"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Gyors útmutató: A kapott eredménynek a Tudásbázis Postman használatával

A Postman-alapú rövid útmutató végigvezeti a Tudásbázis-válasz lekérése.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [ **Postman**](https://www.getpostman.com/).
* Rendelkeznie kell egy [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md) , és egy [Tudásbázis kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Végpont közzététele

Amikor készen áll egy kérdésre adott válasz a Tudásbázis, a létrehozásához [közzététele](../How-to/publish-knowledge-base.md) a tudásbázist.

## <a name="use-production-endpoint-with-postman"></a>Éles környezetben a végpontot használja a postman használatával

A Tudásbázis közzétételekor a **közzététel** lap megjeleníti a HTTP-kérelem beállítások választ létrehozásához. Az alapértelmezett nézet jeleníti meg a választ a létrehozásához szükséges beállításokat [Postman](https://www.getpostman.com).

[![Eredmények közzététele](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Létrehozás Postman-válasz, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Postmant. 
1. Válassza ki a építőeleme, hozzon létre egy alapszintű kérelmet.
1. Állítsa be a **kérelem neve** , `Generate QnA Maker answer`és a **gyűjtemény** , `Generate QnA Maker answers`.
1. A munkaterületen válassza ki a HTTP-metódusa **POST**.
1. Az URL-címéhez fűzze össze a GAZDAGÉP és a teljes URL-cím létrehozása bejegyzés értéke. 

    [![A Postman módszert állítja be a Post és a teljes URL-címe](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Válassza ki a **fejlécek** lap URL-CÍMÉT, majd válassza a **tömeges szerkesztése**. 
1. Másolja a fejlécek a szöveg területre.

    [![A Postman beállítása a fejlécek](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Válassza ki a **törzs** fülre.
1. Válassza ki a **nyers** formázhatja, és adja meg a JSON-t jelöli a kérdését.

    [![A Postman beállítása a szervezet JSON-értéket](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Válassza ki a **küldése** gombra.
1. A válasz tartalmazza a választ, amely lehet fontos, hogy az ügyfélalkalmazás más információkkal együtt. 

    [![A Postman beállítása a szervezet JSON-értéket](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Átmeneti a végpontot használja a curl használatával

Ha azt szeretné, kap választ az átmeneti végpontról, használja a boolean paramétert `isTest` értékét `true`.

`isTest=true`

## <a name="next-steps"></a>További lépések

A közzétételi oldalon olyan információt is tartalmaz [választ készítése](get-answer-from-kb-using-curl.md) a curl használatával. 

> [!div class="nextstepaction"]
> [Metaadatok használata egy válaszfájl létrehozása közben](../How-to/metadata-generateanswer-usage.md)