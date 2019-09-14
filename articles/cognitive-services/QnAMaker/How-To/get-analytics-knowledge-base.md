---
title: Elemzés a Tudásbázisban – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker az összes csevegési naplót és más telemetria tárolja, ha az QnA Maker szolgáltatás létrehozása során engedélyezte az alkalmazás elemzését. Futtassa a lekérdezéseket, hogy beolvassa a csevegési naplókat az App ininsights szolgáltatásból.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5c55084a57e46931049841f5011941b2115e9e69
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961530"
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
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>A kérdéses forgalom teljes száma adott időszakban

```kusto
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Felhasználói forgalom

```kusto
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Capactiy kiválasztása](../tutorials/choosing-capacity-qnamaker-deployment.md)
