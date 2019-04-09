---
title: 'Gyors útmutató: Első válasz a Tudásbázis – QnA Maker a cURL használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti a cURL használatával Tudásbázis-válasz lekérése.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: ce2d39f3941030059cef329aa60e804a44d73a98
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263094"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Gyors útmutató: Első válasz a Tudásbázis a cURL használatával

A cURL-alapú rövid útmutató végigvezeti a Tudásbázis-válasz lekérése.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [ **cURL**](https://curl.haxx.se/).
* Rendelkeznie kell egy [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md) , és egy [Tudásbázis kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Végpont közzététele

Amikor készen áll egy kérdésre adott válasz a Tudásbázis, a létrehozásához [közzététele](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-curl"></a>Éles környezetben a végpontot használja a curl használatával

A Tudásbázis közzétételekor a **közzététel** lap megjeleníti a HTTP-kérelem beállítások választ létrehozásához. A **CURL** lapon látható választ létrehozása a parancssori eszköz, a szükséges beállításokat [CURL](https://www.getpostman.com).

[![Peredmények közzététele](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

A curl használatával választ létrehozásához kövesse az alábbi lépéseket:

1. A CURL lapon másolja be a szöveget. 
1. Nyisson meg egy parancssori vagy a Terminálszolgáltatások, és illessze be a szöveget.
1. Szerkessze a kérdést a Tudásbázis szempontjából. Ügyeljen arra, hogy távolítsa el a kérdés körülvevő tartalmazó JSON-Beállításfájlt.
1. Adja meg a parancsot. 
1. A válasz tartalmazza a választ a lényeges információkat. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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

## <a name="use-staging-endpoint-with-curl"></a>Átmeneti a végpontot használja a curl használatával

Ha azt szeretné, kap választ az átmeneti végpontról, használja a boolean paramétert `isTest` értékét `true`.

`isTest=true`

## <a name="next-steps"></a>További lépések

A közzétételi oldalon olyan információt is tartalmaz [választ készítése](get-answer-from-kb-using-postman.md) a postman használatával. 

> [!div class="nextstepaction"]
> [Metaadatok használata egy válaszfájl létrehozása közben](../How-to/metadata-generateanswer-usage.md)
