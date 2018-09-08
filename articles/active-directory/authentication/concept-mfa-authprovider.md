---
title: Mikor és hogyan használhatja az Azure multi-factor Auth szolgáltatót?
description: Mikor használjam a hitelesítésszolgáltatót az Azure MFA használatba Vételekor?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 8e77a33667bd6794f667348958e0edb9c6a8fb0d
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094977"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Mikor érdemes használni az Azure multi-factor Authentication-szolgáltatót

A kétlépéses ellenőrzés alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](howto-mfa-mfasettings.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure multi-factor Auth szolgáltató segítségével a felhasználók számára az Azure multi-factor Authentication szolgáltatás által biztosított szolgáltatások előnyeinek akik **nem rendelkeznek licenccel**.

Ha rendelkezik licencekkel, mind a minden felhasználó a szervezetben, majd nincs szüksége Azure multi-factor Auth szolgáltatót. Hozzon létre egy Azure multi-factor Authentication-szolgáltatót, csak akkor, ha is meg kell adnia a kétlépéses ellenőrzést, amelyek nem rendelkeznek licenccel az egyes felhasználók számára.

> [!NOTE]
> Hatékony 2018. szeptember 1-től. új auth-szolgáltatók többé nem hozható létre. Meglévő hitelesítésszolgáltatók előfordulhat, hogy továbbra is használja, és frissítve. A multi-factor authentication továbbra is egy elérhető funkciónak az Azure AD Premium-licencet.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Az Azure MFA SDK kapcsolatos figyelmeztetések

Az SDK letöltéséhez egy Azure Multi-Factor Auth szolgáltató szükséges. Megjegyzés: az SDK elavult, és az új ügyfelek már nem támogatott, és csak továbbra is működni fognak 2018. November 14-ig. Ezután az SDK felé indított hívások meghiúsulnak.

Az SDK letöltéséhez hozzon létre egy Azure multi-factor Auth szolgáltatót, ha már rendelkezik Azure MFA, AAD Premium vagy egyéb csomagolt licencek. Ha Azure Multi-Factor Auth szolgáltatót hoz létre erre a célra, és már rendelkezik licencekkel, ügyeljen arra, hogy a szolgáltatót az **Engedélyezett felhasználónként** modellel hozza létre. Ezt követően kapcsolja a szolgáltatót arra a könyvtárra, amely tartalmazza az Azure MFA, Azure AD Premium vagy egyéb csomagolt licencek. Ezzel a konfigurációval biztosíthatja, hogy csak akkor számlázzunk Önnek, amikor több egyedi felhasználója végez kétlépéses ellenőrzést, mint ahány licenccel rendelkezik.

## <a name="what-is-an-mfa-provider"></a>Mi az az MFA-szolgáltató?

Hitelesítésszolgáltatók két típusa van, és a különbséget körül módját az Azure-előfizetés után kell fizetni. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ezt a lehetőséget akkor érdemes használni, ha néhány felhasználó csak alkalmanként végez hitelesítést. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="manage-your-mfa-provider"></a>Az MFA-szolgáltató kezelése

Az MFA szolgáltató létrehozását követően már nem módosíthatja a használati modellt (engedélyezett felhasználónként vagy hitelesítésenként).

Ha az összes olyan felhasználó, a multi-factor Authentication engedélyezett elegendő licenccel vásárolta, érvényesítette törölheti az MFA szolgáltatót.

Ha az MFA szolgáltató nincs Azure AD-bérlőhöz kapcsolva, vagy új MFA szolgáltatót kapcsol egy másik Azure AD-bérlőhöz, a felhasználói és konfigurációs beállításokat a rendszer nem viszi át. Továbbá a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az MFA szolgáltatón keresztül létrehozott aktiváló hitelesítő adatok használatával kell. Az MFA-kiszolgálók összekapcsolása őket az MFA szolgáltatót újraaktiválása nem érinti, telefonhívás vagy szöveges üzenetben, de mobilapp-értesítések leáll az összes felhasználó számára addig, amíg a mobilalkalmazás hitelesítésekre.

## <a name="next-steps"></a>További lépések

[A Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)
