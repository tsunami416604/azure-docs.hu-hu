---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424418"
---
Ez a Poster-alapú rövid útmutató végigvezeti Önt a Tudásbázisból kapott válasz beszerzésén.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Poster**](https://www.getpostman.com/).
* [QnA Maker szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) kell rendelkeznie, és a [Tudásbázisban kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md)kell rendelkeznie. 

## <a name="publish-to-get-endpoint"></a>Közzététel a végpont beszerzéséhez

Ha készen áll arra, hogy megválaszolja a Tudásbázis kérdését, [tegye közzé](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-postman"></a>Üzemi végpont használata a Poster használatával

A Tudásbázis közzétételekor a **közzétételi** oldal MEGJELENÍTI a HTTP-kérelmek beállításait a válasz létrehozásához. Az alapértelmezett nézet a [Poster](https://www.getpostman.com)-válasz létrehozásához szükséges beállításokat jeleníti meg.

Az alábbi képen látható sárga számok azt jelzik, hogy mely név/érték párokat kell használni a következő lépésekben.

[Közzétételi eredmények ![](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

A Poster-beli válasz létrehozásához hajtsa végre a következő lépéseket:

1. A Poster megnyitása. Ha a rendszer megkéri, hogy válasszon ki egy építőelemet, válassza ki az **alapszintű kérelmek** építőeleme elemet. Adja meg a **kérelem nevét** `Generate QnA Maker answer`ként és a **gyűjteményként** `Generate QnA Maker answers`ként. Ha nem szeretne menteni egy gyűjteményt, válassza a **Mégse** gombot.
1. A munkaterületen válassza ki a **post**http-metódusát.

    [![a postán, a POST metódus beállítása](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Az URL-cím esetében fűzze össze a GAZDAGÉP értékét (#2 a rendszerképből) és a post értéket (#1 a rendszerképből) a teljes URL-cím létrehozásához. A teljes példa URL-cím A következőképpen néz ki: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [a Poster ![a teljes URL-címet adja meg](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Válassza a **fejlécek** lapot az URL-cím alatt, majd válassza a **tömeges szerkesztés**lehetőséget. 

1. Másolja a fejléceket (#3 és #4 a rendszerképből) a szövegmezőbe.

    [![a Poster-ban, állítsa be a fejléceket](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Válassza a **törzs** fület.
1. Válassza ki a **nyers** formátumot, és írja be a kérdést jelölő JSON-t (#5 a rendszerképből).

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![a Poster-ban állítsa be a törzs JSON-értékét](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Kattintson a **Küldés** gombra.
1. A válasz tartalmazza a választ, valamint azokat az egyéb információkat, amelyek fontosak lehetnek az ügyfélalkalmazás számára. 

    [![a Poster-ban állítsa be a törzs JSON-értékét](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Átmeneti végpont használata

Ha az átmeneti végponttól választ szeretne kapni, fűzze hozzá az URL-címet a `isTest` Body tulajdonsághoz.
