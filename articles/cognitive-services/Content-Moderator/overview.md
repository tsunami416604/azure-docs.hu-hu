---
title: Mi az az Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő anyagok nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404296"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Content Moderator egy kognitív szolgáltatás, amely ellenőrzi a szöveges, kép- és videotartalmakat a potenciálisan sértő, kockázatos vagy más módon nemkívánatos anyagok esetében. Ha ez az anyag megtalálható, a szolgáltatás megfelelő címkéket (zászlókat) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet. A [Moderálás API-k](#moderation-apis) című részben további információ arról, hogy mit jeleznek a különböző tartalomjelzők.

## <a name="where-its-used"></a>Hol használják

A szoftverfejlesztőknek vagy a csapatoknak például a következő néhány alkalmazási helyzetben érdemes használniuk a Content Moderatort:

- Online piacterek, amelyek moderálják a termékkatalógusokat és más, a felhasználók által létrehozott tartalmakat.
- Szerencsejáték cégek, amelyek mérsékelt felhasználó által generált játék leletek és chat szobák.
- Közösségi üzenetküldő platformok, amelyek moderálják a felhasználók által hozzáadott képeket, szövegeket és videókat.
- Vállalati médiavállalatok, amelyek központosított moderálást valósítanak meg a tartalmukhoz.
- A K-12 oktatási megoldásszolgáltatók kiszűrik a diákok és oktatók számára nem megfelelő tartalmakat.

> [!NOTE]
> A Tartalommoderátor nem használható illegális gyermekkizsákmányolási képek észlelésére. A minősített szervezetek azonban a [PhotoDNA Cloud Service segítségével](https://www.microsoft.com/photodna "Microsoft PhotoDNA felhőszolgáltatás") szűrhetik az ilyen típusú tartalmakat.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Ez is beleértve a Áttekintés szerszám, melyik megenged emberi látogató -hoz segítség a szolgáltatás és tökéletesít vagy finom- finomít -a mértékletesség függvény.

## <a name="moderation-apis"></a>Moderálási API-k

A Tartalommoderátor szolgáltatás moderálási API-kat tartalmaz, amelyek ellenőrzik a potenciálisan nem megfelelő vagy kifogásolható anyagok tartalmát.

![blokkdiagram a tartalommoderátor moderálási API-ihoz](images/content-moderator-mod-api.png)

Az alábbi táblázat a moderálási API-k különböző típusait ismerteti.

| API-csoport | Leírás |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Sértő tartalmat, nyíltan szexuális vagy szuggesztív tartalmat, káromkodást és személyes adatokat keres a szövegben.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A szöveget a beépített kifejezésekkel együtt a kifejezések egyéni listájához vizsgálja. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|

## <a name="review-apis"></a>API-k áttekintése

Az API-k áttekintése lehetővé teszi a moderálási folyamat integrálását az emberi ellenőrzőkkel. A [Feladatok,](review-api.md#jobs) [Vélemények](review-api.md#reviews)és [Munkafolyamat](review-api.md#workflows) műveletekkel a [felülvizsgálati eszközzel](#review-tool) (lent) ember-in-the-loop munkafolyamatokat hozhat létre és automatizálhat.

> [!NOTE]
> A workflow API még nem érhető el a .NET SDK, de használható a REST végpont.

![blokkdiagram a tartalommoderátor ok-ellenőrzési API-ihoz](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Felülvizsgálati eszköz

A Tartalommoderátor szolgáltatás magában foglalja a webalapú [felülvizsgálati eszközt](Review-Tool-User-Guide/human-in-the-loop.md)is, amely az emberi moderátorok tartalom-értékelését tartalmazza. Az emberi input nem tanítja be a szolgáltatást, de a szolgáltatás és az emberi felülvizsgálati csapatok együttes munkája lehetővé teszi a fejlesztők számára, hogy megfelelő egyensúlyt találjanak a hatékonyság és a pontosság között. A Felülvizsgálati eszköz felhasználóbarát előlapot is biztosít számos tartalommoderátor-erőforráshoz.

![Tartalommoderátor-ellenőrző eszköz kezdőlapja](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

A Cognitive Services hez csakúgy, mint a Tartalommoderátor szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft ügyféladatokra vonatkozó irányelveit. További információért tekintse meg a [Cognitive Services lapot](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a Microsoft Adatvédelmi központban.

## <a name="next-steps"></a>További lépések

A Tartalommoderátor szolgáltatás használatának első lépései a [Tartalommoderátor kipróbálása az interneten](quick-start.md)című útmutató utasításait követve.
