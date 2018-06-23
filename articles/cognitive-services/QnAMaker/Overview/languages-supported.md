---
title: Támogatott nyelvek – kérdések és válaszok készítő - Azure kognitív szolgáltatások |} Microsoft Docs
description: Ismerje meg, milyen nyelveket támogatja a kérdések és válaszok készítő.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349243"
---
# <a name="supported-languages"></a>Támogatott nyelvek

A Tudásbázis nyelvének hatással van kérdések és válaszok Maker automatikus-kivonat kérdéseket, és a válaszok [források](../Concepts/data-sources-supported.md), valamint az a kérdés-válasz készítő biztosít a felhasználó lekérdezéseire eredmények.

## <a name="auto-extraction"></a>Automatikus kivonása
Kérdések és válaszok készítő kérdés-válasz kibontási támogatja tetszőleges nyelv lapon, de a kibontási hatékonyságát sokkal nagyobb a következő nyelvekhez, a kérdések és válaszok készítő kulcsszavak segítségével azonosíthatja a kérdések.

|Támogatott nyelvek| Területi beállítás|
|-----|----|
|Angol|en-*|
|francia|FR-*|
|olasz|IT-*|
|német|de-*|
|spanyol|es-*|

## <a name="query-matching-and-relevance"></a>Megfelelő és relevanciájának lekérdezése
Kérdések és válaszok készítő függ [nyelvi elemzőkkel](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) az Azure search biztosítani azt az eredményt. Különleges újra rangsoroló funkciók érhetők el En-* nyelveket, amelyek lehetővé teszik a jobb relevanciájának.

Kérdések és válaszok készítő automatikus-észleli a Tudásbázis nyelvének létrehozása során, és ennek megfelelően állítja be az elemző eszközt. A következő nyelveken Tudásbázis körrel hozhat létre. Olvasási [ez](../How-To/language-knowledge-base.md) nyelvű kérdések és válaszok készítő kezelésének további információt.


> [!Tip]
> Nyelvi elemzőkkel beállítása után, nem módosítható. Emellett a nyelvi elemzőt vonatkozik-e a Tudásbázis körrel egy [Maker kérdések és válaszok szolgáltatás](../How-To/set-up-qnamaker-service-azure.md). Tervezett Tudásbázis körrel másik nyelven, érdemes létrehozni külön kérdések és válaszok készítő szolgáltatások alapján.

|Támogatott nyelvek|
|-----|
|arab|
|örmény|,
bengáli|
|baszk|
|bolgár|
|katalán|
|Chinese_Simplified|
|Chinese_Traditional|
|horvát|
|cseh|
|dán|
|holland|
|Angol|
|észt|
|finn|
|francia|
|galíciai|
|német|
|görög|
|gudzsaráti|
|héber|
|hindi|
|magyar|
|izlandi|
|indonéz|
|ír|
|olasz|
|japán|
|kannada|
|koreai|
|lett|
|litván|
|malajálam|
|maláj|
|norvég|
|lengyel|
|portugál|
|pandzsábi|
|román|
|orosz|
|Serbian_Cyrillic|
|Serbian_Latin|
|szlovák|
|szlovén|
|spanyol|
|svéd|
|tamil|
|telugu|
|thai|
|török|
|ukrán|
|urdu|
|vietnami|
