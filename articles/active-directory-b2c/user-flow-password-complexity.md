---
title: Jelszó-összetettségi követelmények konfigurálása
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C-ben a fogyasztók által megadott jelszavak összetettségi követelményeinek konfigurálása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185641"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>A jelszavak összetettségére vonatkozó követelmények konfigurálása az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) támogatja a végfelhasználó által a fiók létrehozásakor megadott jelszavak összetettségi követelményeinek módosítását. Alapértelmezés szerint az Azure AD `Strong` B2C jelszavakat használ. Az Azure AD B2C is támogatja a konfigurációs beállításokat az ügyfelek által használható jelszavak összetettségének szabályozására.

## <a name="password-rule-enforcement"></a>Jelszószabály kényszerítése

A regisztráció vagy a jelszó alaphelyzetbe állítása során a végfelhasználónak meg kell adnia egy olyan jelszót, amely megfelel az összetettségi szabályoknak. A jelszó összetettségi szabályai felhasználói folyamatonként vannak érvényesítve. Lehetséges, hogy egy felhasználói folyamat négyjegyű pin-t igényel a regisztráció során, míg egy másik felhasználói folyamat nyolc karakteres karakterláncot igényel a regisztráció során. Használhat például egy felhasználói folyamatot, amelynek a felhasználója összetettsége a felnőttekszámára eltérő, mint a gyermekek esetében.

A jelszó összetettsége soha nem lép érvénybe a bejelentkezés során. A bejelentkezés során a felhasználók soha nem kérik a jelszavuk módosítását, mert az nem felel meg a jelenlegi összetettségi követelménynek.

A jelszó összetettségét a következő típusú felhasználói folyamatokban lehet konfigurálni:

- Regisztráció vagy bejelentkezés felhasználói folyamat
- Jelszó alaphelyzetbe állítása felhasználói folyamat

Ha egyéni házirendeket használ,[beállíthatja a jelszó összetettségét egy egyéni házirendben.](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Jelszó összetettségének konfigurálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
3. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
4. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
2. Jelöljön ki egy felhasználói folyamatot, és kattintson **a Tulajdonságok gombra.**
3. A **Jelszó összetettsége csoportban**módosítsa a felhasználói folyamat jelszóösszetettségét **Egyszerű,** **Erős**vagy **Egyéni**beállításra.

### <a name="comparison-chart"></a>Összehasonlító diagram

| Összetettség | Leírás |
| --- | --- |
| Egyszerű | Legalább 8–64 karakterből áll. |
| Erős | Legalább 8–64 karakterből áll. A kis- és nagybetűk, számok vagy szimbólumok 4-ből 3 szükséges. |
| Egyéni | Ez a beállítás biztosítja a jelszavak összetettségére vonatkozó szabályok legnagyobb szabályozását.  Ez lehetővé teszi az egyéni hossz konfigurálását.  Azt is lehetővé teszi, hogy elfogadja a csak szám jelszavak (csapok). |

## <a name="custom-options"></a>Egyéni beállítások

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy csak a számjegyeket (tűket) vagy a teljes karakterkészletet fogadja el.

- **A számok csak** a számjegyeket (0-9) engedélyezik jelszó beírása közben.
- **Minden** lehetővé teszi bármilyen betű, szám, vagy szimbólum.

### <a name="length"></a>Hossz

Lehetővé teszi a jelszó hosszkövetelményeinek szabályozását.

- **A minimális hossznak** legalább 4-nek kell lennie.
- **A maximális hossznak** nagyobbnak vagy egyenlőnek kell lennie a minimális hossznak, és legfeljebb 64 karakter lehet.

### <a name="character-classes"></a>Karakterosztályok

Lehetővé teszi a jelszóban használt különböző karaktertípusok vezérlését.

- **2/4: Kisbetű, Nagybetűs, Szám (0-9), A szimbólum biztosítja,** hogy a jelszó legalább két karaktertípust tartalmazzon. Például egy szám és egy kisbetűs karakter.
- **3/4: Kisbetű, Nagybetűs, Szám (0-9), A szimbólum biztosítja,** hogy a jelszó legalább három karaktertípust tartalmazzon. Például egy szám, egy kisbetű és egy nagybetű.
- **4/4: Kisbetű, Nagybetűs, Szám (0-9), A szimbólum** biztosítja, hogy a jelszó tartalmazza az összes karaktertípust.

    > [!NOTE]
    > A **4/4** megkövetelése a végfelhasználó frusztrációját eredményezheti. Egyes tanulmányok kimutatták, hogy ez a követelmény nem javítja a jelszó entrópiát. Lásd: [NIST-jelszóra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
