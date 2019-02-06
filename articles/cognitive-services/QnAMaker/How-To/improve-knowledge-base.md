---
title: Tudásbázis – QnA Maker javítása
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: cgronlun
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 639b665926f54387dfdc6e837c15c8d6d28df925
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755764"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Aktív tanulás használata a tudásbázis továbbfejlesztéséhez

Aktív tanulás lehetővé teszi a Tudásbázis minősége javítható feltünteti alternatív kérdése van, a felhasználó-beküldés, a a kérdés és válasz párt alapján. Ezeket a javaslatokat tekintse át, vagy a hozzá meglévő kérdések vagy visszautasítja őket. 

A Tudásbázis automatikusan nem változik. El kell fogadnia a vonatkozó javaslatok ahhoz, hogy bármilyen módosítás érvénybe léptetéséhez. Ezek a javaslatok kérdések hozzáadása nem, de módosítsa vagy törölje a meglévő kérdéseket is.

## <a name="active-learning"></a>Aktív tanulás

A QnA Maker megtanulja az implicit és explicit visszajelzés új kérdést változata.
 
* Implicit visszajelzés – a rangsorolás tisztában van azzal, ha egy felhasználó kérdést rendelkezik-e több választ, amelyek a nagyon közeli pontszámok a, és ezt tekinti, visszajelzés. 
* Explicit visszajelzés – amikor a pontszámok kis változat több választ ad vissza a Tudásbázis az ügyfélalkalmazás megkérdezi a felhasználót, mely kérdése a megfelelő kérdést. A felhasználónak explicit módon visszajelzést küld QnA Maker Train API-val. 

Bármelyik módszert biztosít a rangsorolás fürtözöttek hasonló lekérdezésekkel.

Hasonló lekérdezések fürtözöttek, a QnA Maker javasolja a Tudásbázis designer elfogadja vagy elutasítja a felhasználó-alapú kérdések.

## <a name="how-active-learning-works"></a>Hogyan aktív tanulás működése

Aktív tanulás akkor aktiválódik, a felső néhány válasz bármely adott lekérdezésre vonatkozó QnA Maker által visszaadott eredmények alapján. A pontszám különbségek egy kis tartományon belülre esik, akkor a lekérdezés számít egy lehetséges _javaslat_ minden lehetséges válaszokat. 

Az összes javaslat listája együtt fürtözöttek és felső javaslatokat alternatív kérdések jelennek meg a végfelhasználók számára az adott lekérdezések gyakorisága alapján. Aktív tanulás azokban az esetekben, ahol a végpontok egy ésszerű, illetve a különböző használati lekérdezések kihozhatják a lehetséges legjobb javaslatokat nyújt.

## <a name="upgrade-version-to-use-active-learning"></a>Aktív tanulás frissítési verziója

Aktív tanulás a verze modulu runtime 4.4.0 vagy újabb támogatott. Ha a Tudásbázis egy korábbi verzióval készült [a futtatókörnyezet frissítése](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) a funkció használatához. 

## <a name="best-practices"></a>Ajánlott eljárások

Aktív tanulás használata esetén ajánlott eljárásokat lásd: [ajánlott eljárások](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Pontszám közelségi Tudásbázis kérdések között

Ha egy kérdést pontszám magas biztosabb, például a 80 %-os, a pontszámok, aktív tanulás vegye figyelembe a tartomány széles, körülbelül 10 % belül. A megbízhatósági pontszám csökken, mint a 40 %-os, mivel pontszámok számos, megközelítőleg 4 % belül csökken. 

Egy egyszerű számítási közelségi meghatározni az algoritmus nem áll. A tartományokat, a fenti példák nem jelentenek ki kell javítani, de alapján az algoritmus csak hatásának megértéséhez kell használni.

## <a name="turn-on-active-learning"></a>Aktív tanulás bekapcsolása

Aktív tanulás alapértelmezés szerint ki van kapcsolva. Bekapcsolása javasolt kérdések megtekintéséhez. 

1. Aktív tanulás a bekapcsolásához kattintson a a **neve**, lépjen a [ **Szolgáltatásbeállítások** ](https://www.qnamaker.ai/UserSettings) a QnA Maker Portal jobb felső sarokban.  

    ![A szolgáltatás beállításai lapon kapcsolja be a aktív tanulás](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd váltsa át **aktív tanulás**. 

    [![A szolgáltatás beállításai lapon kapcsolja be a aktív tanulás](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Egyszer **aktív tanulás** van engedélyezve, az ismeretek arra utalnak, új kérdéseket rendszeres időközönként, felhasználó által beküldött kérdések alapján. Letilthatja a **aktív tanulás** beállítás átállításával újra.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Aktív tanulás javaslat Tudásbázis hozzáadása

1. Annak érdekében, hogy a javasolt kérdések, tekintse meg a **szerkesztése** Tudásbázis lapon jelölje be **javaslatok megjelenítése**. 

    [![A szolgáltatás beállítások lapon a javaslatok megjelenítése gomb megjelenítése](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A Tudásbázis következő, a kérdés és válasz párok kiválasztásával javaslatok csak megjeleníthető szűrése **javaslatok szűrés**.

    [![A Szolgáltatásbeállítások oldal, a javaslatokat tekintse meg a szűrés csak kérdés-válasz párt](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Minden kérdés szakasz javaslatokkal bemutatja egy pipa jelre az új kérdéseket `✔` , fogadja el a kérdést, vagy egy `x` elutasítása a javaslatok. Válassza ki a pipa jelre a kérdés hozzáadása. 

    [![A szolgáltatás beállításai lapon kapcsolja be a aktív tanulás](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Hozzáadhat és törölhet _minden javaslat_ kiválasztásával **adja hozzá az összes** vagy **az összes elutasítása**.

1. Válassza ki **mentéséhez és a vonat** a Tudásbázis következő, a módosítások mentéséhez.


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

Miután a felhasználó kiválaszt közülük egyet a meglévő kérdéseket. A felhasználó visszajelzést küld a QnA Maker [Train](http://www.aka.ms/activelearningsamplebot) API-t továbbra is az aktív tanulás visszajelzés ikonjához. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

További információ az aktív tanulás használata egy [Azure Bot C# példa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="next-steps"></a>További lépések
 
> [!div class="nextstepaction"]
> [A QnA Maker API használata](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
