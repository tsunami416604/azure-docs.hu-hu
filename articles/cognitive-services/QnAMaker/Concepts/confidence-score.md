---
title: Megbízhatósági pontszám – QnA Maker
titleSuffix: Azure Cognitive Services
description: A megbízhatósági pontszám azt jelzi, hogy a válasz megfelel-e a megfelelőnek az adott felhasználói lekérdezéshez.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a80c61efbcbff569f5fed53734def3979ed70616
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820750"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker Tudásbázis megbízhatósági pontszáma
Ha egy Tudásbázisban egy felhasználói lekérdezés egyezik, a QnA Maker a megfelelő válaszokat adja vissza, és egy megbízhatósági pontszámmal együtt. Ez a pontszám azt jelzi, hogy a válasz a megfelelő egyezés az adott felhasználói lekérdezés esetében. 

A megbízhatósági pontszám 0 és 100 közötti szám. Egy 100-es pontszám valószínűleg pontos egyezést jelent, míg a 0 érték azt jelenti, hogy nem található egyező válasz. Minél magasabb a pontszám – annál nagyobb a válaszban megjelenő bizalom. Egy adott lekérdezés esetében több válasz is érkezett. Ebben az esetben a válaszokat a rendszer a megbízhatósági pontszám csökkentése érdekében adja vissza.

Az alábbi példában egy QnA entitást láthat, 2 kérdéssel. 


