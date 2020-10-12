---
title: Nyelvi fogalmak – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker számos nyelven támogatja a Tudásbázis-tartalmakat. Az egyes QnA Maker szolgáltatásokat azonban egyetlen nyelvre kell lefoglalni. Az első létrehozott Tudásbázis, amely egy adott QnA Maker szolgáltatást céloz meg, beállítja a szolgáltatás nyelvét.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: b0d4250a6659996187923905955a9825a44cea42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132619"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>A QnA Maker Tudásbázis-tartalmának nyelvi támogatása

A szolgáltatás nyelve akkor van kiválasztva, amikor létrehozza az első tudásbázist az erőforrásban. Az erőforrás összes további tudásbázisának azonos nyelven kell lennie.

A nyelv meghatározza, hogy az eredmények mennyire fontosak QnA Maker a felhasználói lekérdezésekre adott válaszként.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Az erőforrás összes tudásbázisának egy nyelve

A QnA Maker esetében az első tudásbázis létrehozásakor állíthatja be a QnA-szolgáltatás nyelvét. A QnA Maker-erőforrásban szereplő összes Tudásbázisnak azonos nyelvűnek kell lennie. Ez a nyelv nem módosítható.

Ha egy erőforrás különböző nyelveken található tudásbázisokat hoz létre, negatívan befolyásolja az eredmények jelentőségét QnA Maker a felhasználói lekérdezésekre adott válaszként.

Tekintse át a [támogatott nyelvek](../overview/language-support.md#languages-supported) listáját, valamint azt, hogy a nyelvek hogyan befolyásolják az [egyeztetést és a megfelelőséget](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Válassza ki a nyelvet az első Tudásbázis létrehozásakor

A nyelv kiválasztása egy erőforrás első tudásbázisának létrehozásához szükséges lépések részét képezi.

![QnA Maker portál képernyőképe az első Tudásbázis nyelvének kiválasztásához](../media/language-support/select-language-when-creating-knowledge-base.png)

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

## <a name="verify-language"></a>Nyelv ellenőrzése

A QnA Maker-erőforrás nyelvét a QnA Maker szolgáltatás beállításai lapján ellenőrizheti.

![QnA Maker portál képernyőképe a Service Settings lapról](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis migrálása](../Tutorials/migrate-knowledge-base.md)
