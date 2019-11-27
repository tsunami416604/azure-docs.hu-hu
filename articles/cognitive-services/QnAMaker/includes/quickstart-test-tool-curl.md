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
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424411"
---
Ez a cURL-alapú rövid útmutató végigvezeti Önt a Tudásbázisból kapott válasz beszerzésén.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**curl**](https://curl.haxx.se/).
* [QnA Maker szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) kell rendelkeznie, és a [Tudásbázisban kérdésekkel és válaszokkal](../Tutorials/create-publish-query-in-portal.md)kell rendelkeznie.

## <a name="publish-to-get-endpoint"></a>Közzététel a végpont beszerzéséhez

Ha készen áll arra, hogy megválaszolja a Tudásbázis kérdését, [tegye közzé](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a tudásbázist.

## <a name="use-production-endpoint-with-curl"></a>Üzemi végpont használata a cURL használatával

A Tudásbázis közzétételekor a **közzétételi** oldal MEGJELENÍTI a HTTP-kérelmek beállításait a válasz létrehozásához. A **curl** lapon láthatók azok a beállítások, amelyek szükségesek a válasz létrehozásához a parancssori [eszközről.](https://www.getpostman.com)

[Közzétételi eredmények ![](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

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

Ha az átmeneti végponttól választ szeretne kapni, használja a `isTest` Body (törzs) tulajdonságot.

```json
isTest:true
```


