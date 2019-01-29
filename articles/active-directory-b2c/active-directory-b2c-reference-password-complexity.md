---
title: Jelszó erőssége az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan kell konfigurálni a bonyolultsági feltételeknek az Azure Active Directory B2C a fogyasztók által megadott jelszót.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1106a67d084020b714f0204b9012076817f9a4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176064"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Jelszavak bonyolultságát követelmények konfigurálása

> [!NOTE]
> **Ez a funkció jelenleg nyilvános előzetes verzióban.**

Az Azure Active Directory B2C (Azure AD B2C-vel) támogatja a bonyolultsági feltételeknek-fiók létrehozása során a felhasználó által megadott jelszavak módosítása.  Alapértelmezés szerint az Azure AD B2C-t használja `Strong` jelszavakat.  Az Azure AD B2C-t is támogatja a konfigurációs beállítások vezérléséhez, amellyel az ügyfelek jelszavak bonyolultságát.

## <a name="when-password-rules-are-enforced"></a>Amikor jelszó szabályok életbe lépnek

A regisztrációhoz, vagy a jelszó-visszaállítás, a végfelhasználó, amely megfelel a szabályoknak jelszót kell megadnia.  Jelszó-bonyolultsági szabályokat a rendszer egy felhasználói folyamat érvényesíti.  A regisztráció ideje alatt egy másik felhasználói folyamat nyolc karakterből álló karakterláncnak szükséges a regisztrációhoz négyjegyű PIN-kódot egy felhasználói folyamat lehetőség.  Például használhat egy felhasználói folyamat a különböző jelszavak összetettségére felnőttek, mint a gyermekek számára.

Jelszó bonyolultsága bejelentkezései soha nem lép érvénybe.  Felhasználók bejelentkezései soha nem kéri a jelszó módosítására, mert nem felel meg az aktuális bonyolultsági követelményeknek.

Íme a felhasználói folyamatok típusú, ahol konfigurálható a jelszó erőssége:

* Regisztrálási vagy bejelentkezési felhasználói folyamata
* Jelszó alaphelyzetbe állítása felhasználói folyamat
* Egyéni szabályzat ([jelszóösszetettség konfigurálása egyéni házirendek](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Jelszó bonyolultsága konfigurálása

1. Nyissa meg **felhasználókövetési adatai**.
2. Válassza ki a felhasználói folyamat, és kattintson a **tulajdonságok**.
3. A **jelszóösszetettség**, módosítsa a jelszó erőssége ezen felhasználói folyamat **egyszerű**, **erős**, vagy **egyéni**.

### <a name="comparison-chart"></a>Összehasonlítási táblázata

| Összetettség | Leírás |
| --- | --- |
| Egyszerű | Ez a jelszó legalább 8 és 64 karakter hosszú lehet. |
| Erős | Ez a jelszó legalább 8 és 64 karakter hosszú lehet. 3 / 4 kisbetűk, nagybetűk, számok és szimbólumok van szükség. |
| Egyéni | Ezt a beállítást a jelszó-bonyolultsági szabályokat a legtöbb felett biztosít.  Ez lehetővé teszi, hogy egy egyéni hosszának konfigurálása.  Lehetővé teszi elfogadásával csak szám jelszavak (PIN). |

## <a name="options-available-under-custom"></a>Elérhető lehetőségek mellett egyéni

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy fogadja el a számjegyek csak (PIN) vagy a teljes készletet.

* **Csak számok** lehetővé teszi, hogy a számjegyek csak (0 – 9) során, hogy jelszót írna be.
* **Az összes** lehetővé teszi, hogy bármely betűvel, számmal vagy szimbólumot.

### <a name="length"></a>Hossz

Lehetővé teszi, hogy szabályozza a hosszúsági követelményeknek, a jelszó.

* **Minimális hossz** legalább 4 kell lennie.
* **Maximális hossz** nagyobb vagy azzal egyenlő minimális hosszának kell lennie, és legfeljebb 64 karakter lehet.

### <a name="character-classes"></a>Karakterosztályok

Segítségével szabályozhatja a különböző karakterkészletnek a jelszót használja.

* **2 / 4: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó legalább két karakter típusú tartalmaz. Például egy számot és egy kisbetűt.
* **3. a 4-ből: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó legalább két karakter típusú tartalmaz. Például több, egy kisbetűt és a egy nagybetűs karaktert.
* **4. 4: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó tartalmazza az összes karaktertípusokat.

    > [!NOTE]
    > Igénylő **4 4** végfelhasználói as gazdasági válság után eredményezhet. Néhány tanulmányok kimutatták, hogy ez a követelmény nem növelhető az jelszó vysokou. Lásd: [NIST jelszavakra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
