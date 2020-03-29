---
title: Megbízhatósági pontszám - QnA Maker
titleSuffix: Azure Cognitive Services
description: A tudásbázist közzé kell tenni. A közzététel után a tudásbázis lekérdezése a futásidejű előrejelzési végpont a generateAnswer API használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843452"
---
# <a name="the-confidence-score-of-an-answer"></a>A válasz megbízhatósági pontszáma
Ha egy felhasználói lekérdezés tanusítegy tudásbázist, a QnA Maker releváns válaszokat ad vissza, valamint megbízhatósági pontszámot. Ez a pontszám azt jelzi, hogy a válasz a megfelelő egyezést az adott felhasználói lekérdezés.

A megbízhatósági pontszám 0 és 100 közötti szám. A 100-as pontszám valószínűleg pontos egyezés, míg a 0 pont azt jelenti, hogy nem találtak egyező választ. Minél magasabb a pontszám, annál nagyobb a bizalom a válaszban. Egy adott lekérdezés esetén több választ is visszaadhat. Ebben az esetben a válaszok at csökkenő megbízhatósági pontszám szerint adja vissza.

Az alábbi példában egy QnA entitás látható, 2 kérdéssel.


![Minta QnA pár](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

A fenti példában, akkor számíthat pontszámok, mint a minta pontszám tartomány alatt- a különböző típusú felhasználói lekérdezések:


![Ranker pontszám tartomány](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Az alábbi táblázat egy adott pontszámhoz kapcsolódó tipikus megbízhatóságot jelöl.

|Pontszám értéke|Pontszám jelentése|Példa lekérdezésre|
|--|--|--|
|90 - 100|A felhasználói lekérdezés közel pontos egyezése és kb kérdés|"A módosítások nem frissülnek kb-ban a közzététel után"|
|> 70|Nagy bizalom - általában egy jó válasz, hogy teljesen választ a felhasználó lekérdezése|"Közzétettem a KB-t, de nem frissült"|
|50 - 70|Közepes megbízhatóság - általában egy meglehetősen jó válasz, hogy meg kell válaszolni a fő szándéka a felhasználói lekérdezés|"A tudásbázis közzététele előtt mentsem a frissítéseket?"|
|30 - 50|Alacsony megbízhatóság - általában egy kapcsolódó válasz, amely részben választ ad a felhasználó szándékának|" Mit csinál a mentés és a vonat?"|
|< 30|Nagyon alacsony megbízhatóság - általában nem válaszol a felhasználó lekérdezésére, de van néhány megfelelő szó vagy kifejezés |" Hol adhatok szinonimákat a KB-hoz"|
|0|Nincs egyezés, így a válasz nem tér vissza.|"Mennyibe kerül a szolgáltatás"|

## <a name="choose-a-score-threshold"></a>Pontszámküszöb kiválasztása
A fenti táblázat a legtöbb kb-n várható pontszámokat mutatja. Mivel azonban minden kb más, és különböző típusú szavakkal, szándékokkal és célokkal rendelkezik, javasoljuk, hogy tesztelje és válassza ki azt a küszöbértéket, amely a legjobban működik az Ön számára. Alapértelmezés szerint a küszöbérték 0, így az összes lehetséges választ adja vissza. Az ajánlott küszöbérték, amely a legtöbb KB esetében működik, **50**.

A küszöbérték kiválasztásakor tartsa szem előtt a pontosság és a lefedettség közötti egyensúlyt, és a küszöbértéket a követelmények alapján finomkodik.

- Ha **a pontosság** (vagy pontosság) fontosabb a forgatókönyv, majd növelje a küszöbértéket. Ily módon, minden alkalommal, amikor visszatér a választ, ez lesz egy sokkal magabiztosabb esetben, és sokkal valószínűbb, hogy a válasz a felhasználók keresnek. Ebben az esetben előfordulhat, hogy a végén több kérdést hagy megválaszolatlanul. *Például:* ha a küszöböt **70-re**teszi , előfordulhat, hogy kihagy néhány kétértelmű példát, mint a "mi a mentés és a vonat?".

- Ha **a lefedettség** (vagy visszahívás) fontosabb, és a lehető legtöbb kérdésre szeretne válaszolni, még akkor is, ha csak részleges kapcsolat van a felhasználó kérdésével, akkor csökkentse a küszöbértéket. Ez azt jelenti, hogy több olyan eset is lehet, amikor a válasz nem válaszol a felhasználó tényleges lekérdezésére, hanem más, némileg kapcsolódó választ ad. *Például:* ha a **küszöbértéket 30-ra**teszi, válaszokat adhat az olyan lekérdezésekre, mint a "Hol szerkeszthetem a kb-t?"

> [!NOTE]
> A QnA Maker újabb verziói a pontozási logika fejlesztéseit tartalmazzák, és befolyásolhatják a küszöbértéket. Minden alkalommal, amikor frissíti a szolgáltatást, győződjön meg róla, hogy tesztelje és csípés a küszöbértéket, ha szükséges. A QnA-szolgáltatás verzióját [itt](https://www.qnamaker.ai/UserSettings)ellenőrizheti, és itt láthatja, hogyan szerezheti be a legújabb [frissítéseket.](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Küszöbérték beállítása

Állítsa be a küszöbértéket a [GenerateAnswer API JSON törzs](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)tulajdonságaként. Ez azt jelenti, hogy minden híváshoz a GenerateAnswer beállítását állítja be.

A robot keret, állítsa be a pontszám részeként a beállítások objektum [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) vagy [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>A megbízhatósági pontszámok javítása
Egy adott felhasználói lekérdezésre adott válasz megbízhatósági pontszámának javítása érdekében a felhasználói lekérdezést hozzáadhatja a tudásbázishoz, mint a válasz alternatív kérdése. A kis- és nagybetűk et nem megkülönböztető [szavak módosításával](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) szinonimákat adhat a tudásbázisban lévő kulcsszavakhoz.


## <a name="similar-confidence-scores"></a>Hasonló megbízhatósági pontszámok
Ha több válasz hasonló megbízhatósági pontszámmal rendelkezik, valószínű, hogy a lekérdezés túl általános volt, és ezért azonos valószínűséggel, több válaszsal egyeztethető. Próbálja meg jobban strukturálni a QnA-kat, hogy minden QnA entitásnak külön szándéka legyen.


## <a name="confidence-score-differences-between-test-and-production"></a>Megbízhatósági pontszám különbségek a teszt és a termelés között
A válasz megbízhatósági pontszáma elhanyagolhatóan változhat a tudásbázis teszt- és közzétett változata között, még akkor is, ha a tartalom megegyezik. Ennek az az oka, hogy a teszt és a közzétett tudásbázis tartalma különböző Azure Cognitive Search-indexekben található.

A tesztindex tartalmazza a tudásbázisok összes QnA párját. A tesztindex lekérdezésekének, a lekérdezés a teljes index, majd az eredmények az adott tudásbázis partíciójára korlátozódnak. Ha a tesztlekérdezés eredményei negatívan befolyásolják a tudásbázis érvényesítésének képességét, a következőket teheti:
* a tudásbázist az alábbi módon rendezheti:
    * 1 erőforrás 1 KB-ra korlátozva: korlátozza az egyetlen QnA-erőforrást (és az eredményül kapott Azure Cognitive Search tesztindexet) egyetlen tudásbázisra.
    * 2 erőforrás - 1 a teszteléshez, 1 a termeléshez: két QnA Maker erőforrással rendelkezik, amelyek közül az egyik tesztelésre szolgál (saját teszt- és termelési indexekkel) és egy termékre (saját teszt- és termelési indexekkel is rendelkezik)
* és mindig ugyanazokat a paramétereket használja, például **[a felülieket,](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** amikor a teszt- és a termelési tudásbázist is lekérdezi

Tudásbázis közzétételekor a tudásbázis kérdés- és választartalma a tesztindexből az Azure search termelési indexébe kerül. Tekintse meg, hogyan működik a [közzétételi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) művelet.

Ha rendelkezik egy tudásbázis különböző régiókban, minden régió saját Azure Cognitive Search indexet használ. Mivel a különböző indexek vannak használva, a pontszámok nem lesz pontosan ugyanaz.


## <a name="no-match-found"></a>Nem található egyezés
Ha a ranker nem talál jó egyezést, a rendszer a 0.0 vagy a "Nincs" megbízhatósági pontszámot adja vissza, és az alapértelmezett válasz a "Nincs jó egyezés a KB-ban". Ezt az [alapértelmezett választ](../How-To/metadata-generateanswer-usage.md) felülírhatja a célpontot hívó robot- vagy alkalmazáskódban. Másik lehetőségként is beállíthatja a felülbírálási választ az Azure-ban, és ez módosítja az alapértelmezett egy adott QnA Maker szolgáltatásban telepített összes tudásbázis.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Bevált módszerek](./best-practices.md)

