---
title: Nyelvi támogatás – QnA Maker
titleSuffix: Azure Cognitive Services
description: Tudásbázis nyelvének hatással van a QnA Maker válaszokat automatikusan kinyerni kérdések és válaszok forrásokból, valamint a QnA Maker felhasználói lekérdezések maximális kézbesítésszáma biztosít az eredmények relevancia alapján végzett. Kulturális környezet, a Tudásbázis támogatja a QnA Maker természetes nyelvek listáját. Ne keverje a nyelveket ugyanazon Tudásbázis.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 820a9ae0df91fba8cf00764428867bec6196841a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335861"
---
# <a name="language-support-for-qna-maker"></a>A QnA Maker nyelvi támogatás

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

## <a name="primary-language-detection"></a>Elsődleges nyelv észlelése

A QnA Maker erőforrás- és az első dokumentum vagy URL-cím hozzáadódik az első Tudásbázis létrehozásakor az erőforráson, az összes tudásbázisok észleléséhez használt elsődleges nyelv van beállítva. A nyelvet nem lehet módosítani. 

Ha a felhasználó több nyelv támogatása tervek, kell rendelkezniük nekik a QnA Maker új erőforrást az egyes nyelvekhez. Ismerje meg, hogyan [nyelven alapuló QnA Maker Tudásbázis létrehozása](/how-to/language-knowledge-base.md).  

Ellenőrizze az alábbi lépéseket követve elsődleges nyelve:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).  
1. Keresse meg és válassza ki az Azure Search-erőforrást a QnA Maker erőforrás részeként létrehozott. Az Azure Search-erőforrás neve a neve megegyezik a QnA Maker erőforrás kezdődik, és a típus lesz **keresési szolgáltatás**. 
1. Az a **áttekintése** lapon a keresési erőforrás, jelölje be **indexek**. 
1. Válassza ki a **testkb** index.
1. Válassza ki a **mezők** fülre. 
1. Nézet a **Analyzer** oszlopában a **kérdések** és **válasz** mezőket. 


## <a name="query-matching-and-relevance"></a>A lekérdezésnek megfelelő, és a relevancia alapján végzett
A QnA Maker függ [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) az eredmények, amelyek biztosítják az Azure Search szolgáltatásban. Speciális újra rangsorértékét funkciók érhetők el az En-* nyelveket, amelyek lehetővé teszik a jobb relevancia alapján.

Bár az Azure Search-funkciók által támogatott nyelvek egyenlő figyelembevétele, QnA Maker egy további rangsorolás, amely az Azure search eredmények felett van. Néhány speciális szemantikai és a word-alapú funkció, az en - használjuk a rangsorolás modell, *, amely még nem érhető el a többi nyelven. Nem vállalunk ezeket a funkciókat érhető el, azok a belső működéséhez a QnA Maker rangsorolás része. 

A QnA Maker [automatikusan észleli a Tudásbázis nyelvének](#primary-language-detection) létrehozása során, és ennek megfelelően beállítja az elemző. Az alábbi nyelveken tudásbázisok hozhat létre. 

|Támogatott nyelvek|
|-----|
|arab|
|örmény|
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
