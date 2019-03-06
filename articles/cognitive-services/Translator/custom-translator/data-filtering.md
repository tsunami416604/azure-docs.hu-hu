---
title: Adatok szűrése – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Egy egyéni rendszer betanításához használandó dokumentumok elküldésekor a dokumentumok feldolgozása és -szűrési lépéseket képzési előkészítése sorozatát mennek keresztül.
author: jann-skotdal
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-jansko
ms.topic: conceptual
ms.openlocfilehash: 46af936cc479770fe5db8085106c9da310a9717e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452402"
---
# <a name="data-filtering"></a>Adatszűrés

Egy egyéni rendszer betanításához használandó dokumentumok elküldésekor a dokumentumok feldolgozása és -szűrési lépéseket képzési előkészítése sorozatát mennek keresztül. Ezeket a lépéseket az itt vannak írva. A szűrés ismerete segítenek megérteni a mondat száma megjelenik egyéni a fordítót, valamint a lépéseket, előfordulhat, hogy a dokumentumok előkészítése betanítás egyéni a fordítót a saját maga.

## <a name="sentence-alignment"></a>Mondatok igazítása
Ha a dokumentumot nem XLIFF, TMX vagy IGAZÍTÁS formátumban, egyéni a fordítót igazítja a mondatok egymással mondatról mondatra a forrás- és dokumentumok. Translator dokumentum igazítás végre – következik az elnevezési a dokumentumok keresése a megfelelő dokumentum más nyelv. A dokumentum egyéni Translator megpróbálja megkeresni a megfelelő mondat más nyelven. A dokumentum használ jelölés, például a beágyazott HTML-címkék zarovnání segítségre.  

Tekintse meg a számot a forrás a mondatok közötti nagy eltérés, és célozhat ügyféloldali dokumentumok, a dokumentumot nem lehet párhuzamos először, vagy valamilyen más okból nem sikerült igazítva. A dokumentum nagy különbség a régiópárok (> 10 %) mindkét oldalon a mondatok garantálja, hogy azok valóban párhuzamos második meg. Egyéni a fordítót a dokumentum mellett figyelmeztetést jelenít meg, ha a mondat száma gyanúsan.  


## <a name="deduplication"></a>Deduplikáció
Egyéni a fordítót eltávolítja a mondatokat, amelyek szerepelnek a teszt- és finomhangolási dokumentumok a betanítási adatok. Az Eltávolítás dinamikusan futtatja, nem a data-feldolgozási lépést a képzés belül történik. Egyéni a fordítót a mondat száma, hogy az ilyen eltávolítás előtt a projekt áttekintés jelentések.  

## <a name="length-filter"></a>Szűrő hossza
* Távolítsa el a mondatok csak több szóból sem.
* Távolítsa el az egyik oldalán 100-nál több szóból álló mondatokat.  Kínai, japán, koreai nem érvényes.
* Távolítsa el a mondatok kevesebb mint 3 karakternél. Kínai, japán, koreai nem érvényes.
* Távolítsa el a több mint 2000 karakternél mondatokat kínai, japán, koreai.
* Távolítsa el a mondatok a kevesebb mint 1 % alfanumerikus karaktereket tartalmazhat.
* Távolítsa el a több mint 50 szót tartalmazó dictionary bejegyzései.

## <a name="white-space"></a>Szóköz
* Cserélje le a bármely sorozata, beleértve a lapok és a egy szóköz karakterrel CR vagy LF feladatütemezések elválasztó karaktert.
* Távolítsa el a mondat szóköz

## <a name="sentence-end-punctuation"></a>Mondat záró írásjelek
Cserélje le a egyetlen példánya több mondat záró elválasztó karaktereket.  

## <a name="japanese-character-normalization"></a>Japán normalizálást karakter
Ismétlődő japán karakterek normalizálása: Átalakítás félszélességűvé tétele teljes szélesség karakter hosszúságú lehet.

## <a name="unescaped-xml-tags"></a>Unescaped XML-címkék
Szűrés átalakítások unescaped címkék escape-karakterrel megjelölt címkék:
* `&lt;` válik `&amp;lt;`
* `&gt;` válik `&amp;gt;`
* `&amp;` válik `&amp;amp;`

## <a name="invalid-characters"></a>Érvénytelen karakter
Egyéni a fordítót eltávolítja a mondatokat, amelyek U + FFFD Unicode-karaktert tartalmazhat. A következő karaktert: U + FFFD sikertelen kódolási konverzió jelzi.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](how-to-train-model.md) az egyéni a fordítót.
