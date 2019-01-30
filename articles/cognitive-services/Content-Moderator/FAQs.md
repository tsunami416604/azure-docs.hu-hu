---
title: Gyakori kérdések – a Content Moderator
titlesuffix: Azure Cognitive Services
description: A Content Moderator kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: faq
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 1c55ef6fdfefbd50442da63637ba98bc43865d27
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206102"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

#### <a name="what-does-my-content-moderator-subscription-include"></a>Saját előfizetés tartalmazza a Content Moderator funkciója?
A Content Moderator előfizetés biztosít hozzáférést a felülvizsgálati eszköz és az API-kat. Eldöntheti, hogy szeretné-e legalább egy másik, vagy mindkettőt, üzleti igényeitől függően használhatja.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Milyen korlátozások/megkötések vonatkoznak az API által moderálható tartalmakra?
Az API használatához, lemezképek kell rendelkeznie legalább 128 pixel, és a egy 4 MB-os maximális fájlméret. Szöveg, legfeljebb 1024 karakter hosszú lehet. Nincs megszabva, hogy a videó időtartama.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Mi történik, ha a szöveges vagy a kép API-nak elküldött szöveg vagy kép meghaladja a méretbeli korlátokat?
A szöveges API hibakódot, amelyben arról tájékoztatja, hogy a szöveg hosszabb a megengedettnél. A képfájl API is hibakódot, amely tájékoztatja, hogy a kép nem felel meg a méretbeli korlátozásoknak.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Akkor tartsa a képek, szöveg vagy videók moderálás elküldött?
A tartalmat a saját és az beleegyezése nélkül a Microsoft nem tarthatók. A Microsoft iparágvezető biztonsági intézkedések használ a tartalom védelme érdekében.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Használható a Content Moderator képernyőre szabálytalan gyermek kiaknázása lemezképek?
Nem. Azonban a minősített szervezetek használhatják a [PhotoDNA Felhőszolgáltatás](https://www.microsoft.com/photodna "Microsoft PhotoDNA Felhőszolgáltatás") képernyőre a tartalomtípushoz.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Legfeljebb hány tekintse át csapatok csatlakozhat el egy felhasználót? A csapatok között válthat a felhasználó?
Egy felhasználó csatlakozhat egy csapat egyszerre.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Milyen típusú csapat egyik tagja a felülvizsgálati eszköz által támogatott szerepkörök? Mi a különbség közöttük?
A Studio jelenleg lehetővé teszi a rendszergazda és a felülvizsgáló szerepkörök hozzárendelése. A rendszergazdák mások meghívása és a konfigurációs beállítások hozzáférése, amíg a felülvizsgálók csak tekintse át a moderálás eredményeket és egy címkét vagy untag tartalmat.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Mit jelent a címkét? A felülvizsgálati eszköz támogatja az egyéni címkéket?
Egy címke, adott vagy kétbetűs rövid kód, amely azt jelzi, hogy a moderálás jelzőt, például az "a" a felnőtt, "r" pikáns és így tovább. Rendszergazdák meghatározhatnak új címkéket üzleti igény szerint.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Hány tagja is kell a csapatomnak?
A rendszergazdával együtt legfeljebb öt tagja lehet a csapatnak.
