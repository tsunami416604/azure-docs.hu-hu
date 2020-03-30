---
title: Az Azure Active Directory műveleti útmutatója
description: Ez a műveleti útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket az identitás- és hozzáférés-kezelés, a hitelesítés, a vállalatirányítás és a műveletek védelme és fenntartása érdekében kell tennie.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535313"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Az Azure Active Directory műveleti útmutatója

Ez a műveleti útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket a következő területek biztonságossá tétele és karbantartása érdekében kell tennie:

- **[Identitás- és hozzáférés-kezelés](active-directory-ops-guide-iam.md)** – az identitások és jogosultságaik életciklusának kezelésére való képesség.
- **[Hitelesítéskezelés](active-directory-ops-guide-auth.md)** – a hitelesítő adatok kezelésének, a hitelesítési élmény meghatározásának, a hozzárendelés delegálásának, a használat mérésének és a hozzáférési házirendek vállalati biztonsági állapot alapján való meghatározásának képessége.
- **[Cégirányítás](active-directory-ops-guide-govern.md)** – képes felmérni és tanúsítani a hozzáférést biztosított nem kiemelt és kiemelt identitások, naplózás, és a környezet változásainak ellenőrzése.
- **[Műveletek](active-directory-ops-guide-ops.md)** – optimalizálja az Azure Active Directory (Azure AD) műveleteket.

Előfordulhat, hogy az itt szereplő javaslatok nem alkalmazhatók az összes ügyfél környezetére, például előfordulhat, hogy az AD FS ajánlott eljárásai nem érvényesek, ha a szervezet jelszókivonat-szinkronizálást használ.

> [!NOTE]
> Ezek az ajánlások a közzététel időpontjától aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell identitáskezelési gyakorlatukat, ahogy a Microsoft-termékek és -szolgáltatások idővel fejlődnek. A javaslatok módosíthatók, ha a szervezetek előfizetnek egy másik Azure AD Premium-licencre. Például az Azure AD Premium P2 további cégirányítási javaslatokat fog tartalmazni.

## <a name="stakeholders"></a>Érdekelt felek

A referenciaútmutató minden egyes szakasza azt javasolja, hogy az érdekelt feleket rendeljék meg a kulcsfontosságú feladatok sikeres megtervezéséhez és megvalósításához. Az alábbi táblázat az útmutatóban szereplő összes érdekelt fellistáját tartalmazza:

| Résztvevő | Leírás |
| :- | :- |
| IAM műveleti csapat | Ez a csapat kezeli az identitás- és hozzáférés-kezelési rendszer napi műveleteinek kezelését |
| Termelékenységi csapat | Ez a csapat birtokolja és kezeli a hatékonyságnövelő alkalmazásokat, például az e-maileket, a fájlmegosztást és együttműködést, az azonnali üzenetküldést és a konferenciákat. |
| Alkalmazás tulajdonosa | Ez a csapat birtokolja az adott alkalmazást egy üzleti és általában egy szervezet technikai szempontból. |
| InfoSec architektúra csapata | Ez a csapat egy szervezet információbiztonsági gyakorlatát tervezi és tervezi meg. |
| InfoSec műveleti csapat | Ez a csapat futtatja és figyeli az InfoSec architektúra csapat implementált információbiztonsági gyakorlatait. |

## <a name="next-steps"></a>További lépések

Ismerkedés az [identitás- és hozzáférés-kezelési ellenőrzésekkel és műveletekkel.](active-directory-ops-guide-iam.md)
