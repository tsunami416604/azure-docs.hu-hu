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
ms.openlocfilehash: 14cf500cff9f1f7f8b1573862c3060326e6518fa
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688860"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

Az Azure Content Moderator API egy olyan Cognitive Services-szolgáltatás, amely esetlegesen sértő, kockázatos vagy egyéb nem kívánt anyagokat keres a szövegekben, képeken és videókban. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet. Tekintse meg a [moderálási API-k](#moderation-apis) szakaszban tudhat meg többet a különböző tartalmak jelzők jelzi.

## <a name="where-it-is-used"></a>Hol használjuk?

A szoftverfejlesztőknek vagy a csapatoknak például a következő néhány alkalmazási helyzetben érdemes használniuk a Content Moderatort:

- Termékkatalógus és más felhasználók által létrehozott tartalom mérsékelt online piacterein.
- Üzletmenetre gyakorolt közepes játék összetevők felhasználó által létrehozott és csevegőszobák játék olvashatók.
- Közösségi platformokon, amely mérsékelt képek, szöveg és a felhasználók által hozzáadott videókat.
- Vállalati media vállalatok, amelyek a tartalmak központi moderálás alkalmazzák.
- K-12 oktatási szolgáltatást nyújtó vállalkozásoknak szűri ki a tartalmat a diákok és oktatók számára nem megfelelő.

> [!NOTE]
> A Content Moderator szabálytalan gyermek kiaknázása képek nem használható. Azonban a minősített szervezetek használhatják a [PhotoDNA Felhőszolgáltatás](https://www.microsoft.com/photodna "Microsoft PhotoDNA Felhőszolgáltatás") képernyőre a tartalomtípushoz.

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