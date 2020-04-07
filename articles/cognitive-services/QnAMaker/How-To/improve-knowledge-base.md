---
title: A tudásbázis fejlesztése - QnA Maker
description: Javítsa tudásbázisa minőségét aktív tanulással. Tekintse át, fogadja el vagy utasítsa el, adja hozzá a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7fafc23eaf21099ebb974da226d07c351fa19699
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756746"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Aktív tanulási javasolt kérdések elfogadása a tudásbázisban


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Az Active Learning módosítja a Tudásbázist vagy a keresési szolgáltatást a javaslat jóváhagyása után, majd menti és betanítja. Ha jóváhagyja a javaslatot, az alternatív kérdésként kerül hozzáadásra.

## <a name="turn-on-active-learning"></a>Az aktív tanulás bekapcsolása

A javasolt kérdések megtekintéséhez be kell kapcsolnia az [aktív tanulást](use-active-learning.md) a QnA Maker erőforráshoz.

## <a name="view-suggested-questions"></a>Javasolt kérdések megtekintése

1. A javasolt kérdések megtekintéséhez a Tudásbázis **szerkesztése** lapon válassza a **Nézet beállításai**lehetőséget, majd az Aktív tanulási **javaslatok megjelenítése**lehetőséget.

    [![A portál Szerkesztés szakaszában válassza a Javaslatok megjelenítése lehetőséget az aktív tanulás új kérdésalternatíváinak megtekintéséhez.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A tudásbázis szűrése kérdés- és válaszpárokkal, hogy csak a javaslatok jelenjenek meg a **Javaslatok szűrése**lehetőség kiválasztásával.

    [![Használja a Szűrő javaslatokkal kapcsolót, hogy csak az aktív tanulás javasolt kérdés alternatíváit tekintse meg.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Minden QnA-pár pipával javasolja az `✔` új kérdésalternatívákat, `x` hogy fogadja el a kérdést, vagy utasítsa el a javaslatokat. A kérdés hozzáadásához jelölje be a pipát.

    [![A zöld pipa vagy a piros törlési jel kiválasztásával válassza vagy utasítsa el az aktív tanulás javasolt kérdésalternatíváit.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Az _összes javaslatot hozzáadhatja_ vagy törölheti a **Környezetfüggő** eszköztár Összes hozzáadása vagy **Elutasítás a** elemre.

1. Válassza a **Mentés és betanítás** lehetőséget a tudásbázis módosítási helyének mentéséhez.

1. Válassza a **Közzététel** lehetőséget, ha engedélyezni szeretné, hogy a módosítások elérhetők legyenek a [GenerateAnswer API-ból.](metadata-generateanswer-usage.md#generateanswer-request-configuration)

    Ha 5 vagy több hasonló lekérdezés van csoportosítva, 30 percenként, qna maker javasolja az alternatív kérdéseket, hogy fogadja el vagy utasítsa el.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architekturális folyamat generateanswer és betanítási API-k használatához egy bot

A robot nak vagy más ügyfélalkalmazásnak a következő architekturális folyamatot kell használnia az aktív tanulás használatához:

* Bot [leadja a választ a tudásbázis a](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) `top` GenerateAnswer API segítségével a tulajdonság, hogy számos választ.
* Bot határozza meg explicit visszajelzést:
    * A saját [egyéni üzleti logika](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)használatával szűrje ki az alacsony pontszámokat.
    * A robot vagy az ügyfél-alkalmazás, megjeleníti a lehetséges válaszok listáját a felhasználó nak, és kap a felhasználó kiválasztott választ.
* Bot [elküldi a kiválasztott választ vissza QnA Maker](#bot-framework-sample-code) a Train [API.](#train-api)


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Használja a GenerateAnswer kérés felső tulajdonságát, hogy több egyező választ kapjon

Amikor egy kérdést küld a QnA Makernek `top` válaszként, a JSON-törzs tulajdonsága beállítja a visszaküldandó válaszok számát.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>A pontszám tulajdonság és az üzleti logika használatával a felhasználók megjelenítésére adott válaszok listájának leéséhez

Amikor az ügyfélalkalmazás (például egy csevegőrobot) megkapja a választ, a rendszer a 3 legfontosabb kérdést adja vissza. A `score` tulajdonság segítségével elemezheti a pontszámok közötti közelséget. Ezt a közelségi tartományt a saját üzleti logikája határozza meg.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Az ügyfélalkalmazás nyomon követése, ha a kérdések pontszámok hasonlóak

Az ügyfélalkalmazás megjeleníti a kérdéseket egy lehetőség a felhasználó számára, hogy válassza ki _az egyetlen kérdés,_ amely a legtöbb képviseli a szándékukat.

Miután a felhasználó kiválasztja a meglévő kérdések egyikét, az ügyfélalkalmazás elküldi a felhasználó választását visszajelzésként a QnA Maker Train API használatával. Ez a visszajelzés befejezi az aktív tanulási visszacsatolási hurkot.

## <a name="train-api"></a>API betanítása

Az aktív tanulási visszajelzést a QnA Maker küldi el a Train API POST kéréssel. Az API-aláírás a következő:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-kérelem tulajdonsága|Név|Típus|Cél|
|--|--|--|--|
|URL-útvonal paramétere|Tudásbázis azonosítója|sztring|A tudásbázis guid azonosítója.|
|Egyéni altartomány|QnAMaker erőforrás neve|sztring|Az erőforrás neve a QnA Maker egyéni altartományaként használatos. Ez a tudásbázis közzététele után a Beállítások lapon érhető el. Ez szerepel a `host`.|
|Fejléc|Content-Type|sztring|Az API-nak küldött törzs adathordozó-típusa. Az alapértelmezett érték a következő:`application/json`|
|Fejléc|Engedélyezés|sztring|A végpontkulcs (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Bejegyzés törzse|JSON-objektum|JSON|A képzési visszajelzések|

A JSON-törzs nek több beállítása van:

|JSON-szerv tulajdonsága|Típus|Cél|
|--|--|--|--|
|`feedbackRecords`|tömb|Visszajelzések listája.|
|`userId`|sztring|A javasolt kérdéseket fogadó személy felhasználói azonosítója. A felhasználói azonosító formátum a Felhasználón múlik. Egy e-mail cím például lehet érvényes felhasználói azonosító az architektúrában. Választható.|
|`userQuestion`|sztring|A felhasználó lekérdezésének pontos szövege. Kötelező.|
|`qnaID`|szám|A kérdés azonosítója a [GenerateAnswer válaszban](metadata-generateanswer-usage.md#generateanswer-response-properties)található. |

Egy példa JSON test néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

A sikeres válasz 204-es állapotot ad vissza, json választörzs nélkül.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Sok visszajelzési rekord egyetlen hívásba kötegelése

Az ügyféloldali alkalmazásban, például egy robotban tárolhatja az adatokat, majd több rekordot `feedbackRecords` küldhet egyetlen JSON-törzsben a tömbben.

Egy példa JSON test néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot keretrendszer mintakódja

A robotkeretrendszer-kód meg kell hívnia a Betanítási API-t, ha a felhasználó lekérdezését aktív tanuláshoz kell használni. Két darab kódot kell írni:

* Annak megállapítása, hogy a lekérdezést aktív tanuláshoz kell-e használni
* Lekérdezés küldése a QnA Maker Train API-nak az aktív tanuláshoz

Az [Azure-robot minta mindkét](https://aka.ms/activelearningsamplebot)tevékenység programozott.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Példa C# kód a Vonat API Bot Framework 4.x

A következő kód bemutatja, hogyan küldhet vissza adatokat a QnA Maker-nek a Train API-val.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Példa Node.js kód a Robot Framework 4.x vonat API-jához

A következő kód bemutatja, hogyan küldhet vissza adatokat a QnA Maker-nek a Train API-val.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Az aktív tanulás az exportált tudásbázisban kerül mentésre

Ha az alkalmazás aktív tanulás engedélyezve van, `SuggestedQuestions` és exportálja az alkalmazást, a tsv-fájl oszlopa megőrzi az aktív tanulási adatokat.

Az `SuggestedQuestions` oszlop az implicit , `autosuggested`és explicit `usersuggested` visszajelzések JSON-objektuma. Egy példa erre a JSON-objektumra egyetlen `help` felhasználó által beküldött kérdéshez:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

A letöltési módosítások API-val is áttekintheti ezeket a módosításokat a REST vagy a nyelvalapú SDK-k használatával:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Az alkalmazás újraimportálásakor az aktív tanulás továbbra is információkat gyűjt, és javaslatokat javasol a tudásbázisához.



## <a name="best-practices"></a>Ajánlott eljárások

Az aktív tanulás használata során ajánlott eljárásokat az Ajánlott eljárások című [témakörben tésszet.](../Concepts/best-practices.md#active-learning)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Metaadatok használata a GenerateAnswer API-val](metadata-generateanswer-usage.md)
