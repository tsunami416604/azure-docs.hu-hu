---
title: Azure multifaktorhitelesítési szolgáltatók – Azure Active Directory
description: Mikor érdemes auth-szolgáltatót használni az Azure MFA-val?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275e5ab394b54960a2340848152741762b28f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269381"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Mikor kell azure-os többtényezős hitelesítési szolgáltatót használni?

A kétlépéses ellenőrzés alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](howto-mfa-mfasettings.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure multifaktor hitelesítésszolgáltatója az Azure többtényezős hitelesítésáltal nyújtott funkciók előnyeit használja **a licenccel nem rendelkező**felhasználók számára.

> [!NOTE]
> Szeptember 1-i hatállyal előfordulhat, hogy 2018. A meglévő hitelesítésszolgáltatók továbbra is használhatók és frissíthetők, de az áttelepítés már nem lehetséges. A többtényezős hitelesítés továbbra is elérhető lesz az Azure AD Premium-licencek szolgáltatásaként.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Az Azure MFA SDK-val kapcsolatos kikötések

Megjegyzés: az SDK elavult, és csak 2018. Ezután az SDK felé indított hívások meghiúsulnak.

## <a name="what-is-an-mfa-provider"></a>Mi az az MFA-szolgáltató?

Kétféle auth-szolgáltatók, és a különbség az Azure-előfizetés díja körül. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ezt a lehetőséget akkor érdemes használni, ha néhány felhasználó csak alkalmanként végez hitelesítést. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="manage-your-mfa-provider"></a>Az MFA-szolgáltató kezelése

Az MFA szolgáltató létrehozását követően már nem módosíthatja a használati modellt (engedélyezett felhasználónként vagy hitelesítésenként).

Ha elegendő licencet vásárolt az MFA-hoz engedélyezett összes felhasználó lefedéséhez, teljesen törölheti az MFA-szolgáltatót.

Ha az MFA szolgáltató nincs Azure AD-bérlőhöz kapcsolva, vagy új MFA szolgáltatót kapcsol egy másik Azure AD-bérlőhöz, a felhasználói és konfigurációs beállításokat a rendszer nem viszi át. Emellett a meglévő Azure MFA-kiszolgálókat újra kell aktiválni az MFA-szolgáltatón keresztül létrehozott aktiválási hitelesítő adatok használatával.

### <a name="removing-an-authentication-provider"></a>Hitelesítésszolgáltató eltávolítása

> [!CAUTION]
> A hitelesítésszolgáltató törlésekor nincs megerősítés. **A Törlés** lehetőség állandó folyamat.

A hitelesítésszolgáltatók az > Azure Active > **Directory-biztonsági** > **MFA-szolgáltatók** **Azure Portalon** > **Azure Active Directory**találhatók.**Providers** Kattintson a felsorolt szolgáltatókra a szolgáltatóhoz társított részletek és konfigurációk megtekintéséhez.

A hitelesítésszolgáltató eltávolítása előtt vegye figyelembe a szolgáltatóban beállított egyéni beállításokat. Döntse el, hogy mely beállításokat kell áttelepíteni a szolgáltatóáltalános MFA-beállításaira, és fejezze be a beállítások áttelepítését. 

A szolgáltatókhoz kapcsolódó Azure MFA-kiszolgálókat újra kell aktiválni az **Azure** > **Active Directory** > **Security** > **MFA** > Server**beállításai**között létrehozott hitelesítő adatokkal. Az újraaktiválás előtt a következő fájlokat `\Program Files\Multi-Factor Authentication Server\Data\` törölni kell az Azure MFA-kiszolgálók címtárából a környezetben:

- caCert között
- Bizonyos
- csoportCACert
- csoportkulcs
- csoportneve
- licenckulcs
- pkey (pkey)

![Hitelesítésszolgáltató törlése az Azure Portalról](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Miután megerősítette, hogy az összes beállítás átlett telepítve, tallózhat az Azure Active Directory **...**  > **biztonsági** > **mfa-szolgáltatói** > **Providers** **nak azure-portálján,** > **Azure Active Directory**és kiválaszthatja a három pontot ... és a **Törlés**lehetőséget.

> [!WARNING]
> A hitelesítésszolgáltató törlésével törlődnek az adott szolgáltatóhoz társított jelentési adatok. Előfordulhat, hogy a szolgáltató törlése előtt menteni szeretné a tevékenységjelentéseket.

> [!NOTE]
> Előfordulhat, hogy a Microsoft Authenticator alkalmazás régebbi verzióival rendelkező felhasználóknak újra regisztrálniuk kell az alkalmazásukat.

## <a name="next-steps"></a>További lépések

[A Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)
