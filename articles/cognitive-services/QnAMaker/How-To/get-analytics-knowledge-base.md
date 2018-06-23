---
title: Hogyan kérhet a Tudásbázis elemzés alap - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Elemzés a Tudásbázis az beszerzése
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348618"
---
# <a name="get-analytics-on-your-knowledge-base"></a>A Tudásbázis analytics letöltése

Kérdések és válaszok készítő tárolja összes Csevegés naplók és egyéb telemetriai adatokat, ha engedélyezte a App Insights során a [a kérdések és válaszok készítő szolgáltatás létrehozása](./set-up-qnamaker-service-azure.md). A csevegési naplók lekérése App Insights a mintalekérdezések futtatása.

1. Nyissa meg az App Insights-erőforrást.

    ![Válassza ki az application insights-erőforrás](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Válassza ki **Analytics**. Egy új ablakban nyílik, hogy ahol lekérdezheti a kérdések és válaszok készítő telemetriai adatokat.

    ![Válassza ki az Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Illessze be a következő lekérdezést, és futtassa.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Válassza ki **futtatása** a lekérdezés futtatásához.

    ![A lekérdezés futtatása](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>A kérdések és válaszok készítő Tudásbázis kapcsolatos egyéb elemzéséhez lekérdezések futtatása

### <a name="total-90-day-traffic"></a>A teljes 90 napos forgalma

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Teljes kérdés forgalmat egy adott időszakban

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Felhasználói forgalom

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>A kérdések késéseloszlása

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kulcsok kezelése](./key-management.md)
