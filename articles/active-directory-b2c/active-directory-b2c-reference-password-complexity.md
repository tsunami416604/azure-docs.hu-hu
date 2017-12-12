---
title: "Jelszó erőssége – az Azure AD B2C |} Microsoft Docs"
description: "Az Azure Active Directory B2C fogyasztói által biztosított jelszavak vonatkozó összetettségi követelményeknek konfigurálása"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 3906c9fa1def206a8f0a7e155949097242728c2f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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
