---
title: Felhasználói folyamatverziók az Azure Active Directory B2C-ben | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory B2C-ben elérhető felhasználói folyamatok verzióit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185620"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Felhasználói folyamat verziói az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatai segítségével olyan közös [szabályzatokat](user-flow-overview.md) állíthat be, amelyek teljes mértékben leírják az ügyfélidentitás-élményeket. Ezek közé tartozik a regisztráció, a bejelentkezés, a jelszó alaphelyzetbe állítása vagy a profil szerkesztése. Az Azure AD B2C-ben az ajánlott felhasználói folyamatok és a felhasználói folyamatok előnézeti verziójának gyűjteményéből választhat.

Az új felhasználói folyamatok új verzióként kerülnek hozzáadásra. Ahogy a felhasználói folyamatok stabillá válnak, ajánlott lesz használni. A felhasználói folyamatok **ajánlottként** vannak megjelölve, ha alaposan tesztelték őket. A felhasználói folyamatokat a rendszer előzetes verzióban veszi figyelembe, amíg az ajánlottként meg nem jelöli. Használjon ajánlott felhasználói folyamatot bármely éles alkalmazáshoz, de válasszon más verziók közül az új funkciók teszteléséhez, amint az elérhetővé válik. Ne használja az ajánlott felhasználói folyamatok régebbi verzióit.

>[!IMPORTANT]
> Ha csak akkor azonosítja a felhasználói **folyamatot,** hogy ajánlott , akkor előzetes *verzióban*tekinthető. Csak ajánlott felhasználói folyamatokat használjon az éles alkalmazásokhoz.

## <a name="v1"></a>1. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Igen | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mail ellenőrzése után. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>Tokenkompatibilitási beállítások</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Profil szerkesztése | Igen | Lehetővé teszi, hogy a felhasználó konfigurálja felhasználói attribútumait. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés A ROPC használatával | Nem | Lehetővé teszi, hogy a helyi fiókkal rendelkező felhasználók közvetlenül jelentkezzenek be a natív alkalmazásokba (nincs szükség böngészőre). Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li></ul> |
| Bejelentkezés | Nem | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>Bejelentkezés blokkolása</li><li>Jelszó-visszaállítás kényszerítése</li><li>Folyamatosan bejelentkezve (KMSI)</ul><br>Ezzel a felhasználói folyamattal nem szabhatja testre a felhasználói felületet. |
| Regisztráció | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Igen | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjába. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>1.1-es á.

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Jelszó-visszaállítás i1.1-es | Nem | Lehetővé teszi a felhasználó számára, hogy új jelszót válasszon az e-mail ellenőrzése után (új lapelrendezés áll rendelkezésre). Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>Tokenkompatibilitási beállítások</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>2. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Jelszó-visszaállítás i 2. | Nem | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mail ellenőrzése után. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>Tokenkompatibilitási beállítások</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Profilszerkesztés v2 | Igen | Lehetővé teszi, hogy a felhasználó konfigurálja felhasználói attribútumait. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés a 2-es versenyen | Nem | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>A bejelentkezési lap testreszabása</li></ul> |
| Regisztráció v2 | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Token élettartama](tokens-overview.md)</li><li>Tokenkompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés a v2-ben | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjába. Ezzel a felhasználói folyamattal a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](custom-policy-multi-factor-authentication.md)</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[A jelszó összetettségére vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
