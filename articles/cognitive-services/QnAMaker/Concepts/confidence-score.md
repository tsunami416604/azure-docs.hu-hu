---
title: Megbízhatósági pontszám – QnA Maker
titleSuffix: Azure Cognitive Services
description: A megbízhatósági pontszám azt jelzi, hogy a válasz-e a megfelelő egyezik a megadott felhasználói lekérdezés magabiztosan.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229105"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis megbízhatósági pontszám
Ha egy felhasználó lekérdezése Tudásbázis van, a QnA Maker azokra adott válaszokat, és a egy magabiztossági pontszámot ad vissza. Ezt az értéket, hogy a válasz-e a megfelelő egyezik a megadott felhasználói lekérdezés magabiztosan jelzi. 

A megbízhatósági pontszám értéke 0 és 100 közötti szám. A pontszám: 100, valószínűleg pontos egyezést, egy pontszám, a 0 azt jelenti, nem egyező válasz nem található, miközben. Minél nagyobb a pontszám - a a választ a nagyobb biztonsággal. Egy adott lekérdezésre vonatkozó lehet több választ ad vissza. Ebben az esetben a válaszokat a rendszer magabiztossági pontszámot csökkenő sorrendben adja vissza.

Az alábbi példában látható QnA egy entitást, 2 kérdése van. 


