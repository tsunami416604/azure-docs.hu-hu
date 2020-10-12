---
title: Nyelvi támogatás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A tudásalapú QnA Maker által támogatott kulturális környezetek, természetes nyelvek listája. Ne keverje a nyelveket ugyanabban a Tudásbázisban.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650893"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker erőforrások és tudásbázisok nyelvi támogatása

A szolgáltatás nyelve akkor van kiválasztva, amikor létrehozza az első tudásbázist az erőforrásban. Az erőforrás összes további tudásbázisának azonos nyelven kell lennie. 

A nyelv meghatározza, hogy az eredmények mennyire fontosak QnA Maker a felhasználói lekérdezésekre adott válaszként. A QnA Maker erőforrás és az erőforráson belüli összes Tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges ahhoz, hogy a lekérdezéshez a legjobb eredményt adja.

## <a name="single-language-per-resource"></a>Egy nyelv/erőforrás

A következőket ajánljuk figyelmébe:

* A QnA Maker szolgáltatás és annak összes tudásbázisa csak egy nyelvet támogat.
* A nyelv explicit módon be van állítva a szolgáltatás első tudásbázisának létrehozásakor
* A nyelv meghatározása a Tudásbázis létrehozásakor hozzáadott fájlokból és URL-címekből történik
* A nyelv nem módosítható a szolgáltatás bármely más tudásbázisa esetében.
* A nyelvet a Cognitive Search szolgáltatás (ranker #1) és a QnA Maker szolgáltatás (ranker #2) használja a legjobb válasz lekérdezésre való létrehozásához.

## <a name="supporting-multiple-languages"></a>Több nyelv támogatása

Ha egy Tudásbázis-rendszer támogatására van szüksége, amely több nyelvet is tartalmaz, a következőket teheti:

* A [Translator szolgáltatással](../../translator/translator-info-overview.md) egyetlen nyelven fordíthatja le a kérdést, mielőtt elküldené a kérdést a Tudásbázisnak. Ez lehetővé teszi, hogy a minőségre koncentráljon, valamint a alternatív kérdések és válaszok minőségére.
* Minden nyelvhez hozzon létre egy QnA Maker erőforrást és egy tudásbázist az adott erőforráson belül. Ez lehetővé teszi, hogy külön alternatív kérdéseket kezeljen, és az egyes nyelvekhez árnyaltabb szövegeket adjon. Ez sokkal nagyobb rugalmasságot biztosít, de sokkal nagyobb karbantartási költségeket igényel, ha a kérdések és válaszok minden nyelven megváltoznak.

QnA Maker [által támogatott nyelvek](../overview/language-support.md) áttekintése.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Minden nyelv támogatása QnA Maker erőforrással

* QnA Maker-erőforrás létrehozása minden nyelvhez
* Csak az adott nyelvhez tartozó fájlok és URL-címek hozzáadása
* A nyelv azonosításához használjon egy elnevezési konvenciót az erőforráshoz. Ilyen például `qna-maker-fr` a francia dokumentumok összes tudásbázisa


## <a name="languages-supported"></a>Támogatott nyelvek

A következő lista a QnA Maker erőforrás által támogatott nyelveket tartalmazza. 

|Nyelv|
|--|
|Arab|
|örmény|
|Bangla|
|Baszk|
|Bolgár|
|Katalán|
|Chinese_Simplified|
|Chinese_Traditional|
|Horvát|
|Cseh|
|Dán|
|Holland|
|Angol|
|Észt|
|Finn|
|Francia|
|Gallego|
|Német|
|Görög|
|gudzsaráti|
|Héber|
|Hindi|
|Magyar|
|Izlandi|
|Indonéz|
|Ír|
|Olasz|
|Japán|
|kannada|
|Koreai|
|Lett|
|Litván|
|malajálam|
|Maláj|
|Norvég|
|Lengyel|
|Portugál|
|pandzsábi|
|Román|
|Orosz|
|Serbian_Cyrillic|
|Serbian_Latin|
|Szlovák|
|Szlovén|
|Spanyol|
|Svéd|
|tamil|
|telugu|
|Thai|
|Török|
|Ukrán|
|urdu|
|Vietnámi|

## <a name="query-matching-and-relevance"></a>Lekérdezés megfeleltetése és relevanciája
A QnA Maker az [Azure Cognitive Search Language Analyzers](https://docs.microsoft.com/rest/api/searchservice/language-support) szolgáltatástól függ az eredmények biztosításához.

Habár az Azure Cognitive Search képességei támogatott nyelveken is elérhetők, QnA Maker rendelkezik egy további, az Azure keresési eredményein felül található rangsorsal. Ebben a Ranger-modellben néhány speciális szemantikai és Word-alapú funkciót használunk a következő nyelveken.

|Nyelvek további rangsorsal|
|--|
|Kínai|
|Cseh|
|Holland|
|Angol|
|Francia|
|Német|
|Magyar|
|Olasz|
|Japán|
|Koreai|
|Lengyel|
|Portugál|
|Spanyol|
|Svéd|

Ez a további rangsorolás a QnA Maker rangsorának belső működése.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Nyelv kiválasztása](../how-to/language-knowledge-base.md)
