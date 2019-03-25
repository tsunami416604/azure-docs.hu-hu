---
title: JavaScript és a lap szerződés felhasználókövetési adatai az Azure Active Directory B2C-verziók |} A Microsoft Docs
description: Ismerje meg, hogyan JavaScript engedélyezése és használata a szerződés verziók lapon szabhatja testre az Azure Active Directory B2C felhasználói folyamat.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401554"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>JavaScript és a lap szerződés verziók használata a felhasználói folyamat

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Az Azure AD B2C biztosít a felhasználói felületi elemekre, a felhasználói folyamatokban a HTML, CSS és JavaScript tartalmazó csomagolt tartalom. Ha szeretne engedélyezni [JavaScript](javascript-samples.md) ügyféloldali kódot a felhasználói folyamatok, érdemes, hogy a JavaScript épül az elemek nem módosíthatók. Ellenkező esetben módosításokat okozhat váratlan viselkedést a felhasználó a flow oldalak. Ezek a problémák megelőzése érdekében a felhasználói folyamat lap szerződés használatának kényszerítése, és adjon meg egy oldal szerződés verziót. Ez biztosítja, hogy a tartalom definíciók, amelyek az, hogy a JavaScript nem módosíthatók. Akkor is, ha nem kívánja a felhasználói folyamat engedélyezze a JavaScript, a felhasználói folyamat lap egy oldal szerződés verziót is megadhat.

> [!NOTE]
> Ez a cikk ismerteti a JavaScript a felhasználói folyamatok, de is JavaScript használata, és válassza ki a lap szerződés verzió használatakor [egyéni szabályzatok](page-contract.md).

## <a name="enable-javascript"></a>JavaScript engedélyezése

A felhasználói folyamat tulajdonságai között engedélyezheti a JavaScript, amely szintén egy oldal szerződés. Ezután állíthatja be az oldal szerződés verzió a következő szakaszban leírtak szerint.

![JavaScript-beállítás engedélyezése](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Adjon meg egy oldal szerződés verziót

Attól, hogy engedélyezze a JavaScript használatát a felhasználói folyamat tulajdonságainak, egy lap szerződés verziót is megadhat a felhasználói folyamat lap. Nyissa meg a felhasználói folyamatot, és válassza ki **lapelrendezés**. A **Elrendezésnévvel**, jelöljön ki egy felhasználói folyamat lapot, és válassza ki a **lap szerződés verzió**.

![JavaScript-beállítás engedélyezése](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>További lépések
Tekintse meg a [JavaScript-minták az Azure Active Directory B2C használata az](javascript-samples.md).
