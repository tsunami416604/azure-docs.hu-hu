---
title: JavaScript és a lap szerződés verziók – Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogy engedélyezze a JavaScript, és a lap szerződés verziók használata az Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511976"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript és az oldal az Azure Active Directory B2C szerződés-verziók

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Az Azure AD B2C biztosít a felhasználói felületi elemekre, a felhasználói folyamatok és az egyéni szabályzatok a HTML, CSS és JavaScript tartalmazó csomagolt tartalom. JavaScript engedélyezése az alkalmazások számára, hozzá kell adnia egy elem a [egyéni házirendet](active-directory-b2c-overview-custom.md) vagy engedélyezni kell azt a portálon a felhasználói folyamatok lap szerződés kiválasztása és használata [b2clogin.com](b2clogin.md) a kérelmek a.

Ha szeretne engedélyezni [JavaScript](javascript-samples.md) ügyféloldali kódot, érdemes, hogy a JavaScript épül az elemek nem módosíthatók. Azt jelzi, ellenkező esetben módosításokat okozhat-e a felhasználó lapok nem várt viselkedést. Ezek a problémák megelőzése érdekében egy oldal szerződés használatának kényszerítése, és adjon meg egy oldal szerződés verziót. Ezáltal biztosítható, hogy a tartalom definíciók, amelyek az, hogy a JavaScript nem módosíthatók. Akkor is, ha nem kívánja engedélyezni a JavaScript, a lapok egy oldal szerződés verziót is megadhat.

## <a name="user-flows"></a>Felhasználói folyamatok

A felhasználói folyamat tulajdonságai között engedélyezheti a JavaScript, amely szintén egy oldal szerződés. Ezután állíthatja be az oldal szerződés verzió a következő szakaszban leírtak szerint.

![JavaScript-beállítás engedélyezése](media/user-flow-javascript-overview/javascript-settings.png)

Attól, hogy engedélyezze a JavaScript használatát a felhasználói folyamat tulajdonságainak, egy lap szerződés verziót is megadhat a felhasználói folyamat lap. Nyissa meg a felhasználói folyamatot, és válassza ki **lapelrendezés**. A **Elrendezésnévvel**, jelöljön ki egy felhasználói folyamat lapot, és válassza ki a **lap szerződés verzió**.

![JavaScript-beállítás engedélyezése](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Egyéni szabályzatok

Egyéni szabályzatok JavaScript engedélyezéséhez adja hozzá a **ScriptExecution** elem a **RelyingParty** elem az egyéni házirend-fájlban. További információkért lásd: [JavaScript-minták az Azure Active Directory B2C használata az](javascript-samples.md).

Függetlenül attól, engedélyezze a JavaScript használatát a egyéni szabályzatait, a lapok egy oldal szerződés verziót is megadhat. Egy lap szerződés megadásával kapcsolatos további információkért lásd: [jelöljön ki egy oldal szerződést az Azure Active Directory B2C-vel egyéni szabályzatok használatával](page-contract.md).

## <a name="next-steps"></a>További lépések

Tekintse meg a [JavaScript-minták az Azure Active Directory B2C használata az](javascript-samples.md).
