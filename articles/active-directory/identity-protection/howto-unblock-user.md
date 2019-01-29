---
title: Hogyan lehet az Azure Active Directory Identity Protection felhasználók tiltásának feloldása |} A Microsoft Docs
description: Ismerje meg, hogyan feloldása, amely egy Azure Active Directory Identity Protection-szabályzat által letiltott felhasználók.
services: active-directory
keywords: az Azure active directory identity protection a következőket felhasználó blokkolásának feloldása
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 9feb54c239800346426d7196a1c303ed5930de16
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192451"
---
# <a name="how-to-unblock-users"></a>kézikönyv: Felhasználók tiltásának feloldása

Az Azure Active Directory Identity Protection a házirendek felhasználók letiltását, a konfigurált feltételek teljesülése esetén konfigurálhatja. Általában egy letiltott felhasználó névjegyek segélyszolgálat lesz feloldva. Ez a cikk ismerteti a lépéseket egy letiltott felhasználó feloldásának is végezhet.

## <a name="determine-the-reason-for-blocking"></a>Blokkolás okának megállapításához
Első lépésként egy felhasználó tiltásának feloldásához meg kell határoznia, hogy a felhasználó letiltotta, mert a következő lépések, attól függően, hogy a házirend típusát.
Az Azure Active Directory Identity Protection a felhasználó vagy blokkolhatja bejelentkezési kockázati szabályzat vagy felhasználói kockázati házirendet.

Megtekintheti a szabályzatot, amely a címsor a párbeszédpanelt, amely során egy bejelentkezési kísérlet után a felhasználó számára a felhasználó letiltotta típusát:

| Szabályzat | Felhasználói párbeszédpanel |
| --- | --- |
| Bejelentkezési kockázat |![Letiltott bejelentkezési](./media/howto-unblock-user/02.png) |
| Felhasználói kockázat |![Letiltott fiók](./media/howto-unblock-user/104.png) |

A felhasználó által blokkolt:

* A bejelentkezési kockázati házirend néven is ismert gyanús bejelentkezési.
* Felhasználói kockázati házirendet egy néven is ismert kitett fiók

## <a name="unblocking-suspicious-sign-ins"></a>Gyanús blokkolásának feloldása bejelentkezések
A gyanús bejelentkezési feloldásához a következő lehetőségek állnak rendelkezésére:

1. **Jelentkezzen be a jól ismert helyről vagy eszköz** -letiltott gyanús bejelentkezési gyakori indoka bejelentkezési kísérletek ismeretlen helyről vagy eszközök. A felhasználók gyorsan megállapíthassák blokkoló ezért úgy, hogy jelentkezzen be egy ismert helyre vagy az eszközről-e.
2. **A házirend kizárása** – Ha úgy véli, hogy a bejelentkezési szabályzat aktuális konfigurációja megadott felhasználók számára problémákat okoz, kizárhatja azokat a felhasználókat belőle. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Szabályzat letiltása** – Ha úgy véli, hogy a házirend-konfiguráció okozza-e problémák a felhasználók számára, hogy letilthatja a szabályzatot. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Veszélyben blokkolásának feloldása fiókok
Érintett fiók zárolásának feloldásához a következő lehetőségek állnak rendelkezésére:

1. **Jelszó alaphelyzetbe állítása** -visszaállíthatja a jelszót. 
2. **Az összes kockázati esemény elvetését** – a felhasználó kockázati szabályzat akkor tiltja a felhasználó, ha a beállított felhasználói kockázati szint blokkolja a hozzáférést a rendszer elérte. Csökkentheti a felhasználó kockázati események jelentett kockázati szint kézzel zárja be. 
3. **A házirend kizárása** – Ha úgy véli, hogy a bejelentkezési szabályzat aktuális konfigurációja megadott felhasználók számára problémákat okoz, kizárhatja azokat a felhasználókat belőle. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Szabályzat letiltása** – Ha úgy véli, hogy a házirend-konfiguráció okozza-e problémák a felhasználók számára, hogy letilthatja a szabályzatot. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>További lépések
 
Szeretne többet megtudni az Azure AD Identity Protection? Tekintse meg [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
