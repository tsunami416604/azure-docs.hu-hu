---
title: Támogatott - adatforrások Microsoft kognitív szolgáltatások |} Microsoft Docs
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
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348686"
---
# <a name="data-sources"></a>Adatforrások 
Kérdések és válaszok készítő automatikusan is kibonthat egy kérdés-válasz pár formátumokból közös félig strukturált tartalom például – gyakori kérdések és a termék dokumentáció. Tartalom is felveheti a Tudásbázisban strukturált fájlokból.

## <a name="plain-faq-pages"></a>Egyszerű – gyakori kérdések lap
Ez egy gyakori kérdéseket tartalmazó oldal, ahol a kérdésekre adott válaszokat közvetlenül követnie ugyanazon az oldalon lévő a leggyakrabban használt típus. 

![Egyszerű gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Szakasz hivatkozásokkal GYIK lapok 
Az ilyen típusú gyakori kérdéseket tartalmazó oldal kérdések együtt összesítik és válaszok, amelyek a különböző szakaszokat ugyanazon az oldalon van csatolva.

 ![A szakasz hivatkozás gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Különböző oldalain mutató hivatkozásokkal GYIK lapok 
Ez a gyakori kérdéseket tartalmazó oldal típus hasonló szakasz kapcsolódó gyakori kérdéseket tartalmazó oldal, azzal a különbséggel, hogy a hivatkozások átirányítja egy másik oldalon. Kérdések és válaszok készítő bejárja az összes csatolt lapok bontsa ki a megfelelő választ.

 ![Áruházra mutató mélyhivatkozás gyakran ismételt kérdések lap](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>A termék laborútmutatókkal

Kézi általában útmutató dokumentumok, amely a termék kerül. A felhasználó számára állítsa be, használja, karbantartásában és hibáinak elhárítása a termék segít. Kérdések és válaszok készítő kézi dolgozza fel, a fejlécére kattintva rendezhető, és alszámok, mint a kérdések és válaszok a későbbi tartalom bontja ki. Példa [Itt](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Kibontási leghatékonyabb tartalmát és/vagy egy index lap és egy tiszta struktúra hierarchikus fejlécekkel rendelkező laborútmutatókkal.


## <a name="structured-data-format-through-file-upload"></a>Fájl feltöltése a strukturált adatok formátuma

Például .tsv, formázott oszlopokkal .xlsx strukturált fájlok is feltölthetők a kérdések és válaszok készítő Tudásbázis létrehozása során. Fájlok is feltöltheti a **beállítások** a Tudásbázis lapja

| Kérdés  | Válasz  | Metaadatok                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
A forrásfájl további oszlopokat figyelmen kívül lesznek hagyva.

## <a name="structured-data-format-through-import"></a>Importálás keresztül strukturált adatok formátuma
A meglévő Tudásbázis tartalmának importálása a Tudásbázis váltja fel. Importálás az adatforrásra vonatkozó információ tartalmazó strukturált .tsv fájl szükséges. Ezen információk alapján a kérdések és válaszok készítő a kérdés-válasz pár csoportnak, és azokat egy adott adatforrást attribútumot.

| Kérdés  | Válasz  | Forrás| Metaadatok                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Szerkesztői besorolás|    `Key:Value`       |

## <a name="editorial"></a>Szerkesztői besorolás
Ha nem rendelkezik már meglévő tartalom feltöltése a Tudásbázis, azt is megteheti őket besorolást kérdések és válaszok készítő ismeretek alap. Ismerje meg, hogyan lehet frissíteni a Tudásbázis [Itt](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A kérdések és válaszok készítő szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még 

[Kérdések és válaszok készítő áttekintése](../Overview/overview.md)