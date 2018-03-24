---
title: Engedélyezi az MFA Használatát az összes Azure-rendszergazdák számára
description: Útmutató a globális rendszergazdai engedélyezése
ms.service: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: a247f5afbca491dc9c31c74453860961188411c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Többtényezős hitelesítés (MFA) az előfizetés rendszergazdáihoz kényszerítése

A rendszergazdák, többek között a globális rendszergazdai fiókjával létrehozásakor elengedhetetlen, hogy nagyon erős hitelesítési módszerek használatát.

Napi szintű felügyeleti végrehajthatják adott rendszergazdai szerepkörök hozzárendelése – például az Exchange-rendszergazda vagy a jelszó-rendszergazda – felhasználói fiókokhoz az informatikai részleg igény szerint.
Emellett engedélyezése [Azure multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) a rendszergazdák számára egy második biztonsági réteget ad a felhasználói bejelentkezéseket és tranzakciókat. Az Azure MFA is segít informatikai szervezet adatait a sérült biztonságú hitelesítő adatok hozzáférhet kevésbé valószínű.

Például: Azure MFA kényszerítéséhez a felhasználók számára, és konfigurálja úgy, hogy telefonhívást vagy SMS-üzenet használata ellenőrzése. Ha a felhasználói hitelesítő adatok integritása sérül, a támadó sem lesz bármilyen olyan erőforrás elérésére, mivel azt nem kell a hozzáférést a felhasználó telefonjára. A szervezetek, ne vegyen fel további identitás védelmi réteget amelyek jobban ki vannak téve a hitelesítő adatok jelszóellopásos támadáshoz, ami azt eredményezheti, hogy az adatok biztonsági sérülése.

Olyan szervezeteknek, amelyek az egész hitelesítési vezérlő helyszíni megtartja egy alternatíva a következő: használandó [Azure multi-factor Authentication kiszolgáló](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), más néven a "Multi-factor Authentication helyszíni". Ez a módszer használatával is tudja kényszeríteni a többtényezős hitelesítést a multi-factor Authentication kiszolgáló helyszíni megtartásával.

Jelölje ki a szervezet számára a következő Microsoft Azure AD V2 PowerShell paranccsal ellenőrizheti rendszergazdai jogosultságokkal rendelkezik:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Többtényezős hitelesítés engedélyezése

Felülvizsgálati hogyan [MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-whats-next) működik, mielőtt továbblép.

Ha a felhasználói rendelkeznek az Azure Multi-Factor Authenticationt magában foglaló licenccel, semmit nem kell tennie az Azure MFA bekapcsolásához. Felhasználói alapon, egyenként kezdeményezheti a kétlépéses ellenőrzés igénylését. Az Azure MFA használatát lehetővé tévő licencek:

- Azure Multi-Factor Authentication
- Prémium szintű Azure Active Directory
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>A kétlépéses ellenőrzés bekapcsolása a felhasználók számára

A felsorolt eljárásokkal [kétlépéses ellenőrzés megkövetelése](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) egy felhasználó vagy csoport Azure multi-factor Authentication használatának megkezdéséhez. Választhat, hogy kétlépéses ellenőrzést követel meg az összes bejelentkezéshez, vagy olyan feltételes hozzáférési szabályzatokat hoz létre, amelyek csak olyan esetekben követelnek meg kétlépéses ellenőrzést, amelyekben annak szükségét érzi.

