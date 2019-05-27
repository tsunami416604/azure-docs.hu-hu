---
title: Az MFA engedélyezése az összes Azure-rendszergazdák
description: Útmutató a globális rendszergazda engedélyezése
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: fbc3bba3d95526277da3487cb8670c1f1a88a925
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951768"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Többtényezős hitelesítés (MFA) az előfizetés adminisztrátorainak

A rendszergazdák, többek között a globális rendszergazdai fiók létrehozásakor fontos, hogy nagyon erős hitelesítési módszereket használ.

Napi szintű felügyelete által meghatározott rendszergazdai szerepkörök hozzárendelése hajthat végre – például az Exchange-rendszergazda vagy a jelszókezelő – felhasználói fiókokhoz az informatikai részleg igény szerint.
Ezenkívül engedélyezése [Azure multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) a rendszergazdáknak egy második biztonsági réteget ad a felhasználói bejelentkezéseket és tranzakciókat. Az Azure MFA is segít, informatikai így kevésbé valószínű, hogy egy sérült biztonságú hitelesítő adat rendelkezik-e a munkahelyi adatokhoz való hozzáférést.

Példa: Az Azure MFA kényszerítéséhez a felhasználók számára, és konfigurálja úgy, hogy az ellenőrzés a telefonhívás vagy SMS-t használja. Ha a felhasználói hitelesítő adatok integritása sérül, a támadó nem fog tudni bármely erőforráshoz hozzáférhet, mivel a hozzáférést a felhasználó telefonja nem rendelkeznek. A szervezetek, ne vegyen fel több réteg az identity protection jobban ki a hitelesítő adatok ellopását alkalmazó támadásokkal, amely lehet a biztonsági adatokat.

Olyan szervezeteknek, amelyek szeretné tartani a teljes hitelesítés vezérlőelem a helyszínen egy alternatív használatára [Azure multi-factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), más néven "MFA helyszíni". Ez a módszer segítségével, továbbra is képesek lesznek a multi-factor authentication, miközben a kiszolgáló helyszíni MFA kényszerítésére.

Jelölje ki a szervezet rendelkezik rendszergazdai jogosultságokkal a következő Microsoft Azure AD V2 PowerShell-paranccsal ellenőrizheti:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Többtényezős hitelesítés engedélyezése

Felülvizsgálat hogyan [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) működik, mielőtt továbblépne.

Ha a felhasználói rendelkeznek az Azure Multi-Factor Authenticationt magában foglaló licenccel, semmit nem kell tennie az Azure MFA bekapcsolásához. Felhasználói alapon, egyenként kezdeményezheti a kétlépéses ellenőrzés igénylését. Az Azure MFA használatát lehetővé tévő licencek:

- Azure Multi-Factor Authentication
- Prémium szintű Azure Active Directory
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>A kétlépéses ellenőrzés bekapcsolása a felhasználók számára

A felsorolt eljárásokkal [igénylése a kétlépéses ellenőrzés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) egy felhasználó vagy csoport, az Azure MFA használatának megkezdéséhez. Választhat, hogy kétlépéses ellenőrzést követel meg az összes bejelentkezéshez, vagy olyan feltételes hozzáférési szabályzatokat hoz létre, amelyek csak olyan esetekben követelnek meg kétlépéses ellenőrzést, amelyekben annak szükségét érzi.

