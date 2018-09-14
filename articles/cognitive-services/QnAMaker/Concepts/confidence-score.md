---
title: Megbízhatósági pontszám – QnA Maker
titleSuffix: Azure Cognitive Services
description: Egy megbízhatósági pontszám a felhasználói kérdés és a visszaadott válasz között egyezés mértékét jelzi.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 30a29bdcb56eb39bcd004b5a7bf8f3526e2d5c75
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541734"
---
# <a name="confidence-score"></a>Megbízhatósági pontszám

Egy megbízhatósági pontszám a felhasználói kérdés és a visszaadott válasz között egyezés mértékét jelzi.

Ha egy felhasználó lekérdezése a Tudásbázis-tartalmat, több választ adott vissza lehet. A válaszok egy rangsorolt magabiztossági pontszámot csökkenő sorrendben adja vissza.

A megbízhatósági pontszám 0 és 100 között van.

|Pontszám értéke|Megbízhatóság|
|--|--|
|100|Pontos egyezéssel felhasználói lekérdezés és a egy KB-os kérdést|
|90|Magas megbízhatóság – a szavakat a legtöbb felel meg.|
|40-60|Valós megbízhatósági - fontos szavak megfelelő|
|10|Alacsony megbízhatósági - fontos szavak nem egyeznek|
|0|Nem egyezik meg az word|


## <a name="similar-confidence-scores"></a>Hasonló megbízhatósági pontszámok
Ha a többszöri válaszadást hasonló konfidencia-pontszám, valószínű, hogy a lekérdezés túl általános, és ezért egyező, több választ tartalmazó valószínűségét volt-e. Próbálja ki a QnA-tudásbázisok jobban építse fel, hogy minden QnA entitás rendelkezik egy különálló szándékot.


## <a name="improving-confidence-scores"></a>Megbízhatósági pontszámok javítása
A megbízhatósági pontszám, egy felhasználó adott válaszban javítása érdekében adhat hozzá a felhasználó lekérdezése a Tudásbázis következő, egy másik kérdésre adott válasz.
   
## <a name="confidence-score-differences"></a>Megbízhatósági pontszám különbségek
A megbízhatósági pontszám válasz változhatnak elhanyagolható mértékben a tesztelés és a Tudásbázis közzétett verziója között akkor is, ha a tartalom azonos. Ennek oka az, a teszt- és a közzétett Tudásbázis tartalmát a különböző Azure Search-indexek találhatók.

Itt látható a [közzététele](../How-To/publish-knowledge-base.md) művelet működését.


## <a name="no-match-found"></a>Nincs találat.
Nem megfelelő talál egyezést szerint a rangsorolás, amikor a 0,0 vagy "None" megbízhatósági pontszámot ad vissza, és az alapértelmezett válasz "nem szerepel jó a KB-ban található". Ez a robot vagy alkalmazás kód, a végpontot hív-e alapértelmezett válasz felül lehet bírálni. Azt is megteheti a felülbírálás válasz állítsa be az Azure-ban, és a egy adott QnA Maker szolgáltatást üzembe helyezett összes tudásbázisok az alapértelmezett értékre változik.

1. Nyissa meg a [az Azure portal](http://portal.azure.com) , és keresse meg az erőforráscsoport, amely a QnA Maker szolgáltatást létrehozott jelöli.

2. Ide kattintva megnyithatja a **App Service-ben**.

    ![Hozzáférés az App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson a **Alkalmazásbeállítások** és szerkesztheti a **DefaultAnswer** a kívánt alapértelmezett válasz mező. Kattintson a **Save** (Mentés) gombra.

    ![Alapértelmezett válasz módosítása](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App service

    ![A QnA Maker az App Service-újraindítás](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Támogatott adatforrások](./data-sources-supported.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker – áttekintés](../Overview/overview.md)
