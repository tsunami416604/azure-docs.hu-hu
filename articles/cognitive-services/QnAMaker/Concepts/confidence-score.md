---
title: Megbízhatósági pontszám - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Foglalja össze az vetett bizalmat pontszám
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348658"
---
# <a name="confidence-score"></a>Megbízhatósági pontszám

Abban, hogy pontszámot azt jelzi, hogy a felhasználói kérdés és válasza egyezését mértékű.

Amikor egy felhasználó lekérdezés Tudásbázis tartalom egyezik, egynél több válasza lehet. A válaszokat a rendszer egy rangsorolt abban, hogy pontszám csökkenő sorrendben adja vissza.

A megbízhatósági érték 0 és 100 között.

|Pontszám érték|Megbízhatóság|
|--|--|
|100|Pontos egyezéssel felhasználói lekérdezés és a KB kérdés|
|90|Magas megbízhatóság – a szavakat a legtöbb felel meg|
|40-60|Igazságos folyamatot – fontos szavak megfelelő|
|10|Alacsony folyamatot – fontos szavak nem egyeznek.|
|0|Nem word megfelelő|


## <a name="similar-confidence-scores"></a>Pontszámok. abban, hogy hasonló.
Ha több válaszok hasonló abban, hogy pontszámot, valószínű, hogy a lekérdezés túl általános, és ezért egyező több választ tartalmazó valószínűségét volt-e. Próbálja meg a QnAs jobban struktúra, úgy, hogy minden kérdés-válasz entitás különböző célt.


## <a name="improving-confidence-scores"></a>Megbízhatósági pontszámok javítása
A abban, hogy egy felhasználó adott válaszban pontszám javításához adhat hozzá a felhasználó lekérdezése a Tudásbázis mint egy másik kérdésre adott válasz.
   
## <a name="confidence-score-differences"></a>Megbízhatósági pontszám különbségek
Az vetett bizalmat pontszám válasz módosulhatnak elhanyagolható mértékben a teszt- és a Tudásbázis közzétett változata akkor is, ha a tartalom azonos. Ennek oka az, a teszt- és a közzétett Tudásbázis tartalmának különböző Azure keresési indexek találhatók.

Itt látható bemutatja a [közzététele](../How-To/publish-knowledge-base.md) művelet működését.


## <a name="no-match-found"></a>Nincs egyezés
Nem megfelelő egyezés a ranker, amikor a abban, hogy pontszám 0,0 vagy "None" ad vissza, és a rendszer az alapértelmezett választ, "Nem található a KB a helyes megfelelő". Ez a kódban botot vagy alkalmazás a végpont meghívása alapértelmezett a válasz lehet felülbírálni. Alternatív megoldásként a felülbírálás válasz állíthat be az Azure-ban, és egy adott kérdések és válaszok készítő szolgáltatást telepített összes Tudásbázis esetében az alapértelmezett értékre változik.

1. Lépjen a [Azure-portálon](http://portal.azure.com) , és keresse meg az erőforráscsoportot, amely a kérdések és válaszok készítő szolgáltatás létrehozott jelöli.

2. Ide kattintva megnyithatja a **App Service**.

    ![Hozzáférés az App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson a **Alkalmazásbeállítások** és szerkesztése a **DefaultAnswer** mezőbe a kívánt alapértelmezett válaszhoz. Kattintson a **Save** (Mentés) gombra.

    ![Alapértelmezett válasz módosítása](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App service

    ![Kérdések és válaszok készítő App Service-újraindítás](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Támogatott adatforrások](./data-sources-supported.md)

## <a name="see-also"></a>Lásd még 

[Kérdések és válaszok készítő áttekintése](../Overview/overview.md)