![Minta QnA pár](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

A fenti példa – a különböző típusú felhasználói lekérdezések esetében a következőhöz hasonló pontszámok is számíthatnak:


![Rangsor pontszámának tartománya](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Az alábbi táblázat az adott pontszámhoz tartozó jellemző megbízhatóságot mutatja be.

|Pontszám értéke|Pontszám jelentése|Példa lekérdezésre|
|--|--|--|
|90 – 100|A felhasználói lekérdezés közel pontos egyezése és egy KB-os kérdés|"A módosítások a közzététel után nem frissültek a KB-ban"|
|> 70|Nagy megbízhatóság – általában egy jó válasz, amely teljesen megválaszolja a felhasználó lekérdezését|"Közzétettem a KB-ot, de ez nem frissült"|
|50 – 70|Közepes megbízhatóság – általában meglehetősen jó válasz, amelynek a felhasználói lekérdezés fő szándékára kell válaszolnia|"Menteni kell a frissítéseket, mielőtt közzéteszem a KB-ot?"|
|30 - 50|Alacsony megbízhatóság – általában egy kapcsolódó válasz, amely részben válaszol a felhasználó szándékára|"Mit csinál a Mentés és a vonat?"|
|< 30|Nagyon alacsony megbízhatóság – általában nem válaszol a felhasználó lekérdezésére, de vannak hozzájuk illő szavak vagy kifejezések |"Hol adhatok hozzá szinonimákat a KB-hoz"|
|0|Nincs egyezés, így a válasz nem lesz visszaadva.|"Mennyibe kerül a szolgáltatás díja"|

## <a name="choose-a-score-threshold"></a>Pontszám küszöbértékének kiválasztása
A fenti táblázat a legtöbb Tudásbázis várt pontszámokat mutatja. Mivel azonban minden KB különböző, és különböző típusú szavakat, szándékokat és célokat tartalmaz, javasoljuk, hogy tesztelje, és válassza ki az Ön számára legmegfelelőbb küszöbértéket. Alapértelmezés szerint a küszöbérték 0 értékre van állítva, hogy a rendszer minden lehetséges választ adjon vissza. A legtöbb Tudásbázis működéséhez ajánlott küszöbérték **50**.

A küszöbérték kiválasztásakor tartsa szem előtt a pontosság és a lefedettség közötti egyensúlyt, és az igényeinek megfelelően módosítsa a küszöbértéket.

- Ha a **pontosság** (vagy pontosság) fontosabb a forgatókönyv esetében, akkor növelje a küszöbértéket. Így minden alkalommal, amikor választ ad vissza, sokkal MAGABIZTOSABB lesz, és sokkal valószínűbb, hogy a felhasználók keresik a válaszokat. Ebben az esetben előfordulhat, hogy a végén további kérdések megválaszolása nem lehetséges. *Például:* ha a **70**-es küszöbértéket választja, akkor előfordulhat, hogy nem egyértelmű példákat ad a "mi a Save and Train?" kifejezésre.

- Ha a **lefedettség** (vagy visszahívás) fontosabb, és a lehető legtöbb kérdésre szeretne választ adni, akkor is, ha csak részleges kapcsolatban áll a felhasználó kérdésével – akkor csökkentse a küszöbértéket. Ez azt jelenti, hogy több eset is lehetséges, ha a válasz nem válaszol a felhasználó tényleges lekérdezésére, de más, némileg kapcsolódó választ ad. *Például:* ha a **30**. küszöbértéket adja meg, választ kaphat a lekérdezésekre, például "hol szerkeszthetem a kb-ot?"

> [!NOTE]
> A QnA Maker újabb verziói között szerepelnek a pontozási logikával kapcsolatos tökéletesítések, és befolyásolhatják a küszöbértéket. Minden alkalommal, amikor frissíti a szolgáltatást, győződjön meg arról, hogy szükség esetén tesztelje és csípése a küszöbértéket. [Itt](https://www.qnamaker.ai/UserSettings)megtekintheti a QnA szolgáltatás verzióját, és megtudhatja, hogyan kérheti le a [legújabb frissítéseket.](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Küszöbérték beállítása 

A küszöbérték pontszámát állítsa be a [GENERATEANSWER API JSON-törzsének](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)tulajdonságára. Ez azt jelenti, hogy a GenerateAnswer minden egyes hívása esetében be kell állítania azt. 

A bot-keretrendszerben állítsa be a pontszámot a ( [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) vagy [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs)) beállítások objektum részeként.

## <a name="improve-confidence-scores"></a>A megbízhatósági pontszámok javítása
A felhasználói lekérdezésekre adott válasz megbízhatósági pontszámának javítása érdekében a felhasználó lekérdezését hozzáadhatja a tudásbázishoz az adott válaszra vonatkozó alternatív kérdésként. A kis-és nagybetűk megkülönböztetésére is használható [, ha](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) szinonimákat ad hozzá a tudásbázisban található kulcsszavakhoz.


## <a name="similar-confidence-scores"></a>Hasonló megbízhatósági pontszámok
Ha több válasz is hasonló megbízhatósági pontszámmal rendelkezik, akkor valószínű, hogy a lekérdezés túl általános, és így azonos valószínűséggel egyezik meg több választal. Próbálja meg jobban felstrukturálni a QnAs, hogy minden QnA-entitásnak külön szándéka legyen.


## <a name="confidence-score-differences"></a>Megbízhatósági pontszám eltérései
A válasz megbízhatósági pontszáma a elhanyagolható mértékben és a Tudásbázis közzétett verziója között is változhat, még akkor is, ha a tartalom ugyanaz. Ennek az az oka, hogy a teszt tartalma és a közzétett Tudásbázis különböző Azure Cognitive Search indexekben található. Ha közzétesz egy tudásbázist, a Tudásbázisban a kérdés és a válasz tartalma a tesztelési indexből egy éles indexbe kerül az Azure Search-ben. Nézze meg, hogyan működik a [közzétételi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) művelet.

Ha a Tudásbázis különböző régiókban található, az egyes régiók saját Azure Cognitive Search indexet használnak. Mivel a rendszer különböző indexeket használ, a pontszámok nem egyeznek meg pontosan. 


## <a name="no-match-found"></a>Nem található egyezés
Ha a rangsor nem találja a megfelelő egyezést, a rendszer visszaadja a 0,0-as vagy a "None" megbízhatósági pontszámot, és az alapértelmezett Válasz: "nem található megfelelő találat a KB-ban". Ezt az [alapértelmezett választ](#change-default-answer) felül lehet bírálni a végpontot meghívó robot vagy alkalmazás kódjában. Másik lehetőségként beállíthatja a felülbírálási választ is az Azure-ban, és ez megváltoztatja az alapértelmezett értéket az adott QnA Maker szolgáltatásban telepített összes Tudásbázis esetében.

## <a name="change-default-answer"></a>Alapértelmezett válasz módosítása

1. Lépjen a [Azure Portal](https://portal.azure.com) , és navigáljon a létrehozott QnA Maker szolgáltatást jelölő erőforráscsoporthoz.

2. Kattintson ide a **app Service**megnyitásához.

    ![A Azure Portal az App Service for QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson az **Alkalmazásbeállítások** elemre, és szerkessze a **DefaultAnswer** mezőt a kívánt alapértelmezett válaszra. Kattintson a **Save** (Mentés) gombra.

    ![Válassza ki az Alkalmazásbeállítások elemet, majd szerkessze a DefaultAnswer QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App Service-t

    ![A DefaultAnswer módosítása után indítsa újra a QnA Maker appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Támogatott adatforrások](./data-sources-supported.md)

