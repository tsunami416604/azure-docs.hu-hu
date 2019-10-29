---
title: Hozzáférési kérelmek jóváhagyása vagy megtagadása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti vagy tagadhatja meg a hozzáférési csomagra irányuló kéréseket a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
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
ms.openlocfilehash: da3e1b6439b033742af62d548775cfb628e955b7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990711"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Hozzáférési kérelmek jóváhagyása vagy megtagadása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD-jogosultságok kezelésével házirendeket állíthat be a hozzáférési csomagok jóváhagyásának megköveteléséhez, és egy vagy több jóváhagyót is választhat. Ez a cikk azt ismerteti, hogyan hagyhatják jóvá vagy tagadhatják meg a kijelölt jóváhagyók a hozzáférési csomagok kérelmeit.

## <a name="open-request"></a>Kérelem megnyitása

A hozzáférési kérelmek jóváhagyásának vagy megtagadásának első lépéseként meg kell keresni és meg kell nyitni a hozzáférési kérelmet függőben lévő jóváhagyást. A hozzáférési kérelem két módon nyitható meg.

**Előfeltételként szükséges szerepkör:** Jóváhagyó

1. Keressen olyan Microsoft Azure e-mailt, amely arra kéri, hogy hagyja jóvá vagy tagadja meg a kérést. Példa erre az e-mail címre:

    ![Hozzáférési kérelem jóváhagyása a csomag e-mail-címéhez](./media/entitlement-management-shared/email-approve-request.png)

1. A hozzáférési kérelem megnyitásához kattintson a **jóváhagyás vagy a Megtagadás kérelem** hivatkozásra.

1. Jelentkezzen be a saját hozzáférési portálra.

Ha nem rendelkezik az e-mail-címmel, a következő lépésekkel megtalálhatja a jóváhagyásra váró hozzáférési kérelmeket.

1. Jelentkezzen be a saját hozzáférési portálra a [https://myaccess.microsoft.com](https://myaccess.microsoft.com)címen.

1. A bal oldali menüben kattintson a **jóváhagyások** elemre, hogy megtekintse a hozzáférési kérelmek függőben lévő jóváhagyásának listáját.

1. A **függőben** lévő lapon keresse meg a kérelmet.

## <a name="approve-or-deny-request"></a>Kérelem jóváhagyása vagy elutasítása

Miután megnyitotta a hozzáférési kérelmek jóváhagyását, megtekintheti azokat a részleteket, amelyek segítségével jóváhagyhatja vagy megtagadhatja a döntést.

**Előfeltételként szükséges szerepkör:** Jóváhagyó

1. Kattintson a **nézet** hivatkozásra a hozzáférési kérés panel megnyitásához.

1. A hozzáférési kérelem részleteinek megtekintéséhez kattintson a **részletek** gombra.

    A részletek tartalmazzák a felhasználó nevét, szervezetét, a hozzáférési kezdési és befejezési dátumát, ha van ilyen, az üzleti indoklás, a kérelem elküldésekor, valamint a kérelem érvényességének lejárta után.

1. Kattintson a **jóváhagyás** vagy a **Megtagadás**gombra.

1. Szükség esetén adjon meg egy okot.

    ![Hozzáférési portál – hozzáférési kérelem](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. A döntés elküldéséhez kattintson a **Submit (Küldés** ) gombra.

    Ha egy házirend több jóváhagyóval van konfigurálva, csak egy jóváhagyónak kell döntenie a függőben lévő jóváhagyásról. Miután egy jóváhagyó elküldte döntését a hozzáférési kérésnek, a kérés befejeződött, és már nem érhető el a többi jóváhagyó számára a kérelem jóváhagyásához vagy elutasításához. A többi jóváhagyó láthatja a kérelemre vonatkozó döntést és a döntéshozót a saját hozzáférési portálján. Jelenleg csak egy egyfázisú jóváhagyás támogatott.

    Ha a konfigurált jóváhagyók egyike sem tudja jóváhagyni vagy megtagadni a hozzáférési kérelmet, a kérelem érvényessége a konfigurált kérelem időtartama után lejár. A felhasználó értesítést kap arról, hogy a hozzáférési kérelem lejárt, és újra el kell küldenie a hozzáférési kérést.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
