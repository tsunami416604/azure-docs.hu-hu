---
title: Jelszó-összetettségi követelmények konfigurálása
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C felhasználók által szolgáltatott jelszavak bonyolultsági követelményeinek konfigurálása.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d37d1ae3871e8b14a44540883b1d03c29b58d27e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950561"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>A Azure Active Directory B2Cban található jelszavak bonyolultsági követelményeinek konfigurálása

A Azure Active Directory B2C (Azure AD B2C) a fiók létrehozásakor támogatja a végfelhasználók által megadott jelszavak összetettségi követelményeinek módosítását. Alapértelmezés szerint a Azure AD B2C `Strong` jelszavakat használ. A Azure AD B2C az ügyfelek által használható jelszavak bonyolultságának szabályozásához is támogatja a konfigurációs beállításokat.

## <a name="password-rule-enforcement"></a>Jelszavas szabály kényszerítése

A regisztráció vagy a jelszó alaphelyzetbe állítása során a végfelhasználónak meg kell adnia egy jelszót, amely megfelel az összetettségi szabályoknak. A jelszó bonyolultsági szabályait felhasználónként kell érvényesíteni. Egy felhasználói folyamatnak egy négyjegyű PIN-kódot kell megadnia a regisztráció során, míg egy másik felhasználói folyamat 8 karakterből álló karakterláncot igényel a regisztráció során. Használhat például egy olyan felhasználói folyamatot, amely különböző jelszó-bonyolultságot biztosít a felnőttek számára, mint a gyermekek számára.

A bejelentkezés során a rendszer soha nem kényszeríti ki a jelszó bonyolultságát. A rendszer a bejelentkezés során soha nem kéri a felhasználókat a jelszavuk módosítására, mert nem felel meg a jelenlegi összetettségi követelményeknek.

A jelszó bonyolultságát a következő típusú felhasználói folyamatokban lehet konfigurálni:

- Regisztrációs vagy bejelentkezési felhasználói folyamat
- Felhasználói folyamat jelszavának alaphelyzetbe állítása

Ha egyéni házirendeket használ, akkor (a[jelszó bonyolultságát egyéni szabályzatban állíthatja be](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Jelszó bonyolultságának konfigurálása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok**lehetőséget.
2. Válasszon ki egy felhasználói folyamatot, majd kattintson a **Tulajdonságok**elemre.
3. A **jelszó bonyolultsága**területen módosítsa a felhasználói folyamat jelszavának bonyolultságát **egyszerű**, **erős**vagy **Egyéni**értékre.

### <a name="comparison-chart"></a>Összehasonlító diagram

| Összetettség | Leírás |
| --- | --- |
| Egyszerű | Egy legalább 8 – 64 karakterből álló jelszó. |
| Erős | Egy legalább 8 – 64 karakterből álló jelszó. Ehhez 3 kisbetűs, nagybetűs, szám vagy szimbólum közül 3. |
| Egyéni | Ez a beállítás biztosítja a jelszó bonyolultságára vonatkozó szabályok szabályozását.  Lehetővé teszi az egyéni hossz konfigurálását.  Emellett lehetővé teszi a csak számú jelszó (PIN-kód) fogadását. |

## <a name="custom-options"></a>Egyéni beállítások

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy csak számjegyeket (PIN-ket) vagy a teljes karakterkészletet fogadja el.

- A **számok csak** számjegyeket (0-9) engedélyeznek a jelszó beírása közben.
- **Minden** betűt, számot vagy szimbólumot engedélyez.

### <a name="length"></a>Hossz

Lehetővé teszi a jelszó hosszára vonatkozó követelmények szabályozását.

- A **minimális hossznak** legalább 4-nek kell lennie.
- A **maximális hossznak** nagyobbnak vagy egyenlőnek kell lennie a minimális hosszsal, és legfeljebb 64 karakter hosszú lehet.

### <a name="character-classes"></a>Karakter osztályok

Lehetővé teszi a jelszóban használt különböző karakterkészletek szabályozását.

- **2/4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó legalább két karaktert tartalmazzon. Például egy szám és egy kisbetűs karakter.
- **3 – 4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó legalább két karaktert tartalmazzon. Például egy szám, egy kisbetűs karakter és egy nagybetűs karakter.
- **4/4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó tartalmazza az összes karaktert.

    > [!NOTE]
    > A **4 – 4** . megkövetelése végfelhasználói frusztrációt eredményezhet. Egyes tanulmányok azt mutatták, hogy ez a követelmény nem javítja a jelszó entrópiat. Lásd: a [NIST-jelszóra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
