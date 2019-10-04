---
title: Nyelvi támogatás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A Tudásbázis nyelve hatással van QnA Maker lehetőségére, hogy a forrásokból származó kérdéseket és válaszokat automatikusan kibontsa, valamint az eredmények QnA Maker a felhasználói lekérdezésekre adott válaszként. Kulturális környezet, a Tudásbázis támogatja a QnA Maker természetes nyelvek listáját. Ne keverje a nyelveket ugyanazon Tudásbázis.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066669"
---
# <a name="language-support-for-qna-maker"></a>A QnA Maker nyelvi támogatása

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

## <a name="primary-language-detection"></a>Elsődleges nyelvi észlelés

Az észleléshez használt elsődleges nyelv a QnA Maker erőforráshoz, és az adott erőforráson létrehozott összes tudásbázishoz van beállítva, amikor az első dokumentumot vagy URL-címet hozzáadja az első tudásbázishoz. A nyelv nem módosítható. 

Ha a felhasználó több nyelv támogatását tervezi, minden nyelvhez új QnA Maker erőforrást kell használnia. Megtudhatja, hogyan [hozhat létre nyelvi alapú QnA Maker tudásbázist](../how-to/language-knowledge-base.md).  

Ellenőrizze az elsődleges nyelvet a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
1. Keresse meg és válassza ki a QnA Maker erőforrás részeként létrehozott Azure Search erőforrást. A Azure Search erőforrás neve megegyezik a QnA Maker erőforrás nevével, és a típus **keresési szolgáltatással**fog rendelkezni. Ne feledje, hogy csak egy QnA Maker erőforrás lehet egy Azure Search erőforráshoz csatolva.
1. A keresési erőforrás **Áttekintés** lapján válassza az **indexek**lehetőséget. 
1. Válassza ki a **testkb** indexet.
1. Válassza a **mezők** lapot. 
1. Tekintse meg a **kérdések** és **válaszok** mezők **Analyzer** oszlopát. 


## <a name="query-matching-and-relevance"></a>A lekérdezésnek megfelelő, és a relevancia alapján végzett
A QnA Maker függ [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) az eredmények, amelyek biztosítják az Azure Search szolgáltatásban. Speciális újra rangsorértékét funkciók érhetők el az En-* nyelveket, amelyek lehetővé teszik a jobb relevancia alapján.

Bár az Azure Search-funkciók által támogatott nyelvek egyenlő figyelembevétele, QnA Maker egy további rangsorolás, amely az Azure search eredmények felett van. Ebben a Ranger-modellben olyan speciális szemantikai és Word-alapú funkciókat használunk az EN-*-ben, amelyek még nem érhetők el más nyelveken. Ezeket a funkciókat nem tesszük elérhetővé, mivel azok a QnA Maker Ranger belső működésének részét képezik. 

QnA Maker [automatikusan észleli a Tudásbázis nyelvét](#primary-language-detection) a létrehozás során, és ennek megfelelően állítja be az elemzőt. Az alábbi nyelveken tudásbázisok hozhat létre. 

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
