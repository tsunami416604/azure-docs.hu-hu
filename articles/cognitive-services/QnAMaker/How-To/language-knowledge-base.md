---
title: Nyelvi fogalmak - QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker számos nyelven támogatja a tudásbázis tartalmát. Azonban minden QnA Maker szolgáltatást egyetlen nyelvhez kell fenntartani. Az első létrehozott tudásbázis, amely egy adott QnA Maker szolgáltatást céloz meg, beállítja a szolgáltatás nyelvét.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220632"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Tudásbázis-tartalom nyelvi támogatása a QnA Maker számára

A szolgáltatás nyelve az erőforrás első tudásbázisának létrehozásakor kerül kiválasztásra. Az erőforrás ban található összes további tudásbázisnak ugyanazon a nyelven kell lennie.

A nyelv határozza meg a QnA Maker által a felhasználói lekérdezésekre adott válaszként biztosított eredmények relevanciáját.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Egy nyelv az erőforrás összes tudásbázisához

A QnA Maker lehetővé teszi a QnA szolgáltatás nyelvének kiválasztását, miközben létrehozza az első tudásbázist. A QnA Maker erőforrás összes tudásbázisa esetében mindegyiknek ugyanazon a nyelven kell lennie. Ezt a nyelvet nem lehet megváltoztatni.

A tudásbázisok létrehozása különböző nyelveken egy erőforrásban negatívan befolyásolja a QnA Maker által a felhasználói lekérdezésekre adott válaszként elért eredmények relevanciáját.

Tekintse át a [támogatott nyelvek](../overview/language-support.md#languages-supported) listáját, valamint azt, hogy a nyelvek hogyan befolyásolják az [egyeztetést és a relevanciát.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Nyelv kiválasztása az első tudásbázis létrehozásakor

A nyelvkiválasztása az erőforrás első tudásbázisának létrehozásához szükséges lépések része.

![QnA Maker portál képernyőképe az első tudásbázis nyelvének kiválasztásáról](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Lekérdezésegyeztetés és relevancia
A QnA Maker az [Azure Cognitive Search nyelvi elemzőitől](https://docs.microsoft.com/rest/api/searchservice/language-support) függ az eredmények biztosításához.

Míg az Azure Cognitive Search képességek egyenrangú a támogatott nyelvek, QnA Maker egy további ranker, amely az Azure keresési eredmények felett ül. Ebben a ranker modellben néhány speciális szemantikai és szóalapú funkciót használunk a következő nyelveken.

|Nyelvek további ranker|
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

Ez a további rangsor a QnA Maker rangadójának belső feladata.

## <a name="verify-language"></a>Nyelv ellenőrzése

A QnA Maker erőforrás nyelvét a QnA Maker szolgáltatásbeállítások lapján ellenőrizheti.

![A QnA Maker portál képernyőképe a Szolgáltatás beállítások lapjáról](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis migrálása](../Tutorials/migrate-knowledge-base.md)
