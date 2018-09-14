---
title: Ajánlott eljárások – QnA Maker
titlesuffix: Azure Cognitive Services
description: Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: c82c117d149da39fba7b9a243aebb3e127540881
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542927"
---
# <a name="best-practices"></a>Ajánlott eljárások
A [Tudásbázis fejlesztési életciklus](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a KB-os-végpontok kezelése meg. Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.

## <a name="extraction"></a>Kinyerés
A QnA Maker folyamatosan a algoritmusokat használnak a QnA-tudásbázisok kinyerése a tartalom javítása és kibontása a fájl- és HTML-oldal támogatott formátumok listáját. Kövesse a [irányelvek](../Concepts/data-sources-supported.md) a dokumentum típusa a következők alapján kinyerési kinyerését. 

Gyakori kérdéseket tartalmazó oldalak általában az önálló és egyéb információkat a nem összevont kell lennie. Kézikönyvek egyértelmű címsorok és lehetőleg index lapot kell rendelkeznie. 

## <a name="rankingmatching"></a>Ennek a területnek/egyeztetése
Győződjön meg arról, amelyek a lehető legjobban kihasználják támogatja a QnA Maker rangsorolási szolgáltatásokat. Ezzel javul a valószínűsége, hogy egy adott felhasználó lekérdezési válasz érkezik a megfelelő választ.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Kérdések az eredetitől eltérő](../How-To/edit-knowledge-base.md) felhasználói lekérdezés egyezést valószínűségének növelése. Alternatív kérdések akkor hasznos, ha több módon is ugyanezt a kérdést kérni. Ide tartozhatnak a módosításokat a mondat struktúra (például *"Áll rendelkezésre álló ideiglenes?"* és *"Rendelkezik autó park?"* ) vagy a word-stílus és a szleng felismerését változásai (például *"Hi"* és *"Yo"*, *"Nincs Hey!"* ).

### <a name="use-metadata-filters"></a>Fájlmetaadat-szűrők használata
[Metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszi az eredményeket a felhasználói lekérdezés szűrők alapján. A Tudásbázis választ a metaadat-kódcímke alapján is eltérnek, akkor is, ha a lekérdezés nem ugyanaz. Például *"hol is található ideiglenes"* egy másik választ is rendelkezhet, ha az éttermi ág helye eltérő –, hogy a metaadatok van *helye: Seattle* és *helye: Redmond*.)

### <a name="use-synonyms"></a>A szinonimák használata
Van néhány támogatás angol nyelven szinonimák, bár [word-átalakítások](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) szinonimák hozzáadása a kulcsszavak, amelyek különböző (Példa: *vásárlása* -> *megvásárlása*  vagy *netbanking* -> *banki net*. Szinonimák kell adni a QnA Maker szolgáltatás szintű és a szolgáltatás minden tudásbázisok által megosztott.

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával tesz különbséget a kérdések
A QnA Maker egyezés rang algoritmusok, amelyek megfelelnek egy kérdést a Tudásbázis rendelkező felhasználói lekérdezések akkor működnek a legjobban, minden egyes kérdést címek eltérő igényeit. Ismétlődő kérdések között ugyanazon szó csökkenti annak valószínűsége, hogy a helyes válasz egy adott felhasználó lekérdezés azokat a szavakat az van kiválasztva.

## <a name="collaborate"></a>Együttműködés
A QnA Maker lehetővé teszi a felhasználóknak [együttműködés](../How-to/collaborate-knowledge-base.md) a Tudásbázis. Felhasználók az Azure QnA Maker erőforráscsoporthoz hozzáférést igényelnek a tudásbázisok eléréséhez. Egyes szervezetek érdemes kiszervezik a Tudásbázis szerkesztéséhez és a karbantartás, és továbbra is felhasználhatják az Azure-erőforrásokhoz való hozzáférés védelme. Ez a szerkesztő, jóváhagyó modell valósítható meg beállításával két azonos [QnA Maker szolgáltatás](../How-to/set-up-qnamaker-service-azure.md) eltérő előfizetésekben és a egy, a Szerkesztés tesztelés ciklusig az újbóli kijelölése. A tesztelés után a Tudásbázis-tartalmat a vihetők át egy [az import-export](../Tutorials/migrate-knowledge-base.md) feldolgozása a QnA Maker szolgáltatás a jóváhagyó, végül pedig a Tudásbázis közzététele, és a végpont frissítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)

