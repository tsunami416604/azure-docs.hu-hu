---
title: Jelszó erőssége – az Azure AD B2C |} Microsoft Docs
description: Az Azure Active Directory B2C fogyasztói által biztosított jelszavak vonatkozó összetettségi követelményeknek konfigurálása
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: b89a404d583e9d495b65180e145295e6a106b3ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Az Azure AD B2C: Bonyolult jelszót konfigurálása

> [!NOTE]
> **A funkció jelenleg előzetes verzió.**  Ügyfél [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) kell rendelkeznie a tesztbérlővel, ez a szolgáltatás engedélyezve van.  Csak akkor teszteljen Ez a termelési bérlők.

Az Azure Active Directory B2C (az Azure AD B2C) támogatja a bonyolultsági feltételeknek, egy fiók létrehozásakor a felhasználó által megadott jelszavak módosítása.  Alapértelmezés szerint az Azure AD B2C használja `Strong` jelszavakat.  Az Azure AD B2C beállítások szabályozzák, hogy az ügyfelek használhatják a összetettségét is támogatja.

## <a name="when-password-rules-are-enforced"></a>Ha a jelszó-szabályok érvényesek

Regisztráció során vagy a jelszó alaphelyzetbe állítása, amelyet a végfelhasználók, amely megfelel a bonyolultsági szabályokat jelszót kell megadnia.  Jelszó-bonyolultsági szabályokat száma szabályzat lépnek érvénybe.  Lehetséges, hogy egy négyjegyű PIN-kódot előfizetési kicsit során egy másik házirend nyolc karakterlánc szükséges regisztráció során több szabályzatot is.  Például használhatja egy házirend különböző jelszó erősségét a felnőttek mint számára készült.

Jelszó erőssége soha nem kényszerített bejelentkezés során.  Rendszer soha nem kéri a felhasználóktól bejelentkezés során a jelszó módosítására, mert nem felel meg az aktuális bonyolultsági követelményeknek.

Íme szabályzattípusokat, ahol a jelszó erősségét konfigurálható:

* Regisztráció vagy bejelentkezés házirend
* Jelszó-visszaállítási házirend
* Egyéni házirend ([állítsa be a jelszó erősségét egyéni házirendek](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Jelszó erőssége konfigurálása

1. Az alábbi lépéseket követve [navigáljon az Azure AD B2C beállítások](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Nyissa meg **regisztráció vagy bejelentkezés házirendek**.
1. Jelöljön ki egy házirendet, és kattintson a **szerkesztése**.
1. Nyissa meg **jelszó erőssége**.
1. A jelszó erősségét a házirend módosítása **egyszerű**, **erős**, vagy **egyéni**.

### <a name="comparison-chart"></a>Összehasonlítási táblázat

| Összetettsége | Leírás |
| --- | --- |
| Egyszerű | Legalább 8 – 64 karakter hosszú jelszót. |
| Erős | Legalább 8 – 64 karakter hosszú jelszót. Igényel 3 kívüli 4, kisbetű, nagybetű, számok és szimbólumok. |
| Egyéni | Ez a beállítás segítségével a jelszó-bonyolultsági szabályokat a legtöbb szabályozhatja.  Lehetővé teszi egy egyéni hosszának konfigurálása.  Azt is lehetővé teszi, hogy elfogadása csak szám jelszavak (PIN-ek). |

## <a name="options-available-under-custom"></a>Rendelkezésre álló lehetőségek egyéni alatt

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy el kell fogadnia a számjegyek csak (PIN-ek) vagy a teljes karakterkészlet.

* **Csak számokat** lehetővé teszi, hogy a számjegyek csak (0-9) során, hogy jelszót írna be.
* **Minden** lehetővé teszi, hogy bármely karakter, számot vagy szimbólumot.

### <a name="length"></a>Hossz

Lehetővé teszi, hogy a hosszúsági követelményeknek, a jelszó vezérlését.

* **Minimális hossz** legalább 4 kell lennie.
* **Maximális hossz** nagyobbnak vagy minimális hosszának egyenlőnek kell lennie, és legfeljebb 64 karakter lehet.

### <a name="character-classes"></a>Négy karaktertípusból

Lehetővé teszi, hogy a különböző karaktertípusokat a jelszót használt vezérlését.

* **2. 4: kisbetűt, nagybetűt, számot (0-9), a szimbólum** biztosítja a jelszó legalább két karakter típusokat tartalmazza. Például egy számot és egy kisbetűt.
* **3, 4: kisbetűt, nagybetűt, számot (0-9), a szimbólum** biztosítja a jelszó legalább két karakter típusokat tartalmazza. Például: egy szám, egy kisbetűt, és egy nagybetűt.
* **4. 4: kisbetűt, nagybetűt, számot (0-9), a szimbólum** biztosítja a jelszó tartalmazza az összes a karaktertípusokat.

    > [!NOTE]
    > Igénylő **4 4** végfelhasználói eljárás megzavarását eredményezhet. Néhány a tanulmányok kimutatták, hogy ez a követelmény nem növelhető jelszó entrópiát. Lásd: [NIST jelszavakra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
