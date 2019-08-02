---
title: 'Gyors útmutató: A Poster használatával kaphat választ a Tudásbázisból – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti Önt a Tudásbázisban a Poster használatával kapott válasz beszerzésén.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7c419ea7cb5c9ae1d1cc419798961ec67f250160
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562999"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Gyors útmutató: Válasz kérése a Tudásbázisból a Poster használatával

Ez a Poster-alapú rövid útmutató végigvezeti Önt a Tudásbázisból kapott válasz beszerzésén.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Poster**](https://www.getpostman.com/).
* [QnA Maker szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) kell rendelkeznie, és a [Tudásbázisban kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md)kell rendelkeznie. 

## <a name="publish-to-get-endpoint"></a>Közzététel a végpont beszerzéséhez

Ha készen áll arra, hogy megválaszolja a Tudásbázis kérdését, [tegye közzé](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-postman"></a>Üzemi végpont használata a Poster használatával

A Tudásbázis közzétételekor a **közzétételi** oldal MEGJELENÍTI a HTTP-kérelmek beállításait a válasz létrehozásához. Az alapértelmezett nézet a Poster-válasz létrehozásához szükséges beállításokat [](https://www.getpostman.com)jeleníti meg.

Az alábbi képen látható sárga számok azt jelzik, hogy mely név/érték párokat kell használni a következő lépésekben.

[![Közzétételi eredmények](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

A Poster-beli válasz létrehozásához hajtsa végre a következő lépéseket:

1. A Poster megnyitása. Ha a rendszer megkéri, hogy válasszon ki egy építőelemet, válassza ki az alapszintű **kérelmek** építőeleme elemet. Adja meg a **kérelem nevét** `Generate QnA Maker answer` `Generate QnA Maker answers`és a **gyűjteményt** . Ha nem szeretne menteni egy gyűjteményt, válassza a **Mégse** gombot.
1. A munkaterületen válassza ki a **post**http-metódusát.

    [![A Poster-ban állítsa be a POST metódust](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Az URL-cím esetében fűzze össze a GAZDAGÉP értékét (#2 a rendszerképből) és a post értéket (#1 a rendszerképből) a teljes URL-cím létrehozásához. A teljes példa URL-cím A következőképpen néz ki: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![A Poster-ben állítsa be a teljes URL-címet.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Válassza a **fejlécek** lapot az URL-cím alatt, majd válassza a **tömeges szerkesztés**lehetőséget. 

1. Másolja a fejléceket (#3 és #4 a rendszerképből) a szövegmezőbe.

    [![A Poster alatt állítsa be a fejléceket](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Válassza a **törzs** fület.
1. Válassza ki a **nyers** formátumot, és írja be a kérdést jelölő JSON-t (#5 a rendszerképből).

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![A Poster alatt állítsa be a törzs JSON-értékét.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Kattintson a **Küldés** gombra.
1. A válasz tartalmazza a választ, valamint azokat az egyéb információkat, amelyek fontosak lehetnek az ügyfélalkalmazás számára. 

    [![A Poster alatt állítsa be a törzs JSON-értékét.](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Átmeneti végpont használata

Ha az előkészítési végponttól választ szeretne kapni, fűzze hozzá az URL-címet a `isTest` Body tulajdonsághoz.

## <a name="next-steps"></a>További lépések

A közzétételi oldalon a cURL használatával kapcsolatos [válaszok](get-answer-from-kb-using-curl.md) is megadhatók. 

> [!div class="nextstepaction"]
> [Metaadatok használata a válasz létrehozásakor](../How-to/metadata-generateanswer-usage.md)
