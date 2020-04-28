---
title: A felhasználói folyamatok verziói a Azure Active Directory B2Cban | Microsoft Docs
description: A Azure Active Directory B2Cban elérhető felhasználói folyamatok verzióinak megismerése.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185620"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>A felhasználói folyamatok verziói Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatai segítségével olyan általános [házirendeket](user-flow-overview.md) állíthat be, amelyek teljes mértékben leírják az ügyfelek identitásának élményét. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése. Azure AD B2C az ajánlott felhasználói folyamatok gyűjteményéből és az előzetes verziójú felhasználói folyamatokból is választhat.

Az új felhasználói folyamatok új verzióként lesznek hozzáadva. Mivel a felhasználói folyamatok stabilak lesznek, használatra ajánlottak. A felhasználói folyamatok az **ajánlott** módon vannak megjelölve, ha alaposan tesztelték őket. A felhasználói folyamatok előzetes verzióban tekinthetők meg, amíg az ajánlott jelölést nem jelöli. Bármely éles alkalmazáshoz használjon ajánlott felhasználói folyamatot, de más verziók közül választhat, hogy tesztelje az új funkciókat, amint az elérhetővé válik. A javasolt felhasználói folyamatok régebbi verzióit nem ajánlott használni.

>[!IMPORTANT]
> Ha a felhasználói folyamat **ajánlottként**van azonosítva, az *előzetes*verzióban tekinthető meg. Az üzemi alkalmazások esetében csak az ajánlott felhasználói folyamatokat kell használnia.

## <a name="v1"></a>1. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Igen | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Profil szerkesztése | Igen | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés a ROPC használatával | Nem | Lehetővé teszi, hogy a felhasználó helyi fiókkal jelentkezzen be közvetlenül a natív alkalmazásokban (nincs szükség böngészőre). A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li></ul> |
| Bejelentkezés | Nem | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>Bejelentkezés letiltása</li><li>Jelszó alaphelyzetbe állításának kényszerítése</li><li>Bejelentkezés megtartása (KMSI)</ul><br>Ezzel a felhasználói folyamattal nem szabhatja testre a felhasználói felületet. |
| Regisztráció | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Igen | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>1.1-es verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Jelszó-visszaállítási v 1.1 | Nem | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után (az új lapelrendezés elérhető). A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>2. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Jelszó alaphelyzetbe állítása v2 | Nem | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Profil szerkesztése v2 | Igen | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés v2-ben | Nem | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>Bejelentkezési oldal testreszabása</li></ul> |
| Regisztráció v2 | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés v2-ben | Nem | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjával. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Korhatáralapú hozzáférés-korlátozás](basic-age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](user-flow-password-complexity.md)</li></ul> |
