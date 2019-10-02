---
title: 'Gyors útmutató: A cURL használatával választ kaphat a Tudásbázisból – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti Önt a Tudásbázisban a cURL használatával kapott válasz beszerzésén.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b698b40546ee1655ebbef3980692ede6b51fc7f1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803030"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Gyors útmutató: A tudásbázisbeli válasz beszerzése a cURL használatával

Ez a cURL-alapú rövid útmutató végigvezeti Önt a Tudásbázisból kapott válasz beszerzésén.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**curl**](https://curl.haxx.se/).
* [QnA Maker szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) kell rendelkeznie, és a [Tudásbázisban kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md)kell rendelkeznie.

## <a name="publish-to-get-endpoint"></a>Közzététel a végpont beszerzéséhez

Ha készen áll arra, hogy megválaszolja a Tudásbázis kérdését, [tegye közzé](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-curl"></a>Üzemi végpont használata a cURL használatával

A Tudásbázis közzétételekor a **közzétételi** oldal MEGJELENÍTI a HTTP-kérelmek beállításait a válasz létrehozásához. A **curl** lapon láthatók azok a beállítások, amelyek szükségesek a válasz létrehozásához a parancssori [eszközről.](https://www.getpostman.com)

[@no__t – 1Publish eredményei](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

A CURL-beli válasz létrehozásához hajtsa végre a következő lépéseket:

1. Másolja a szöveget a CURL lapon. 
1. Nyisson meg egy parancssori vagy terminált, és illessze be a szöveget.
1. Szerkessze a kérdést, hogy releváns legyen a Tudásbázisban. Ügyeljen arra, hogy ne távolítsa el a kérdést körülvevő JSON-t.
1. Adja meg a parancsot. 
1. A válasz tartalmazza a válaszra vonatkozó információkat. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Átmeneti végpont használata a cURL használatával

Ha az előkészítési végponttól választ szeretne kapni, használja a `isTest` Body tulajdonságot.

```json
isTest:true
```

## <a name="next-steps"></a>További lépések

A közzétételi oldal a Poster-beli [Válasz](get-answer-from-kb-using-postman.md) létrehozásával kapcsolatos információkat is tartalmaz. 

> [!div class="nextstepaction"]
> [Metaadatok használata a válasz létrehozásakor](../How-to/metadata-generateanswer-usage.md)
