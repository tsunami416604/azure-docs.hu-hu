---
title: Tudásbázis – QnA Maker javítása
titleSuffix: Azure Cognitive Services
description: Aktív tanulás lehetővé teszi a Tudásbázis minősége javítható feltünteti alternatív kérdése van, a felhasználó-beküldés, a a kérdés és válasz párt alapján. Ezeket a javaslatokat tekintse át, vagy a hozzá meglévő kérdések vagy visszautasítja őket. A Tudásbázis automatikusan nem változik. El kell fogadnia a vonatkozó javaslatok bármely módosítás érvénybe léptetéséhez. Ezek a javaslatok kérdések hozzáadása nem, de módosítsa vagy törölje a meglévő kérdéseket is.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/06/2019
ms.author: diberry
ms.openlocfilehash: f8d2f6d9fce6a249a782f959ac7672ac8e123fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075156"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktív tanulás használatával javíthatja a Tudásbázis

Aktív tanulás lehetővé teszi a Tudásbázis minősége javítható feltünteti alternatív kérdése van, a felhasználó-beküldés, a a kérdés és válasz párt alapján. Ezeket a javaslatokat tekintse át, vagy a hozzá meglévő kérdések vagy visszautasítja őket. 

A Tudásbázis automatikusan nem változik. Ahhoz, hogy bármilyen módosítás érvénybe léptetéséhez el kell fogadnia a vonatkozó javaslatok. Ezek a javaslatok kérdések hozzáadása nem, de módosítsa vagy törölje a meglévő kérdéseket is.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás?

A QnA Maker megtanulja az implicit és explicit visszajelzés új kérdést változata.
 
