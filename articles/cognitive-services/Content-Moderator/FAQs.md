---
title: Gyakori kérdések az Azure Content moderátor |} Microsoft Docs
description: Tartalom moderátor gyakran feltett kérdésekre adott válaszok.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 7bf6c67bd0a83d9455d14253f4f4b2becafd29ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347014"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

#### <a name="what-does-my-content-moderator-subscription-include"></a>Az előfizetés tartalmazza a tartalom moderátor funkciója?
A tartalom moderátor előfizetése a felülvizsgálati eszköz és az API-k elérésére. Eldöntheti, hogy szeretné-e egy vagy egyéb, vagy mindkettőt, attól függően, hogy az üzleti igényeknek megfelelően.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Milyen korlátozások/megkötések vonatkoznak az API által moderálható tartalmakra?
Az API-val, képek kell 128 képpontos minimális és a 4 MB maximális fájlméretet. A szöveg, legfeljebb 1024 karakter hosszú lehet. A videó időtartamával korlátozva van.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Mi történik, ha a szöveges vagy a kép API-nak elküldött szöveg vagy kép meghaladja a méretbeli korlátokat?
A szöveg API hibakódot, amelyben arról tájékoztatja, hogy a szöveg hosszabb a megengedettnél. A kép API is hibakódot, amelyben arról tájékoztatja, hogy a kép nem felel meg a kötetméretek követelményeit.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Tegye megtartja a képek, text vagy tartalmazó moderálás a videók?
A tartalmat a saját, és előfordulhat, hogy nem őrzi meg a Microsoft Ön hozzájárulása nélkül. Microsoft iparágvezető biztonsági intézkedéseket használ a tartalom védelme érdekében.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Használhatok tartalom moderátor képernyőre szabálytalan gyermek kihasználásának lemezképek?
Nem. Azonban a minősített szervezetek használhatnak a [PhotoDNA felhőalapú szolgáltatás](https://www.microsoft.com/photodna "Microsoft PhotoDNA Felhőszolgáltatás") képernyő, az adott tartalmat.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Legfeljebb hány tekintse át csapatok lehetővé teszi a felhasználó csatlakoztatását? A felhasználói csoportok között válthat?
A felhasználók kapcsolódhatnak egy csoportot egyszerre.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Milyen szerepkörök a felülvizsgálati eszköz által támogatott csapattag számára? Mi a különbség közöttük?
A Studio jelenleg lehetővé teszi a rendszergazda és a felülvizsgáló szerepkörök hozzárendelése. A rendszergazdák meghívott más felhasználóknak és felülvizsgálók csak moderálás eredmények és címke vagy untag tartalom hozzáférése a konfigurációs beállításokat.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Mi az, hogy egy címkét? A felülvizsgálati eszköz támogatja egyéni címkék?
Egy címkét, adott vagy kétbetűs rövid kód, amely azt jelzi, a moderálás jelzőt, például az "a" felnőtt, "r" ellopható és így tovább. Rendszergazdák meghatározhatnak új címkék az üzleti igény szerint.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Hány csoport tagjai is rendelkezik a felülvizsgálati csoport?
A rendszergazdával együtt legfeljebb öt tagja lehet a csapatnak.
