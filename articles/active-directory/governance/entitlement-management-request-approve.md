---
title: Hagyja jóvá vagy utasítsa a az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory hozzáférési kérelmek
description: Ismerje meg, hogyan használhatja a saját hozzáférési portál jóváhagyja vagy elutasítja a kérelmeket az access-csomag az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541525"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Hagyja jóvá vagy utasítsa a hozzáférési kérelmeket az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tagjogosultság-kezelés az Azure AD-jóváhagyás megkövetelése a hozzáférési csomagok házirendeket konfigurálhat, és válasszon egy vagy több jóváhagyó. Ez a cikk azt ismerteti, hogyan kijelölt jóváhagyókat jóváhagyhatják vagy megtagadhatják a hozzáférési csomagok vonatkozó kéréseket.

## <a name="open-request"></a>Nyissa meg a kérelem

Az első lépés jóváhagyja vagy elutasítja a hozzáférési kérelmeket, hogy keresse meg és nyissa meg a hozzáférési kérelem jóváhagyásra vár. Nyissa meg a hozzáférési kérelem két módon lehet.

**Előfeltétel szerepkör:** Jóváhagyó

1. Keresse meg e-mailt, amely rákérdez, hogy elfogadja vagy elutasítja a kérelmet a Microsoft Azure-ból. Íme egy példa e-mailt:

    ![Csomag e-mailek elérését kérelem jóváhagyása](./media/entitlement-management-shared/email-approve-request.png)

1. Kattintson a **jóváhagyás vagy megtagadja a kérelmet** nyissa meg a hozzáférési kérelem mutató hivatkozást.

1. Jelentkezzen be a saját hozzáférés-portálon.

Ha nem rendelkezik az e-mailt, megtalálhatja a hozzáférési kérelmeket jóváhagyásra váró az alábbi lépéseket.

1. Jelentkezzen be a saját Access Portalra [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. A bal oldali menüben kattintson a **jóváhagyások** hozzáférési kérelmeket jóváhagyásra váró listájának megtekintéséhez.

1. Az a **függőben lévő** lapra, keresse meg a kérelmet.

## <a name="approve-or-deny-request"></a>Hagyja jóvá vagy utasítsa a kérelem

Miután megnyitotta a hozzáférési kérelem jóváhagyásra, láthatja, győződjön meg arról, egy jóváhagyás vagy megtagadhatja a döntés segítő részletek.

**Előfeltétel szerepkör:** Jóváhagyó

1. Kattintson a **nézet** hivatkozásra a hozzáférési kérés panel megnyitásához.

1. Kattintson a **részletek** a hozzáférési kérés részleteinek megtekintéséhez.

    A részletek közé tartozik a felhasználó nevét, a szervezet, eléréséhez a kezdő és záró dátumát, ha meg van adva, üzleti indoklás, amikor a kérelem el lett küldve, és amikor a kérelem lejár.

1. Kattintson a **jóváhagyása** vagy **megtagadása**.

1. Ha szükséges, adja meg az okot.

    ![A hozzáférési portál – a hozzáférési kérelem](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kattintson a **küldés** döntéseiben elküldéséhez.

    Ha egy szabályzat van konfigurálva több jóváhagyóval, csak egy jóváhagyó kell a függőben lévő jóváhagyási kapcsolatos döntéseket. Miután a jóváhagyó azok a hozzáférési kérés döntési küldött, a kérelem befejeződött, és már nem érhető el a jóváhagyó jóváhagyja vagy elutasítja a kérelmet. A többi jóváhagyók láthatja a kérelem döntést és döntéshozóként pedig a saját hozzáférés-portálon. Jelenleg csak egyetlen lépésből álló jóváhagyás támogatott.

    A konfigurált jóváhagyók sem tudja jóváhagyja vagy elutasítja a hozzáférési kérést, ha a kérelem után a konfigurált kérés időtartama lejár. Lekérdezi a felhasználó értesítést kap, hogy a hozzáférési kérelem lejárt, és küldje el újra a hozzáférési kérelem szükséges.

## <a name="next-steps"></a>További lépések

- [Hozzáférés csomagjára hozzáférés kérése](entitlement-management-request-access.md)
- [Folyamat és az e-mailes értesítések kérése](entitlement-management-process.md)