* [Implicit visszajelzés](#how-qna-makers-implicit-feedback-works) – a rangsorolás tisztában van azzal, ha egy felhasználó kérdést rendelkezik-e több választ, amelyek a nagyon közeli pontszámok a, és ezt tekinti, visszajelzés. Nem kell semmit, hogy ez.
* [Explicit visszajelzés](#how-you-give-explicit-feedback-with-the-train-api) – Ha a pontszámok kis változat több választ ad vissza a Tudásbázis az ügyfélalkalmazás kéri a felhasználó mely kérdése a megfelelő kérdést. A felhasználónak explicit módon visszajelzést küld a QnA Maker a [Train API](#train-api). 

A két módszert biztosítanak a rangsorolás fürtözöttek hasonló lekérdezésekkel.

## <a name="how-active-learning-works"></a>Hogyan aktív tanulás működése

Aktív tanulás akkor aktiválódik, a felső néhány válaszokat a QnA Maker által visszaadott eredmények alapján. Ha a pontszám különbségek egy kis tartományon belülre esik, majd a lekérdezés nem lehetséges javaslat (mint egy másik kérdést) minden lehetséges kérdés-válasz párt. Miután elfogadja a javasolt kérdést egy adott QnA párhoz, a többi párokhoz elutasítva. Mentse és betanítást, beküldhetők javaslatok után ne felejtse el kell.

Aktív tanulás azokban az esetekben, ahol a végpontok egy ésszerű, illetve a különböző használati lekérdezések kihozhatják a lehetséges legjobb javaslatokat nyújt. 5 vagy több hasonló lekérdezések fürtözöttek, 30 percenként, QnA Maker javasolja a Tudásbázis designer elfogadja vagy elutasítja a felhasználó-alapú kérdések. Az összes javaslat listája együtt fürtözöttek és felső javaslatokat alternatív kérdések jelennek meg a végfelhasználók számára az adott lekérdezések gyakorisága alapján.

Miután kérdések a QnA Maker Portal használata javasolt, tekintse át és fogadja el vagy elutasítása szólhatnak kell. API-t javaslatok kezelése nem áll rendelkezésre.

## <a name="how-qna-makers-implicit-feedback-works"></a>Hogyan működik a QnA Maker implicit visszajelzés

A QnA Maker implicit visszajelzés pontszám közelségi határozza meg, majd győződjön meg arról, aktív tanulás javaslatok algoritmust használ. Egy egyszerű számítási közelségi meghatározni az algoritmus nem áll. Az alábbi példában a tartományokat nem jelentenek ki kell javítani, de alapján az algoritmus csak hatásának megértéséhez kell használni.

Ha egy kérdést pontszám magas biztosabb, például a 80 %-os, a pontszámok, aktív tanulás vegye figyelembe a tartomány széles, körülbelül 10 % belül. A megbízhatósági pontszám csökken, mint a 40 %-os, mivel pontszámok számos, megközelítőleg 4 % belül csökken. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hogyan, visszajelzés explicit Train API-val

Fontos, hogy a QnA Maker lekérdezi explicit visszajelzés arról, hogy mely válaszokat volt a legjobb választ. Hogyan határozza meg, a legjobb választ Öntől, és képes a következők:

* Felhasználói visszajelzések, ha kiválaszt egy adott válaszokat.
* Üzleti logika, amely meghatározza, hogy egy elfogadható például tartomány pontozása.  
* Mindkét felhasználói visszajelzések és az üzleti logika kombinációját.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Aktív tanulás használata modul frissítéséhez

Aktív tanulás a verze modulu runtime 4.4.0 vagy újabb támogatott. Ha a Tudásbázis egy korábbi verzióval készült [a futtatókörnyezet frissítése](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) a funkció használatához. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Kapcsolja be a learning javaslatok megtekintéséhez

Aktív tanulás alapértelmezés szerint ki van kapcsolva. Kapcsolja be a javasolt kérdések talál. Aktív tanulás bekapcsolása után kell az ügyfélalkalmazás adatokat küld a QnA Maker. További információkért lásd: [architekturális folyamata GenerateAnswer és Train API-k alapján a robot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Válassza ki **közzététel** a Tudásbázis közzététele. Aktív tanulás lekérdezéseket a rendszer a végpontról GenerateAnswer API előrejelzési csak gyűjti. A teszt panelt a QnA Maker Portal, a lekérdezések nem érinti a aktív tanulás.

1. Lépjen be a QnA Maker Portal learning aktív-e, a jobb felső sarokban, válassza ki a **neve**, lépjen a [ **Szolgáltatásbeállítások**](https://www.qnamaker.ai/UserSettings).  

    ![Aktív tanulás javasolt kérdést alternatíva a szolgáltatás beállításai lapon kapcsolja. Válassza ki a felhasználónevet a jobb felső menüben, majd válassza a szolgáltatás beállításait.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd váltsa át **aktív tanulás**. 

    [![A szolgáltatás beállításai lapon kapcsolja be a aktív tanulás szolgáltatást. Ha nem tudja váltsa át a szolgáltatást, szükség lehet a szolgáltatás frissítése.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Egyszer **aktív tanulás** van engedélyezve, a Tudásbázis javasol új kérdéseket rendszeres időközönként, felhasználó által beküldött kérdések alapján. Letilthatja a **aktív tanulás** beállítás átállításával újra.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Fogadja el a Tudásbázis egy aktív tanulás javaslat

1. Annak érdekében, hogy a javasolt kérdések, tekintse meg a **szerkesztése** Tudásbázis lapon jelölje be **beállításai**, majd **aktív tanulás javaslatok megjelenítése**. 

    [![A portál a szerkesztése területen jelölje be javaslatok megjelenítése az aktív tanulás új kérdést alternatívák láthatók.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A kérdés és válasz párok kiválasztásával csak javaslatok megjelenítése a Tudásbázis szűrése **javaslatok szűrés**.

    [![Javaslatok bekapcsolásához a szűrő használatával csak az aktív tanulás javasolt kérdést alternatívák megtekintéséhez.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Minden kérdés-válasz párt javasolja az új kérdést a jelölőnégyzet be van jelölve, az alternatív megoldások `✔` , fogadja el a kérdést, vagy egy `x` elutasítása a javaslatok. Válassza ki a pipa jelre a kérdés hozzáadása. 

    [![Válassza ki, vagy utasítsa el aktív tanulás javasolt kérdést alternatíva a zöld pipa, vagy a vörös törlés osztás kiválasztásával.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Hozzáadhat és törölhet _minden javaslat_ kiválasztásával **adja hozzá az összes** vagy **az összes elutasítása** a környezetfüggő eszköztáron.

1. Válassza ki **mentéséhez és a vonat** a Tudásbázis következő, a módosítások mentéséhez.

1. Válassza ki **közzététel** , hogy a módosítások is elérhetők lesznek a [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    5 vagy több hasonló lekérdezések fürtözöttek, 30 percenként, QnA Maker javasolja az alternatív kérdéseket, amelyeket elfogadására vagy elutasítására.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>A robot GenerateAnswer és Train API-k használatával architekturális folyamata

A robot vagy más ügyfélalkalmazás kell használnia a következő architekturális folyamat aktív tanulás használata:

* A robot [olvas be a választ a Tudásbázis](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) a GenerateAnswer API-val használatával a `top` válaszok számának tulajdonság.
* A robot explicit visszajelzés határozza meg:
    * Használja a saját [egyéni üzleti logika](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), szűrje ki alacsony pontszámokat.
    * A robot vagy ügyfélalkalmazás lehetséges válaszokat a felhasználó listájának megjelenítéséhez, és a felhasználó kiválasztott válasz lekérése.
* A robot [küld vissza a QnA Maker kijelölt válasz](#bot-framework-sample-code) az a [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>A felső tulajdonság használatával a GenerateAnswer kérelem több egyező válaszok

Amikor a választ, QnA Maker, kérdés elküldése a `top` JSON-törzse a tulajdonság határozza meg a visszaadandó válaszok száma. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>A pontszám tulajdonság segítségével üzleti logika és megjelenítése a felhasználónak adott válaszokat tartalmazó lista beolvasása

Ha az ügyfélalkalmazás (például csevegőrobotot) megkapja a választ, a rendszer 3 feltett legnépszerűbb kérdésekre adja vissza. Használja a `score` tulajdonság a pontszámok között könnyen elemezheti. Ez a közelségi tartomány saját üzleti logikája határozza meg. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Kérdése van hasonló pontszámok ügyfél alkalmazás nyomon követése

Az ügyfélalkalmazás jeleníti meg a kérdéseket, és válassza ki a felhasználó lehetőség _egyetlen kérdésre_ , hogy a legtöbb szándékát jelöli. 

Miután a felhasználó kiválaszt egy meglévő kérdések, az ügyfélalkalmazás küldi a felhasználó által választott visszajelzés a QnA Maker Train API-val. A visszajelzés befejezi az aktív tanulás visszajelzési hurkot. 

## <a name="train-api"></a>Train API

Aktív tanulás visszajelzést küld a vonat API POST-kérés a QnA Maker. Az API-aláírás a következő:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-kérelem tulajdonság|Name (Név)|Típus|Cél|
|--|--|--|--|
|URL-cím útvonal-paraméter|Tudásbázis-azonosító|string|A Tudásbázis GUID azonosítója.|
|Host subdomain|QnAMaker erőforrás neve|string|A QnA Maker, az Azure-előfizetésében az állomásnevet. Ez érhető el a beállítások lapon a Tudásbázis közzététele után. |
|Fejléc|Content-Type|string|Az API-nak küldött törzs médiatípusa. Alapértelmezett érték a következő: `application/json`|
|Fejléc|Engedélyezés|string|A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Bejegyzés törzse|JSON-objektum|JSON|A képzési visszajelzés|

JSON-törzse többféle beállításokkal rendelkezik:

|JSON-törzse tulajdonság|Típus|Cél|
|--|--|--|--|
|`feedbackRecords`|tömb|Visszajelzés listája.|
|`userId`|string|A felhasználói azonosító, annak a személynek a javasolt kérdések elfogadásával. Felhasználói azonosító formátuma szerint strukturálhatja. Például egy e-mail-cím lehet a felhasználói azonosító érvénytelen az architektúrához. Választható.|
|`userQuestion`|string|A kérdés pontos szövegét. Kötelező.|
|`qnaID`|szám|Kérdés, talált azonosító a [GenerateAnswer válasz](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Egy példa JSON-törzse hasonlóan néz ki:

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

A sikeres válasz egy 204, és nincs JSON-válasz törzsében állapotát adja vissza. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework mintakód

A bot framework-kód kell az Train API-t, ha a felhasználó lekérdezése aktív tanulás kell használni. Nincsenek két kódrészletek írni:

* Határozza meg, ha a lekérdezés kell használni aktív tanulás
* Lekérdezés küldi vissza a QnA Maker Train API aktív tanulás

Az a [Azure Bot minta](https://aka.ms/activelearningsamplebot), mindkét programozni. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Példa C# Train API a Bot Framework kódját 4.x

Az alábbi kód bemutatja, hogyan adatok küldése a QnA Maker Train API-val. Ez [teljes körű Kódmintát](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) elérhető a Githubon.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Példa Node.js-kód Train API a Bot keretrendszer 4.x verziója 

Az alábbi kód bemutatja, hogyan adatok küldése a QnA Maker Train API-val. Ez [teljes körű Kódmintát](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) elérhető a Githubon.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktív tanulás a rendszer menti az exportált Tudásbázis

Ha az alkalmazás rendelkezik aktív tanulás engedélyezve van, és exportálja az alkalmazás a `SuggestedQuestions` oszlop a tsv-fájl az aktív tanulás adatait őrzi meg. 

A `SuggestedQuestions` oszlop egy JSON-objektum adatainak implicit, `autosuggested`, és csak explicit módon, `usersuggested` visszajelzést. A JSON-objektum egyetlen felhasználó által beküldött kérdés, például `help` van:

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

Ha Ön importálja újra az alkalmazást, az aktív tanulás továbbra is információkat gyűjthet, és javasolja a Tudásbázis javaslatokat. 

## <a name="best-practices"></a>Ajánlott eljárások

Aktív tanulás használata esetén ajánlott eljárásokat lásd: [ajánlott eljárások](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>További lépések
 
> [!div class="nextstepaction"]
> [Metaadatok használata GenerateAnswer API-val](metadata-generateanswer-usage.md)
