---
title: Mi az az Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő anyagok nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 7e9c12c7da701fb627c51373e57f870d3fc77ac5
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551313"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

Az Azure Content Moderator API egy olyan Cognitive Services-szolgáltatás, amely esetlegesen sértő, kockázatos vagy egyéb nem kívánt anyagokat keres a szövegekben, képeken és videókban. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet. Tekintse meg a [moderálási API-k](#moderation-apis) szakaszban tudhat meg többet a különböző tartalmak jelzők jelzi.

## <a name="where-it-is-used"></a>Hol használjuk?

A szoftverfejlesztőknek vagy a csapatoknak például a következő néhány alkalmazási helyzetben érdemes használniuk a Content Moderatort:

- Online piacterek, amelyek moderálják a termékkatalógusokat és más, a felhasználók által létrehozott tartalmakat
- Játékcégek, amelyek moderálják a felhasználók által létrehozott játékösszetevőket és a csevegőszobákat
- Közösségi üzenetkezelési platformok, amelyek moderálják a felhasználók által hozzáadott képeket, szövegeket és videókat
- Médianagyvállalatok, amelyek központosított moderálást alkalmaznak a tartalmakon
- Általános és középiskolai oktatási megoldások szolgáltatói, amelyek a diákok és oktatók számára szűrik ki a helytelen tartalmakat

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Ezek között az emberi felülvizsgálati eszköz is szerepel, amely lehetővé teszi, hogy emberi felülvizsgálók segítsék a szolgáltatást és végezzenek finomhangolást a moderálási funkción.

## <a name="moderation-apis"></a>Moderálási API-k

A Content Moderator szolgáltatás moderálási API-alkalmazásokat, ellenőrizze az esetlegesen nem megfelelő vagy nem kívánatos van, amely a tartalom magában foglalja.

![a Content Moderator moderálási API-k blokkolása diagramja](images/content-moderator-mod-api.png)

A következő táblázat ismerteti a különböző típusú moderálási API-kat.

| API-csoport | Leírás |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Szöveg sértő vagy durva tartalom, nyíltan explicit vagy kétértelmű tartalmat, vulgáris, és a személyes adatok ellenőrzése.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A beépített kifejezések mellett egyéni kifejezéslisták alapján is keres a szövegekben. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|

## <a name="review-apis"></a>API-k áttekintése

A felülvizsgálati API-k lehetővé teszik a moderálási folyamat integrálása az emberi felülvizsgálók. Használja a [feladatok](review-api.md#jobs), [felülvizsgálatok](review-api.md#reviews), és [munkafolyamat](review-api.md#workflows) hozhat létre és emberi hurok munkafolyamatok automatizálása a műveletek a [vizsgálóeszköz](#the-review-tool) () az alábbi).

> [!NOTE]
> A munkafolyamat API még nem áll rendelkezésre a .NET SDK-ban, de használható a REST-végponthoz.

![blokk-diagram a Content Moderator API-k áttekintése](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A felülvizsgálati eszköz

A Content Moderator szolgáltatást is tartalmaz a webalapú [vizsgálóeszköz](Review-Tool-User-Guide/human-in-the-loop.md), tartalmat tároló, amely áttekinti az emberi moderátorok feldolgozni. Az emberi bevitel nem tanítja be a szolgáltatást, de a szolgáltatás és az emberi felülvizsgáló csapatok együttes munkája lehetővé teszi, hogy a fejlesztők megfelelő egyensúlyt érjenek el a hatékonyság és a pontosság között. A felülvizsgálati eszköz is biztosít egy felhasználóbarát előtér-Content Moderator erőforrások különböző.

![A Content Moderator emberi felülvizsgálati eszköz kezdőlapja](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Az összes a Cognitive Services, a Content Moderator szolgáltatással a fejlesztők célszerű tisztában lennie a vásárlói adatokat a Microsoft házirendeket. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Ismerkedés a Content Moderator szolgáltatás használatával a következő témakör utasításait követve [próbálja ki a Content Moderator, a weben](quick-start.md).