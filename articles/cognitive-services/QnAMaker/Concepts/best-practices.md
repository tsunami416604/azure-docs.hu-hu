---
title: Ajánlott eljárásokat – kérdések és válaszok készítő - Azure kognitív szolgáltatások |} Microsoft Docs
description: Az alábbi gyakorlati tanácsok segítségével növelheti a Tudásbázis, és jobb eredményeket nyújt a végfelhasználók számára az alkalmazás/Csevegés botot.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349902"
---
# <a name="best-practices"></a>Ajánlott eljárások
A [Tudásbázis fejlesztési életciklus során](../Concepts/development-lifecycle-knowledge-base.md) végig is vezeti Önt a KB-végpontok kezelése meg. Az alábbi gyakorlati tanácsok segítségével növelheti a Tudásbázis, és jobb eredményeket nyújt a végfelhasználók számára az alkalmazás/Csevegés botot.

## <a name="extraction"></a>Kinyerés
Kérdések és válaszok készítő folyamatosan a algoritmust, amelyet QnAs kibontani a tartalom javítása és a lista a fájl- és a támogatott formátumok HTML-oldal kibontása. Kövesse a [irányelvek](../Concepts/data-sources-supported.md) a dokumentum típusa a következők alapján kibontási kibontása. 

Gyakran ismételt kérdések lapok általában az önálló és ezeket az adatokat nem kombinált kell lennie. A termék laborútmutatókkal törölje a jelet a fejlécére kattintva rendezhető, és lehetőség szerint indexfájlja kell rendelkeznie. 

## <a name="rankingmatching"></a>Rangsorolási/megfelelő
Ellenőrizze, hogy a kérdések és válaszok Maker támogatja rangsorolási szolgáltatásokat a lehető legjobb felhasználását teszi. Ez javítja a valószínűsége, amely egy adott felhasználó lekérdezés melléket megfelelő válasz.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Alternatív kérdések](../How-To/edit-knowledge-base.md) javítása felhasználói lekérdezéssel egyezés valószínűségét. Alternatív kérdések akkor hasznos, ha többféleképpen is ugyanezt a kérdést kérni. Ez magában foglalhatja módosítások a mondatok struktúrában (például *"Az elérhető várakozást?"* és *"Rendelkezik car park?"* ) vagy a word-stílus és zsargon módosításokat (például *"Hi"* és *"Yo"*, *"Nincs Hey!"* ).

### <a name="use-metadata-filters"></a>Metaadat-szűrők használata
[Metaadatok](../How-To/edit-knowledge-base.md) lehetőséget biztosít a felhasználói lekérdezések szűrők alapján az eredmények szűkítéséhez. A Tudásbázis válasz eltérőek lehetnek a metadata kód alapján még akkor is, ha a lekérdezés megegyezik. Például *"hol található található várakozást"* egy másik választ a rendelkezhet, ha a éttermi fiókirodai helye eltérő – Ez azt jelenti, hogy a metaadatok *helye: Budapest* és *helye: Redmond*.)

### <a name="use-synonyms"></a>Szinonimák használata
Míg néhány szinonimák angol nyelven támogatása, használja [változásokból eredő word](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) szinonimák hozzáadása kulcsszavakat, amelyekre különböző utat (például: *megvásárlása* -> *vásárlása*  vagy *netbanking* -> *banki net*. Szinonimák a kérdések és válaszok készítő szolgáltatásiszint-hozzáadott legyen, és minden Tudásbázis körrel közösen használnak a szolgáltatásban.

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával megkülönböztetéséhez kérdések
Kérdések és válaszok készítő egyezés dimenziószáma algoritmusok, amelyek megfelelnek a Tudásbázis fel kérdést tartalmazó felhasználói lekérdezést legjobb működik, ha minden kérdés címek eltérő igényeit. Ismétlődő kérdések közé azonos Word csökkenti annak a valószínűségét, egy adott felhasználó lekérdezés listázzák a kiválasztott a megfelelő választ.

## <a name="collaborate"></a>Együttműködés
Kérdések és válaszok Maker lehetővé teszi a felhasználóknak [közötti együttműködés](../How-to/collaborate-knowledge-base.md) meg a Tudásbázis. Felhasználók a kérdések és válaszok készítő Azure erőforráscsoport hozzáférést igényelnek a Tudásbázis körrel eléréséhez. Egyes szervezetek érdemes erőforrás kihelyezése a Tudásbázis szerkesztésével és a karbantartás, és továbbra is tudja védeni az Azure-erőforrások eléréséhez. A szerkesztő-jóváhagyó modell valósítható meg beállításával két azonos [kérdések és válaszok készítő szolgáltatások](../How-to/set-up-qnamaker-service-azure.md) különböző előfizetésekhez és egy, a Szerkesztés tesztelés ciklus kijelölése. Ha vizsgálat befejeződött, a Tudásbázis tartalmát át lehet vinni az egy [importálási-exportálási](../Tutorials/migrate-knowledge-base.md) feldolgozása a kérdések és válaszok készítő szolgáltatáshoz, a jóváhagyó, végül a Tudásbázis közzéteszi, és a végpont frissítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)

