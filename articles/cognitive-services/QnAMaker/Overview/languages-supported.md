---
title: Támogatott nyelvek – QnA Maker
titlesuffix: Azure Cognitive Services
description: Tudásbázis nyelvének hatással van a QnA Maker válaszokat automatikusan kinyerni kérdések és válaszok forrásokból, valamint a QnA Maker felhasználói lekérdezések maximális kézbesítésszáma biztosít az eredmények relevancia alapján végzett.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: ee04733064ec4e3d131b800fe1f18b27e5127fe8
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572940"
---
# <a name="supported-languages"></a>Támogatott nyelvek

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
