---
title: Nyelvi támogatás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker által támogatott természetes nyelvek listáját.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.openlocfilehash: 8c47c4a59f03328b1dc8d3df7771bac81864bb34
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566635"
---
# <a name="language-and-region-support-for-qna-maker"></a>A QnA Maker nyelvéhez és régiójához támogatása

Tudásbázis nyelvének hatással van a QnA Maker válaszokat automatikusan kinyerni kérdések, valamint választ ad a [források](../Concepts/data-sources-supported.md), valamint az a felhasználói lekérdezések maximális kézbesítésszáma biztosít a QnA Maker eredmények.

## <a name="auto-extraction"></a>Automatikus kivonása
A QnA Maker kérdés-válasz kinyerési támogatja a tetszőleges nyelven lapon, de a kivonás hatékonyságát sokkal magasabb, a következő nyelvekhez, QnA Maker kulcsszavak segítségével azonosítja a kérdések.

|Támogatott nyelvek| Területi beállítás|
|-----|----|
|Angol|en-*|
|francia|FR-*|
|olasz|IT-*|
|német|Németország – *|
|spanyol|es-*|

## <a name="query-matching-and-relevance"></a>A lekérdezésnek megfelelő, és a relevancia alapján végzett
A QnA Maker függ [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) az eredmények, amelyek biztosítják az Azure Search szolgáltatásban. Speciális újra rangsorértékét funkciók érhetők el az En-* nyelveket, amelyek lehetővé teszik a jobb relevancia alapján.

Bár az Azure Search-funkciók által támogatott nyelvek egyenlő figyelembevétele, QnA Maker egy további rangsorolás, amely az Azure search eredmények felett van. Rangsorolás ebben a modellben, hogy néhány speciális szemantikai használja, és word-alapú szolgáltatások az en-*, amely még nem érhető el a többi nyelven. Nem vállalunk ezek érhető el, azok a belső működéséhez a rangsorolás része. 

A QnA Maker automatikusan észleli a nyelvet, a Tudásbázis létrehozása során, és ennek megfelelően állítja be az elemző. Az alábbi nyelveken tudásbázisok hozhat létre. Olvasási [ez](../How-To/language-knowledge-base.md) hogyan kezeli a QnA Maker a nyelvek kapcsolatos további részletekért.


> [!Tip]
> Nyelvi elemzők, beállítása után, nem módosítható. Emellett a nyelvi elemző vonatkozik-e az összes tudásbázisok egy [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md). Tervezett tudásbázisok különböző nyelven, kell létrehoznia a különálló QnA Maker szolgáltatások.

|Támogatott nyelvek|
|-----|
|arab|
|örmény|; 
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
