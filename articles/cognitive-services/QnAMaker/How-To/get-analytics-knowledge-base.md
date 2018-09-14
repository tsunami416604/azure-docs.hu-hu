---
title: A Tudásbázis Analytics
titleSuffix: Azure Cognitive Services
description: A QnA Maker minden csevegési naplók és egyéb telemetriát tárolja, ha engedélyezte az App Insights a QnA Maker szolgáltatás létrehozása során. A csevegési naplók lekérése az App Insights a minta lekérdezéseket futtathat.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 28dd34cc49d0004dd434a54d53f3f27f7c7d80e7
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542702"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Tudásbázis elemzésének lekérése

A QnA Maker tárolja az összes csevegési naplók és egyéb telemetriát, ha engedélyezte az App Insights során a [létrehozása a QnA Maker szolgáltatás](./set-up-qnamaker-service-azure.md). A csevegési naplók lekérése az App Insights a minta lekérdezéseket futtathat.

1. Nyissa meg az App Insights-erőforrást.

    ![Válassza ki az application insights-erőforrás](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Válassza ki **Analytics**. Egy új ablak megnyílik, hogy ahol lekérdezheti a QnA Maker telemetriai adatokat.

    ![Válassza ki az Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Illessze be a következő lekérdezést, és futtathatja.

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

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis más elemzési lekérdezések futtatása

### <a name="total-90-day-traffic"></a>90 napos teljes forgalom

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

### <a name="user-traffic"></a>A felhasználói adatforgalmat

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

### <a name="latency-distribution-of-questions"></a>Késés terjesztési kérdések

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
