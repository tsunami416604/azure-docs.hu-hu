---
title: Hozzáférési kérelmek jóváhagyása vagy megtagadása – Azure AD-jogosultságkezelés
description: Megtudhatja, hogy miként hagyhatja jóvá vagy tagadhatja meg a Saját hozzáférés portált egy hozzáférési csomagra vonatkozó kérelmek jóváhagyásához vagy elutasításához az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261722"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Hozzáférési kérelmek jóváhagyása vagy megtagadása az Azure AD jogosultságkezelésében

Az Azure AD jogosultságkezelés, konfigurálhatja szabályzatok jóváhagyásra hozzáférési csomagok, és válasszon egy vagy több jóváhagyók. Ez a cikk azt ismerteti, hogy a kijelölt jóváhagyók hogyan hagyhatják jóvá vagy tagadhatják meg a hozzáférési csomagokra vonatkozó kérelmeket.

## <a name="open-request"></a>Kérelem megnyitása

A hozzáférési kérelmek jóváhagyásának vagy megtagadásának első lépése a jóváhagyásra váró hozzáférési kérelem megkeresése és megnyitása. A hozzáférési kérelem kétféleképpen nyitható meg.

**Előfeltételi szerepkör:** Jóváhagyó

1. Keressen egy e-mailt a Microsoft Azure-ból, amely egy kérelem jóváhagyására vagy elutasítására kéri. Itt van egy példa e-mail:

    ![Csomaglevelezési kérelem jóváhagyása](./media/entitlement-management-shared/approver-request-email.png)

1. A hozzáférési kérelem megnyitásához kattintson a **Jóváhagyás vagy megtagadása hivatkozásra.**

1. Jelentkezzen be a Saját hozzáférés portálra.

Ha nem rendelkezik az e-maillel, az alábbi lépések végrehajtásával megtalálhatja a jóváhagyásra váró hozzáférési kérelmeket.

1. Jelentkezzen be a Saját [https://myaccess.microsoft.com](https://myaccess.microsoft.com)hozzáférés portálra a helyen.  (Az Egyesült Államok kormánya számára a My `myaccess.microsoft.us`Access portál hivatkozásának tartománya a .)

1. A bal oldali menüben kattintson a **Jóváhagyások** parancsra a jóváhagyásra váró hozzáférési kérelmek listájának megtekintéséhez.

1. A **Függőben** lapon keresse meg a kérelmet.

## <a name="approve-or-deny-request"></a>Kérelem jóváhagyása vagy megtagadása

Miután megnyitotta a jóváhagyásra váró hozzáférési kérelmet, megtekintheti azolyan részleteket, amelyek segítenek a jóváhagyás vagy a döntés megtagadásában.

**Előfeltételi szerepkör:** Jóváhagyó

1. Kattintson a **Nézet** hivatkozásra az Access kérelemablak megnyitásához.

1. Kattintson a **Részletek** gombra a hozzáférési kérelem részleteinek megtekintéséhez.

    A részletek közé tartozik a felhasználó neve, szervezete, a hozzáférés kezdő és záró dátuma, ha meg van adva, üzleti indoklás, a kérelem elküldésének időpontja és a kérelem lejáratának időpontja.

1. Kattintson **a Jóváhagyás** vagy **a Megtagadás gombra.**

1. Ha szükséges, adjon meg egy okot.

    ![Saját hozzáférési portál – Hozzáférési kérelem](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. A döntés elküldéséhez kattintson a **Küldés** gombra.

    Ha egy házirend több jóváhagyóval van konfigurálva, csak egy jóváhagyónak kell döntenie a függőben lévő jóváhagyásról. Miután egy jóváhagyó benyújtotta a döntést a hozzáférési kérelemhez, a kérelem befejeződött, és a többi jóváhagyó számára már nem érhető el a kérelem jóváhagyásához vagy elutasításához. A többi jóváhagyó a kérelemdöntés és a döntéshozó a Saját hozzáférés portálon láthatja. Jelenleg csak egylépcsős jóváhagyás támogatott.

    Ha a konfigurált jóváhagyók egyike sem tudja jóváhagyni vagy megtagadni a hozzáférési kérelmet, a kérelem a beállított kérelem időtartama után lejár. A felhasználó értesítést kap arról, hogy a hozzáférési kérelem lejárt, és hogy újra kell küldenie a hozzáférési kérelmet.

## <a name="next-steps"></a>További lépések

- [Hozzáférés kérése hozzáférési csomaghoz](entitlement-management-request-access.md)
- [Folyamat- és e-mail-értesítések kérése](entitlement-management-process.md)
