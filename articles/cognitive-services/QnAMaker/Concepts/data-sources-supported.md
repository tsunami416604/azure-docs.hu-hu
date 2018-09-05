---
title: Támogatott – adatforrások a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Támogatott adatforrások
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 698f96b15a9387cd30d26e684ed03ff4cc3346a7
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697900"
---
# <a name="data-sources"></a>Adatforrások 
A QnA Maker – gyakori kérdések és kézikönyvek például közös félig strukturált tartalom formátumok automatikusan kinyerheti az kérdés-válasz párt. Tartalmakat is hozzáadhat a Tudásbázis strukturált fájlokból.

## <a name="plain-faq-pages"></a>Egyszerű gyakori kérdéseket tartalmazó oldalak
Ez a gyakori kérdéseket tartalmazó oldal, ahol a válaszok azonnal kövesse ugyanazon az oldalon szereplő kérdések leggyakoribb típusát. 

![Egyszerű gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>A szakasz mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak 
A gyakori kérdéseket tartalmazó oldal az ilyen típusú kérdések együtt vannak összesítve, és választ, amelyek különböző szakaszaiban ugyanazon az oldalon van csatolva.

 ![A szakasz hivatkozás gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>A különböző oldalakhoz mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak 
Ez a gyakori kérdéseket tartalmazó oldal típus egy szakasz kapcsolódó gyakori kérdéseket tartalmazó oldal hasonló azzal a különbséggel, hogy a hivatkozások egy másik oldalra irányítsa át. A QnA Maker feltérképezi a csatolt lapok bontsa ki a megfelelő választ.

 ![Áruházra mutató mélyhivatkozás – gyakori kérdések lapján](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Kézikönyvek

Manuális művelet rendszerint útmutatást anyagot, amely a termékek dokumentációiból tájékozódhat. Állítsa be, használja, karbantartása és a termék hibaelhárítása a felhasználó számára nyújt segítséget. A QnA Maker manuális dolgozza fel, ha a fejlécek és alszámok, kérdések és válaszok az azt követő tartalom bontja ki. Látható egy példa [Itt](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Kibontási optimális, amelyek a tábla tartalmát és/vagy a index lapot és a egy egyértelmű struktúra hierarchikus fejlécekkel kézikönyvek.


## <a name="structured-data-format-through-file-upload"></a>Strukturált adatok formátumát a fájl feltöltése

Strukturált fájlok, például a .tsv, amely formázott oszlopokat .xlsx-fájlba is feltölthetők a QnA Maker Tudásbázis létrehozása során. Emellett feltölthet fájlokat a **beállítások** Tudásbázis lapján

| Kérdés  | Válasz  | Metaadatok                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | "Key1:Value1|Key2:Value2 " |
| Question2 | Answer2 |      `Key:Value`           |
A forrásfájl további oszlopokat a rendszer figyelmen kívül hagyja.

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása
Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Importálás a strukturált .tsv adatforrás adatait tartalmazó fájl szükséges. Ez az információ segít a QnA Maker, kérdés-válasz párt csoportot, és azokat egy adott adatforrást attribútumot.

| Kérdés  | Válasz  | Forrás| Metaadatok                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|"Key1:Value1|Key2:Value2 " |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="editorial"></a>Szerkesztői
Ha nem rendelkezik már meglévő tartalom feltöltéséhez a Tudásbázis, is felveheti őket besorolást a QnA Maker Knowledge base. Ismerje meg, hogyan frissíthető a Tudásbázis [Itt](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker – áttekintés](../Overview/overview.md)
