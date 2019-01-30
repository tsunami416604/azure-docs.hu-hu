---
title: Mi az az Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő anyagok nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 4e0b5070a9ee5b8c8554505dcf81c8226a9fddc0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227216"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

Az Azure Content Moderator API egy olyan Cognitive Services-szolgáltatás, amely esetlegesen sértő, kockázatos vagy egyéb nem kívánt anyagokat keres a szövegekben, képeken és videókban. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet. A különböző tartalomjelölők jelentéséről további információt a [Content Moderator API-kat](#content-moderator-apis) ismertető szakaszban talál.

## <a name="where-it-is-used"></a>Hol használjuk?

A szoftverfejlesztőknek vagy a csapatoknak például a következő néhány alkalmazási helyzetben érdemes használniuk a Content Moderatort:

- Online piacterek, amelyek moderálják a termékkatalógusokat és más, a felhasználók által létrehozott tartalmakat
- Játékcégek, amelyek moderálják a felhasználók által létrehozott játékösszetevőket és a csevegőszobákat
- Közösségi üzenetkezelési platformok, amelyek moderálják a felhasználók által hozzáadott képeket, szövegeket és videókat
- Médianagyvállalatok, amelyek központosított moderálást alkalmaznak a tartalmakon
- Általános és középiskolai oktatási megoldások szolgáltatói, amelyek a diákok és oktatók számára szűrik ki a helytelen tartalmakat

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Ezek között az emberi felülvizsgálati eszköz is szerepel, amely lehetővé teszi, hogy emberi felülvizsgálók segítsék a szolgáltatást és végezzenek finomhangolást a moderálási funkción.

![a Content Moderator blokkdiagramja, amelyen a moderálási API-k, az ellenőrző API-k és az emberi felülvizsgálati eszköz látható](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Content Moderator API-k

A Content Moderator szolgáltatás az alábbi alkalmazási helyzetekhez tartalmaz API-kat.

| Műveletek | Leírás |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Sértő tartalmat, szexuálisan explicit vagy szexuális utalásokat tartalmazó, profanitást és személyazonosításra alkalmas adatokat (IIP) tartalmazó szövegeket keres.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A beépített kifejezések mellett egyéni kifejezéslisták alapján is keres a szövegekben. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|
|[**Felülvizsgálatok**](try-review-api-job.md)| A [Feladatok](try-review-api-job.md), [Felülvizsgálatok](try-review-api-review.md) és [Munkafolyamat](try-review-api-workflow.md) műveletek segítségével hozhatók létre és automatizálhatók az emberi beavatkozást igénylő munkafolyamatok az emberi felülvizsgálati eszközzel. A munkafolyamat API még nem érhető el a .NET SDK-n keresztül.|

### <a name="human-review-tool"></a>Emberi felülvizsgálati eszköz

A Content Moderator szolgáltatás webalapú [emberi felülvizsgálati eszközt](Review-Tool-User-Guide/human-in-the-loop.md) is tartalmaz. 

![A Content Moderator emberi felülvizsgálati eszköz kezdőlapja](images/homepage.PNG)

A felülvizsgálati API-k segítségével állíthatja be a szövegek, képek és videótartalmak csapat általi felülvizsgálatát a megadott szűrők alapján. Ezt követően az emberi moderátorok hozhatják meg a végső moderátori döntéseket. Az emberi bevitel nem tanítja be a szolgáltatást, de a szolgáltatás és az emberi felülvizsgáló csapatok együttes munkája lehetővé teszi, hogy a fejlesztők megfelelő egyensúlyt érjenek el a hatékonyság és a pontosság között.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság
Az összes a Cognitive Services, a Content Moderator szolgáltatással a fejlesztők célszerű tisztában lennie a vásárlói adatokat a Microsoft házirendeket. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Ismerkedés a Content Moderator szolgáltatás használatával a következő témakör utasításait követve [próbálja ki a Content Moderator, a weben](quick-start.md).
