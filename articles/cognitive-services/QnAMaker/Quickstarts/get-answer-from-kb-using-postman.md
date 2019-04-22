---
title: 'Gyors útmutató: Első válasz a Tudásbázis – QnA Maker a Postman használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti egy válasz lekérése a Tudásbázis Postman használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 24bd6731faa9788dc336db199aa9776813e7348f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683443"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Gyors útmutató: A kapott eredménynek a Tudásbázis Postman használatával

A Postman-alapú rövid útmutató végigvezeti a Tudásbázis-válasz lekérése.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [ **Postman**](https://www.getpostman.com/).
* Rendelkeznie kell egy [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md) , és egy [Tudásbázis kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Végpont közzététele

Amikor készen áll egy kérdésre adott válasz a Tudásbázis, a létrehozásához [közzététele](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-postman"></a>Éles környezetben a végpontot használja a postman használatával

A Tudásbázis közzétételekor a **közzététel** lap megjeleníti a HTTP-kérelem beállítások választ létrehozásához. Az alapértelmezett nézet jeleníti meg a választ a létrehozásához szükséges beállításokat [Postman](https://www.getpostman.com).

Az alábbi ábrán a sárga számok jelölik, mely név-érték párokat. a következő lépésekben használni.

[![Eredmények közzététele](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Létrehozás Postman-válasz, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Postmant. Ha az kéri, hogy válasszon egy olyan építőelem, válassza ki a **alapszintű kérelem** építőelem. Állítsa be a **kérelem neve** , `Generate QnA Maker answer`és a **gyűjtemény** , `Generate QnA Maker answers`. Ha nem szeretné menteni egy gyűjteményhez, válassza ki a **Mégse** gombra.
1. A munkaterületen válassza ki a HTTP-metódusa **POST**.

    [![A Postman beállítása a POST-metódus](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Az URL-fűzze össze a GAZDAGÉP (2-rendszerképből) és a Post értékkel (1-rendszerképből) hozhat létre a teljes URL-CÍMÉT. Egy teljes példát URL-cím hasonlóan néz ki: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![A Postman beállítása a teljes URL-címe](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Válassza ki a **fejlécek** lap URL-CÍMÉT, majd válassza a **tömeges szerkesztése**. 

1. Másolja a fejléceket (#3 és rendszerképből #4) a szöveg területre.

    [![A Postman beállítása a fejlécek](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Válassza ki a **törzs** fülre.
1. Válassza ki a **nyers** formázhatja, és adja meg a kérdés képviselő JSON (5-rendszerképből).

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![A Postman beállítása a szervezet JSON-értéket](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Válassza ki a **küldése** gombra.
1. A válasz tartalmazza a választ, amely lehet fontos, hogy az ügyfélalkalmazás más információkkal együtt. 

    [![A Postman beállítása a szervezet JSON-értéket](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Átmeneti tárolási végpont használata

Ha azt szeretné, kap választ az átmeneti végpontról, fűzze hozzá az URL-CÍMÉT a `isTest` törzs tulajdonság.

## <a name="next-steps"></a>További lépések

A közzétételi oldalon olyan információt is tartalmaz [választ készítése](get-answer-from-kb-using-curl.md) a curl használatával. 

> [!div class="nextstepaction"]
> [Metaadatok használata egy válaszfájl létrehozása közben](../How-to/metadata-generateanswer-usage.md)
