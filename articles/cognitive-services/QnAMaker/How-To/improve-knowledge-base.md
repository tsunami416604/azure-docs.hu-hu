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
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: dbdd0165e276e5c82f8d4c15ef70d3a541d76bc0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522196"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktív tanulás használatával javíthatja a Tudásbázis

Aktív tanulás lehetővé teszi a Tudásbázis minősége javítható feltünteti alternatív kérdése van, a felhasználó-beküldés, a a kérdés és válasz párt alapján. Ezeket a javaslatokat tekintse át, vagy a hozzá meglévő kérdések vagy visszautasítja őket. 

A Tudásbázis automatikusan nem változik. El kell fogadnia a vonatkozó javaslatok ahhoz, hogy bármilyen módosítás érvénybe léptetéséhez. Ezek a javaslatok kérdések hozzáadása nem, de módosítsa vagy törölje a meglévő kérdéseket is.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás?

A QnA Maker megtanulja az implicit és explicit visszajelzés új kérdést változata.
 
* Implicit visszajelzés – a rangsorolás tisztában van azzal, ha egy felhasználó kérdést rendelkezik-e több választ, amelyek a nagyon közeli pontszámok a, és ezt tekinti, visszajelzés. 
* Explicit visszajelzés – amikor a pontszámok kis változat több választ ad vissza a Tudásbázis az ügyfélalkalmazás megkérdezi a felhasználót, mely kérdése a megfelelő kérdést. A felhasználónak explicit módon visszajelzést küld QnA Maker Train API-val. 

Bármelyik módszert biztosít a rangsorolás fürtözöttek hasonló lekérdezésekkel.

## <a name="how-active-learning-works"></a>Hogyan aktív tanulás működése

Aktív tanulás akkor aktiválódik, a felső néhány válasz bármely adott lekérdezésre vonatkozó QnA Maker által visszaadott eredmények alapján. A pontszám különbségek egy kis tartományon belülre esik, akkor a lekérdezés számít egy lehetséges _javaslat_ minden lehetséges válaszokat. 

Az összes javaslat listája együtt fürtözöttek és felső javaslatokat alternatív kérdések jelennek meg a végfelhasználók számára az adott lekérdezések gyakorisága alapján. Aktív tanulás azokban az esetekben, ahol a végpontok egy ésszerű, illetve a különböző használati lekérdezések kihozhatják a lehetséges legjobb javaslatokat nyújt.

5 vagy több hasonló lekérdezések fürtözöttek, 30 percenként, QnA Maker javasolja a Tudásbázis designer elfogadja vagy elutasítja a felhasználó-alapú kérdések.

Miután kérdések a QnA Maker Portal használata javasolt, tekintse át és fogadja el vagy elutasítása szólhatnak kell. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Aktív tanulás használata frissítéséhez

Aktív tanulás a verze modulu runtime 4.4.0 vagy újabb támogatott. Ha a Tudásbázis egy korábbi verzióval készült [a futtatókörnyezet frissítése](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) a funkció használatához. 

## <a name="best-practices"></a>Ajánlott eljárások

