---
title: "Az Azure Active Directory Identity Protection - letiltásának feloldása felhasználók |} Microsoft Docs"
description: "Megtudhatja, hogyan feloldása az Azure Active Directory Identity Protection-házirend által blokkolt felhasználóknak."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, felhasználó tiltásának feloldása"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 67fc333a47333fff0cd95aebd0616892cd6c6a42
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Az Azure Active Directory Identity Protection - letiltásának feloldása felhasználók
Az Azure Active Directory azonosító adatok védelmét beállíthatja a házirendek felhasználók blokkolására, ha a megadott feltételek teljesülnek. Általában egy letiltott felhasználó névjegyek támogatási szolgálat lesz oldva. A témakörök végezhet egy letiltott felhasználó feloldásának lépéseit ismerteti.

## <a name="determine-the-reason-for-blocking"></a>Blokkolja okának meghatározása
Első lépésként a felhasználó tiltásának feloldása meg kell határoznia, mert a következő lépéseket a attól függően, hogy a felhasználó letiltotta szabályzat típusát.
Az Azure Active Directory azonosító adatok védelmét a felhasználó vagy blokkolhatja a bejelentkezési kockázat házirendnek vagy egy felhasználó kockázat házirendnek.

A házirend letiltotta a felhasználó a megjelenő párbeszédpanelen, amely során egy bejelentkezési kísérlet után jelenik meg a felhasználó a fejléc típusú szerezheti be:

| Szabályzat | Felhasználó párbeszédpanel |
| --- | --- |
| Bejelentkezési kockázata |![Letiltott bejelentkezés](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Felhasználói kockázata |![Letiltott fiók](./media/active-directory-identityprotection-unblock-howto/104.png) |

A felhasználó által blokkolt:

* Gyanús bejelentkezés más néven egy bejelentkezési kockázat házirendnek
* Egy felhasználó kockázat házirendnek is olyan veszélyben fiók

## <a name="unblocking-suspicious-sign-ins"></a>Feloldásáról gyanús bejelentkezések
A gyanús bejelentkezés feloldásához a következő lehetőségei vannak:

1. **Bejelentkezés ismerős helyről vagy eszköz** -gyakori oka a letiltott gyanús bejelentkezések ismeretlen helyekről vagy eszközök bejelentkezési kísérlet. A felhasználók gyorsan megállapíthatja, hogy ez-e a zárolási ok úgy, hogy bejelentkezés ismerős helyről vagy eszköz.
2. **Zárja ki a házirend** – Ha úgy gondolja, hogy aktuális beállításait, a bejelentkezési házirend olyan felhasználók számára problémákat okoz, kizárhatja a felhasználók belőle. Lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md) további részleteket.
3. **Tiltsa le a házirend** – Ha úgy gondolja, hogy a házirend-konfigurációt a felhasználók számára problémákat okoz, letilthatja a házirendet. Lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md) további részleteket.

## <a name="unblocking-accounts-at-risk"></a>Veszélyben feloldásáról fiókok
Olyan veszélyben fiók zárolásának feloldásához a következő lehetőségei vannak:

1. **Jelszó-átállítási** -visszaállíthatja a jelszót. Lásd: [manuális biztonságos jelszó-átállítási](active-directory-identityprotection.md#manual-secure-password-reset) további részleteket.
2. **Minden kockázati események elvetése** – a felhasználói kockázat házirend blokkok egy felhasználót, ha a beállított felhasználói kockázati szint blokkolja a hozzáférést a el lett érve. Csökkentheti a felhasználó kockázati események jelentett manuálisan bezárásával kockázati szintjét. További részletekért lásd: [kockázati események manuálisan bezárása](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Zárja ki a házirend** – Ha úgy gondolja, hogy aktuális beállításait, a bejelentkezési házirend olyan felhasználók számára problémákat okoz, kizárhatja a felhasználók belőle. Lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md) további részleteket.
4. **Tiltsa le a házirend** – Ha úgy gondolja, hogy a házirend-konfigurációt a felhasználók számára problémákat okoz, letilthatja a házirendet. Lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md) további részleteket.

## <a name="next-steps"></a>Következő lépések
 Meg szeretné ismerni további információk az Azure AD Identity Protection? Tekintse meg [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