![Minta kérdés-válasz párt](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

A fenti példa-pontszámok, például a minta pontszám tartomány az alábbi – a különböző típusú felhasználói lekérdezések várható:


![Rangsorolás pontszám tartomány](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Az alábbi táblázat azt jelzi, hogy egy adott pontszám kapcsolódó tipikus magabiztosan.

|Pontszám értéke|Pontszám jelentése|. Példalekérdezés|
|--|--|--|
|90 - 100|A felhasználó lekérdezése és a egy KB-os kérdést pontos egyezés közelében|"A módosítások a közzététel után nem frissültek a KB-ban"|
|> 70|Magas megbízhatóság – általában egy jó választ, amely teljesen ad választ a felhasználó lekérdezése|"A KB-os közzétett, de nem frissül"|
|50 - 70|Közepes megbízhatósági – általában egy viszonylag jó választ, amely elsődleges célja, a felhasználó lekérdezése kell választ adni.|"Kell menteni a frissítés előtt közzé saját KB?"|
|30 – 50|Alacsony megbízhatósági – általában egy kapcsolódó választ, a felhasználói szándékot részleges válaszok|"Mire a Mentés és a vonat?"|
|< 30|Nagyon alacsony megbízhatósági – általában nem felel meg a felhasználó lekérdezése, de van néhány egyező szavak vagy kifejezések |"Ha is hozzáadhatok szinonimák saját KB"|
|0|Nincs egyezés, így a válasz nem ad vissza.|"A szolgáltatás mennyibe"|

## <a name="choose-a-score-threshold"></a>Válassza ki a pontszám küszöbértéket
A fenti táblázatban a legtöbb Tudásbázis mutatja, amelyek várhatóan pontszámokat. Mivel azonban minden KB különböző, és különböző típusú szavakat, szándékokat és célokat tartalmaz, javasoljuk, hogy tesztelje, és válassza ki az Ön számára legmegfelelőbb küszöbértéket. Alapértelmezés szerint a küszöbérték 0 értékre van állítva, hogy a rendszer minden lehetséges választ adjon vissza. A legtöbb Tudásbázis működéséhez ajánlott küszöbérték **50**.

A küszöbérték kiválasztásakor tartsa szem előtt a pontosság és lefedettség közötti egyensúly, és a Teljesítménybeállítások az igényei alapján küszöbértékét.

- Ha a **pontosság** (vagy pontosság) fontosabb a forgatókönyv esetében, akkor növelje a küszöbértéket. Így minden alkalommal, amikor visszatér a választ, csak egy sokkal több CONFIDENT megkülönbözteti a kis, és sokkal valószínűleg a válasz felhasználókat keres. Ebben az esetben előfordulhat, hogy végül hagyja a további kérdések, megválaszolatlan be. *Például:* ha a **70**-es küszöbértéket választja, akkor előfordulhat, hogy nem egyértelmű példákat ad a "mi a Save and Train?" kifejezésre.

- Ha a **lefedettség** (vagy visszahívás) fontosabb, és a lehető legtöbb kérdésre szeretne választ adni, akkor is, ha csak részleges kapcsolatban áll a felhasználó kérdésével – akkor csökkentse a küszöbértéket. Ez azt jelenti, hogy ott lehet további olyan esetekben, ahol a válasz nem felel meg a felhasználó a tényleges lekérdezés, de néhány egyéb némileg kapcsolódó választ ad. *Például:* ha a **30**. küszöbértéket adja meg, választ kaphat a lekérdezésekre, például "hol szerkeszthetem a kb-ot?"

> [!NOTE]
> A QnA Maker újabb verzióit közé tartozik a pontozási logikát, és hatással lehetnek a küszöbértéket. Frissíti a szolgáltatás bármikor ügyeljen arra, hogy tesztelése és finomhangolása a küszöbértéket, ha szükséges. [Itt](https://www.qnamaker.ai/UserSettings)megtekintheti a QnA szolgáltatás verzióját, és megtudhatja, hogyan kérheti le a [legújabb frissítéseket.](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Küszöbérték beállítása 

A küszöbérték pontszámát állítsa be a [GENERATEANSWER API JSON-törzsének](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)tulajdonságára. Ez azt jelenti, hogy a GenerateAnswer minden egyes hívása esetében be kell állítania azt. 

A bot-keretrendszerben állítsa be a pontszámot a ( [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) vagy [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs)) beállítások objektum részeként.

## <a name="improve-confidence-scores"></a>Megbízhatósági pontszámukat
A megbízhatósági pontszám, egy felhasználó adott válaszban javítása érdekében adhat hozzá a felhasználó lekérdezése a Tudásbázis következő, egy másik kérdésre adott válasz. A kis-és nagybetűk megkülönböztetésére is használható [, ha](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) szinonimákat ad hozzá a tudásbázisban található kulcsszavakhoz.


## <a name="similar-confidence-scores"></a>Hasonló megbízhatósági pontszámok
Ha a többszöri válaszadást hasonló konfidencia-pontszám, valószínű, hogy a lekérdezés túl általános, és ezért egyező, több választ tartalmazó valószínűségét volt-e. Próbálja ki a QnA-tudásbázisok jobban építse fel, hogy minden QnA entitás rendelkezik egy különálló szándékot.


## <a name="confidence-score-differences-between-test-and-production"></a>A megbízhatósági pontszám különbsége a tesztelés és a gyártás között
A megbízhatósági pontszám válasz változhatnak elhanyagolható mértékben a tesztelés és a Tudásbázis közzétett verziója között akkor is, ha a tartalom azonos. Ennek az az oka, hogy a teszt tartalma és a közzétett Tudásbázis különböző Azure Cognitive Search indexekben található. 

A tesztelési index tartalmazza a Tudásbázis összes QnA. A tesztelési index lekérdezésekor a lekérdezés a teljes indexre vonatkozik, majd az eredmények az adott tudásbázishoz tartozó partícióra korlátozódnak. Ha a tesztelési lekérdezés eredménye negatívan befolyásolja a Tudásbázis érvényesítésének képességét, a következőket teheti:
* a tudásbázist a következők egyikével rendezheti:
    * 1 KB-ig korlátozott erőforrás: korlátozza az egyetlen QnA erőforrást (és az eredményül kapott Azure Cognitive Search tesztelési indexet) egyetlen tudásbázisba. 
    * 2 erőforrás-1 az ellenőrzéshez, 1 az éles környezethez: két QnA Maker erőforrással rendelkezik, amelyek közül az egyiket a teszteléshez (saját teszttel és éles indexekkel), a másikat pedig a termékhez (a saját tesztelési és éles indexekkel is rendelkezik)
* és mindig ugyanazokat a paramétereket használja **[, mint például](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** a tesztelési és a üzemi Tudásbázis lekérdezéséhez.

Ha közzétesz egy tudásbázist, a Tudásbázisban a kérdés és a válasz tartalma a tesztelési indexből egy éles indexbe kerül az Azure Search-ben. Nézze meg, hogyan működik a [közzétételi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) művelet.

Ha a Tudásbázis különböző régiókban található, az egyes régiók saját Azure Cognitive Search indexet használnak. Mivel a rendszer különböző indexeket használ, a pontszámok nem egyeznek meg pontosan. 


## <a name="no-match-found"></a>Nincs találat.
Nem megfelelő talál egyezést szerint a rangsorolás, amikor a 0,0 vagy "None" megbízhatósági pontszámot ad vissza, és az alapértelmezett válasz "nem szerepel jó a KB-ban található". Ezt az [alapértelmezett választ](#change-default-answer) felül lehet bírálni a végpontot meghívó robot vagy alkalmazás kódjában. Azt is megteheti a felülbírálás válasz állítsa be az Azure-ban, és a egy adott QnA Maker szolgáltatást üzembe helyezett összes tudásbázisok az alapértelmezett értékre változik.

## <a name="change-default-answer"></a>Alapértelmezett válasz módosítása

1. Lépjen a [Azure Portal](https://portal.azure.com) , és navigáljon a létrehozott QnA Maker szolgáltatást jelölő erőforráscsoporthoz.

2. Kattintson ide a **app Service**megnyitásához.

    ![Az Azure Portalon férhet hozzá a QnA Maker az App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson az **Alkalmazásbeállítások** elemre, és szerkessze a **DefaultAnswer** mezőt a kívánt alapértelmezett válaszra. Kattintson a **Save** (Mentés) gombra.

    ![Válassza ki az alkalmazás beállításait, és szerkesztheti a QnA Maker DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App service

    ![Miután a DefaultAnswer módosítja, indítsa újra a QnA Maker App Service](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Támogatott adatforrások](./data-sources-supported.md)