Aktív tanulás használata esetén ajánlott eljárásokat lásd: [ajánlott eljárások](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Pontszám közelségi Tudásbázis kérdések között

Ha egy kérdést pontszám magas biztosabb, például a 80 %-os, a pontszámok, aktív tanulás vegye figyelembe a tartomány széles, körülbelül 10 % belül. A megbízhatósági pontszám csökken, mint a 40 %-os, mivel pontszámok számos, megközelítőleg 4 % belül csökken. 

Egy egyszerű számítási közelségi meghatározni az algoritmus nem áll. A tartományokat, a fenti példák nem jelentenek ki kell javítani, de alapján az algoritmus csak hatásának megértéséhez kell használni.

## <a name="turn-on-active-learning"></a>Aktív tanulás bekapcsolása

Aktív tanulás alapértelmezés szerint ki van kapcsolva. Kapcsolja be a javasolt kérdések talál. 

1. Válassza ki **közzététel** a Tudásbázis közzététele. Aktív tanulás lekérdezéseket a rendszer a végpontról GenerateAnswer API előrejelzési csak gyűjti. A teszt panelt a Qna Maker Portal, a lekérdezések nem érinti a aktív tanulás.

1. Aktív tanulás a bekapcsolásához kattintson a a **neve**, lépjen a [ **Szolgáltatásbeállítások** ](https://www.qnamaker.ai/UserSettings) a QnA Maker Portal jobb felső sarokban.  

    ![Aktív tanulás javasolt kérdést alternatíva a szolgáltatás beállításai lapon kapcsolja. Válassza ki a felhasználónevet a jobb felső menüben, majd válassza a szolgáltatás beállításait.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd váltsa át **aktív tanulás**. 

    [![A szolgáltatás beállításai lapon kapcsolja be a aktív tanulás szolgáltatást. Ha nem tudja váltsa át a szolgáltatást, szükség lehet a szolgáltatás frissítése.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Egyszer **aktív tanulás** van engedélyezve, az ismeretek arra utalnak, új kérdéseket rendszeres időközönként, felhasználó által beküldött kérdések alapján. Letilthatja a **aktív tanulás** beállítás átállításával újra.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Aktív tanulás javaslat Tudásbázis hozzáadása

1. Annak érdekében, hogy a javasolt kérdések, tekintse meg a **szerkesztése** Tudásbázis lapon jelölje be **javaslatok megjelenítése**. 

    [![A portál a szerkesztése területen jelölje be javaslatok megjelenítése az aktív tanulás új kérdést alternatívák láthatók.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A kérdés és válasz párok kiválasztásával csak javaslatok megjelenítése a Tudásbázis szűrése **javaslatok szűrés**.

    [![Javaslatok bekapcsolásához a szűrő használatával csak az aktív tanulás javasolt kérdést alternatívák megtekintéséhez.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Minden kérdés szakasz javaslatokkal bemutatja egy pipa jelre az új kérdéseket `✔` , fogadja el a kérdést, vagy egy `x` elutasítása a javaslatok. Válassza ki a pipa jelre a kérdés hozzáadása. 

    [![Válassza ki, vagy utasítsa el aktív tanulás javasolt kérdést alternatíva a zöld pipa, vagy a vörös törlés osztás kiválasztásával.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Hozzáadhat és törölhet _minden javaslat_ kiválasztásával **adja hozzá az összes** vagy **az összes elutasítása**.

1. Válassza ki **mentéséhez és a vonat** a Tudásbázis következő, a módosítások mentéséhez.

1. Válassza ki **közzététel** , hogy a módosítások a GenerateAnswer API elérhető legyen.

    5 vagy több hasonló lekérdezések fürtözöttek, 30 percenként, QnA Maker javasolja a Tudásbázis designer elfogadja vagy elutasítja a felhasználó-alapú kérdések.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Határozza meg a legjobb választás, ha számos kérdést hasonló pontszámok

Pontszám túl kicsi, az egyéb kérdések feltevése esetén az ügyfél-alkalmazás fejlesztője lehet váltani, kérje meg a fejlesztőhöz.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>A felső tulajdonság csak akkor használható a GenerateAnswer kérelem

Küldjön el egy kérdést a választ a QnA Maker, JSON-törzse részét lehetővé teszi, hogy egynél több felső válasz visszaküldése:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Ha az ügyfélalkalmazás (például csevegőrobotot) megkapja a választ, visszaadott 3 feltett legnépszerűbb kérdésekre:

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

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Kérdése van hasonló pontszámok ügyfél alkalmazás nyomon követése

Az ügyfélalkalmazás összes kérdést jelenít meg és válassza ki a kérdést, hogy a felhasználó lehetőség, hogy a legtöbb szándékát jelöli. 

Miután a felhasználó kiválaszt egy meglévő kérdések, az ügyfélalkalmazás küldi a felhasználó által választott visszajelzés a QnA Maker Train API-val. A visszajelzés befejezi az aktív tanulás visszajelzési hurkot. 

Használja a [Azure Bot C# példa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot) aktív tanulás egy teljes körű forgatókönyvben megtekintéséhez.

## <a name="train-api"></a>Train API

Aktív tanulás visszajelzést küld a vonat API POST-kérés a QnA Maker. Az API-aláírás a következő:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-kérelem tulajdonság|Name (Név)|Typo|Cél|
|--|--|--|--|
|URL-cím útvonal-paraméter|Tudásbázis-azonosító|sztring|A Tudásbázis GUID azonosítója.|
|Host subdomain|QnAMaker erőforrás neve|sztring|A QnA Maker, az Azure-előfizetésében az állomásnevet. Ez érhető el a beállítások lapon a Tudásbázis közzététele után. |
|Fejléc|Content-Type|sztring|Az API-nak küldött törzs médiatípusa. Alapértelmezett érték a következő: `application/json`|
|Fejléc|Engedélyezés|sztring|A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Bejegyzés törzse|JSON-objektum|JSON|A képzési visszajelzés|

JSON-törzse többféle beállításokkal rendelkezik:

|JSON-törzse tulajdonság|Typo|Cél|
|--|--|--|--|
|`feedbackRecords`|tömb|Visszajelzés listája.|
|`userId`|sztring|A felhasználói azonosító, annak a személynek a javasolt kérdések elfogadásával. Felhasználói azonosító formátuma szerint strukturálhatja. Például egy e-mail-cím lehet a felhasználói azonosító érvénytelen az architektúrához. Választható.|
|`userQuestion`|sztring|A kérdés pontos szövegét. Kötelező.|
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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktív tanulás a rendszer menti az exportált Tudásbázis

Ha az alkalmazás rendelkezik aktív tanulás engedélyezve van, és exportálja az alkalmazás a `SuggestedQuestions` oszlop a tsv-fájl az aktív tanulás adatait őrzi meg. 

A `SuggestedQuestions` oszlop egy JSON-objektum adatainak implicit (`autosuggested`), és explicit (`usersuggested`) visszajelzés. A JSON-objektum egyetlen felhasználó által beküldött kérdés, például `help` van:

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

## <a name="next-steps"></a>További lépések
 
> [!div class="nextstepaction"]
> [Metaadatok használata GenerateAnswer API-val](metadata-generateanswer-usage.md)
