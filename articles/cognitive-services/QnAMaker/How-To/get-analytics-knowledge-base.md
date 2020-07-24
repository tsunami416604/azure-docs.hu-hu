---
title: Elemzés a Tudásbázisban – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker az összes csevegési naplót és más telemetria tárolja, ha az QnA Maker szolgáltatás létrehozása során engedélyezte az alkalmazás elemzését. Futtassa a lekérdezéseket, hogy beolvassa a csevegési naplókat az App ininsights szolgáltatásból.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 00b7b88aa4ce0cab2a2379756e40054f27fc633b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131650"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Tudásbázis elemzésének lekérése

QnA Maker az összes csevegési naplót és más telemetria tárolja, ha az [QnA Maker szolgáltatás létrehozása](./set-up-qnamaker-service-azure.md)során engedélyezte az alkalmazás elemzését. Futtassa a lekérdezéseket, hogy beolvassa a csevegési naplókat az App ininsights szolgáltatásból.

1. Nyissa meg az alkalmazás-keresési erőforrást.

    ![Válassza ki az Application ininsight-erőforrást](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Válassza a **napló (elemzés)** lehetőséget. Megnyílik egy új ablak, ahol lekérdezheti QnA Maker telemetria.

3. Illessze be a következő lekérdezést, és futtassa.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    A lekérdezés futtatásához válassza a **Futtatás** lehetőséget.

    [![Lekérdezés futtatása a kérdések, válaszok és pontozás meghatározásához a felhasználóktól](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Lekérdezések futtatása a QnA Maker Tudásbázis egyéb elemzési adataihoz

### <a name="total-90-day-traffic"></a>90-napi forgalom összesen

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>A kérdéses forgalom teljes száma adott időszakban

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Felhasználói forgalom

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Kérdések késésének eloszlása

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Megválaszolatlan kérdések

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Capactiy kiválasztása](./improve-knowledge-base.md)
