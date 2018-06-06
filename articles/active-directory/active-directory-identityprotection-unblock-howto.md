---
title: Az Azure Active Directory Identity Protection - letiltásának feloldása felhasználók |} Microsoft Docs
description: Megtudhatja, hogyan feloldása az Azure Active Directory Identity Protection-házirend által blokkolt felhasználóknak.
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, felhasználó tiltásának feloldása
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1e96f24aeb0083e57618ad3e38163f50c23c55d3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713456"
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Az Azure Active Directory Identity Protection - letiltásának feloldása felhasználók
Az Azure Active Directory azonosító adatok védelmét beállíthatja a házirendek felhasználók blokkolására, ha a megadott feltételek teljesülnek. Általában egy letiltott felhasználó névjegyek támogatási szolgálat lesz oldva. Ez a cikk ismerteti az egy letiltott felhasználó feloldásának végezheti el.

## <a name="determine-the-reason-for-blocking"></a>Blokkolja okának meghatározása
Első lépésként a felhasználó tiltásának feloldása meg kell határoznia, mert a következő lépéseket a attól függően, hogy a felhasználó letiltotta szabályzat típusát.
Az Azure Active Directory azonosító adatok védelmét a felhasználó vagy blokkolhatja a bejelentkezési kockázat házirendnek vagy egy felhasználó kockázat házirendnek.

A házirend letiltotta a felhasználó a megjelenő párbeszédpanelen, amely során egy bejelentkezési kísérlet után jelenik meg a felhasználó a fejléc típusú szerezheti be:

| Szabályzat | Felhasználó párbeszédpanel |
| --- | --- |
| Bejelentkezési kockázat |![Letiltott bejelentkezés](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Felhasználói kockázat |![Letiltott fiók](./media/active-directory-identityprotection-unblock-howto/104.png) |

A felhasználó által blokkolt:

* Gyanús bejelentkezés más néven egy bejelentkezési kockázat házirendnek
* Egy felhasználó kockázat házirendnek is olyan veszélyben fiók

## <a name="unblocking-suspicious-sign-ins"></a>Feloldásáról gyanús bejelentkezések
A gyanús bejelentkezés feloldásához a következő lehetőségei vannak:

1. **Jelentkezzen be a megszokott helyről vagy eszköz** -gyakori oka a letiltott gyanús bejelentkezések ismeretlen helyekről vagy eszközök bejelentkezési kísérlet. A felhasználók gyorsan megállapíthatja, hogy ez-e a zárolási ok úgy, hogy bejelentkezés ismerős helyről vagy eszköz.
2. **Zárja ki a házirend** – Ha úgy gondolja, hogy aktuális beállításait, a bejelentkezési házirend olyan felhasználók számára problémákat okoz, kizárhatja a felhasználók belőle. További információkért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
3. **Tiltsa le a házirend** – Ha úgy gondolja, hogy a házirend-konfigurációt a felhasználók számára problémákat okoz, letilthatja a házirendet. További információkért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Veszélyben feloldásáról fiókok
Olyan veszélyben fiók zárolásának feloldásához a következő lehetőségei vannak:

1. **Jelszó-átállítási** -visszaállíthatja a jelszót. További információkért lásd: [manuális biztonságos jelszó-átállítási](active-directory-identityprotection.md#manual-secure-password-reset).
2. **Minden kockázati események elvetése** – a felhasználói kockázat házirend blokkok egy felhasználót, ha a beállított felhasználói kockázati szint blokkolja a hozzáférést a el lett érve. Csökkentheti a felhasználó kockázati események jelentett manuálisan bezárásával kockázati szintjét. További információkért lásd: [kockázati események manuálisan bezárása](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Zárja ki a házirend** – Ha úgy gondolja, hogy aktuális beállításait, a bejelentkezési házirend olyan felhasználók számára problémákat okoz, kizárhatja a felhasználók belőle. További információkért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
4. **Tiltsa le a házirend** – Ha úgy gondolja, hogy a házirend-konfigurációt a felhasználók számára problémákat okoz, letilthatja a házirendet. További információkért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="next-steps"></a>További lépések
 Meg szeretné ismerni további információk az Azure AD Identity Protection? Tekintse meg [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
