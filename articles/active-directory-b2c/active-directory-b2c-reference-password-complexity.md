---
title: Jelszó bonyolultsága – Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan kell konfigurálni a bonyolultsági feltételeknek az Azure Active Directory B2C a fogyasztók által megadott jelszót.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d7874d7c8d04e3d3565cdfe2e52e49c538b3091
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009801"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Jelszavak bonyolultsági feltételeknek konfigurálása az Azure Active Directory B2C-vel

Az Azure Active Directory (Azure AD) B2C támogatja a bonyolultsági feltételeknek-fiók létrehozása során a felhasználó által megadott jelszavak módosítása. Alapértelmezés szerint az Azure AD B2C-t használja `Strong` jelszavakat. Az Azure AD B2C-t is támogatja a konfigurációs beállítások vezérléséhez, amellyel az ügyfelek jelszavak bonyolultságát.

## <a name="password-rule-enforcement"></a>Jelszó szabály kényszerítése

A regisztrációhoz, vagy a jelszó-visszaállítás, a végfelhasználó, amely megfelel a szabályoknak jelszót kell megadnia. Jelszó-bonyolultsági szabályokat a rendszer egy felhasználói folyamat érvényesíti. A regisztráció ideje alatt egy másik felhasználói folyamat során szüksége van egy nyolc karakterből álló karakterlánc-előfizetési négyjegyű PIN-kódot egy felhasználói folyamat lehetőség. Például használhat egy felhasználói folyamat a különböző jelszavak összetettségére felnőttek, mint a gyermekek számára.

Jelszó bonyolultsága bejelentkezései soha nem lép érvénybe. Felhasználók bejelentkezései soha nem kéri a jelszó módosítására, mert nem felel meg az aktuális bonyolultsági követelményeknek.

Jelszó bonyolultsága felhasználókövetési adatai a következő típusú konfigurálható:

- Regisztrálási vagy bejelentkezési felhasználói folyamata
- Jelszó alaphelyzetbe állítása felhasználói folyamat

Ha egyéni szabályzatokat használ, akkor az ([jelszóösszetettség konfigurálása egyéni szabályzatokban](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Jelszó bonyolultsága konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **felhasználókövetési adatai**.
2. Válassza ki a felhasználói folyamat, és kattintson a **tulajdonságok**.
3. A **jelszóösszetettség**, módosítsa a jelszó erőssége ezen felhasználói folyamat **egyszerű**, **erős**, vagy **egyéni**.

### <a name="comparison-chart"></a>Összehasonlítási táblázata

| Összetettség | Leírás |
| --- | --- |
| Egyszerű | Ez a jelszó legalább 8 és 64 karakter hosszú lehet. |
| Erős | Ez a jelszó legalább 8 és 64 karakter hosszú lehet. 3 / 4 kisbetűk, nagybetűk, számok és szimbólumok van szükség. |
| Egyéni | Ezt a beállítást a jelszó-bonyolultsági szabályokat a legtöbb felett biztosít.  Ez lehetővé teszi, hogy egy egyéni hosszának konfigurálása.  Lehetővé teszi elfogadásával csak szám jelszavak (PIN). |

## <a name="custom-options"></a>Egyéni beállítások

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy fogadja el a számjegyek csak (PIN) vagy a teljes készletet.

- **Csak számok** lehetővé teszi, hogy a számjegyek csak (0 – 9) során, hogy jelszót írna be.
- **Az összes** lehetővé teszi, hogy bármely betűvel, számmal vagy szimbólumot.

### <a name="length"></a>Hossz

Lehetővé teszi, hogy szabályozza a hosszúsági követelményeknek, a jelszó.

- **Minimális hossz** legalább 4 kell lennie.
- **Maximális hossz** nagyobb vagy azzal egyenlő minimális hosszának kell lennie, és legfeljebb 64 karakter lehet.

### <a name="character-classes"></a>Karakterosztályok

Segítségével szabályozhatja a különböző karakterkészletnek a jelszót használja.

- **2 / 4: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó legalább két karakter típusú tartalmaz. Például egy számot és egy kisbetűt.
- **3. a 4-ből: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó legalább két karakter típusú tartalmaz. Például több, egy kisbetűt és a egy nagybetűs karaktert.
- **4. 4: Kisbetűt, nagybetűt, számot (0 – 9), a szimbólum** biztosítja, hogy a jelszó tartalmazza az összes karaktertípusokat.

    > [!NOTE]
    > Igénylő **4 4** végfelhasználói as gazdasági válság után eredményezhet. Néhány tanulmányok kimutatták, hogy ez a követelmény nem növelhető az jelszó vysokou. Lásd: [NIST jelszavakra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
