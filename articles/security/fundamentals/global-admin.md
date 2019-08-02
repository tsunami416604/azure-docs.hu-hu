---
title: MFA engedélyezése az összes Azure-rendszergazda számára
description: Útmutató a globális rendszergazda engedélyezéséhez
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 702feded450aed7368836c7eff799e969b9f396b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727300"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>A többtényezős hitelesítés (MFA) érvényesítése az előfizetés-rendszergazdák számára

A rendszergazdák létrehozásakor, beleértve a globális rendszergazdai fiókot is, elengedhetetlen, hogy nagyon erős hitelesítési módszereket használjon.

A napi felügyeletet meghatározott rendszergazdai szerepkörök, például az Exchange-rendszergazda vagy a jelszó-rendszergazda hozzárendelésével végezheti el, szükség szerint az informatikai munkatársak felhasználói fiókjaihoz.
Emellett az [Azure multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) engedélyezése a rendszergazdák számára egy második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókra. Az Azure MFA emellett csökkenti annak a valószínűségét, hogy a feltört hitelesítő adatok hozzáférjenek a szervezet adataihoz.

Példa: Ön kényszeríti az Azure MFA használatát a felhasználók számára, és úgy konfigurálja, hogy telefonhívást vagy szöveges üzenetet használjon ellenőrzésként. Ha a felhasználó hitelesítő adatai biztonságban vannak, a támadó nem fog tudni hozzáférni az erőforrásokhoz, mivel azok nem férnek hozzá a felhasználó telefonjára. Azok a szervezetek, amelyek nem vesznek fel további identitás-védelmet, érzékenyebbek a hitelesítő adatok ellopása elleni támadásokra, ami adatsérülést eredményezhet.

Egy másik lehetőség azon szervezetek számára, amelyek a teljes hitelesítési vezérlést szeretnék megőrizni a helyszínen, az [Azure multi-Factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), más néven a "MFA on-helyszíni" használata. Ennek a módszernek a használatával továbbra is kikényszerítheti a többtényezős hitelesítést, miközben az MFA-kiszolgálót a helyszínen tartja.

Annak ellenőrzéséhez, hogy a szervezeten belül kik rendelkeznek-e rendszergazdai jogosultságokkal, a következő Microsoft Azure AD v2 PowerShell-paranccsal ellenőrizheti:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>MFA engedélyezése

A folytatás előtt tekintse át az [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) működését.

Ha a felhasználói rendelkeznek az Azure Multi-Factor Authenticationt magában foglaló licenccel, semmit nem kell tennie az Azure MFA bekapcsolásához. Felhasználói alapon, egyenként kezdeményezheti a kétlépéses ellenőrzés igénylését. Az Azure MFA használatát lehetővé tévő licencek:

- Azure Multi-Factor Authentication
- Prémium szintű Azure Active Directory
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>A kétlépéses ellenőrzés bekapcsolása a felhasználók számára

Az Azure MFA használatának megkezdéséhez használja a [két lépésből álló ellenőrzés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) megkövetelése a felhasználók vagy csoportok számára című témakörben felsorolt eljárások egyikét. Dönthet úgy is, hogy kikényszeríti a kétlépéses ellenőrzést az összes bejelentkezésnél, vagy létrehozhat feltételes hozzáférési szabályzatokat, hogy csak akkor igényeljen kétlépéses ellenőrzést, ha az Ön számára fontos.

