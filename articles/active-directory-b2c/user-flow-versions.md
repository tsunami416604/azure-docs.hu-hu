---
title: Felhasználói folyamat verziók az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg a felhasználói folyamatok elérhető az Azure Active Directory B2C verzióit.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b3750768eb7346938faec7e3c1ead232923a68bd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159488"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói folyamat verziók

>[!IMPORTANT]
> Minden olyan felhasználói folyamatot, a cikkben szereplő tekinthető nyilvános előzetes verzióban érhető el, ha azonosította az eseményt **ajánlott**. Ajánlott felhasználókövetési adatai csak használja az éles üzemű alkalmazás számára.

Az Azure Active Directory (Azure AD) B2C felhasználói folyamatok segítségével állítsa be a közös [házirendek](active-directory-b2c-reference-policies.md) ismertetheti teljes ügyfél identitással kapcsolatos műveletet. Ezek a tapasztalatok közé tartozik a regisztrációs, bejelentkezési, jelszó-visszaállítás vagy a profil szerkesztését. Az Azure AD B2C-t javasolt felhasználókövetési adatai és a felhasználói folyamatok előzetes gyűjteménye közül választhat. 

Új felhasználói folyamatokat új verziók hozzá szeretné adni. Felhasználói folyamatok egyre stabil, akkor lesz lehet ajánlott. Felhasználói folyamatok vannak megjelölve **ajánlott** Ha, hogy alaposan tesztelt. Felhasználói folyamatok minősülnek mindaddig, amíg az ajánlott megjelölve előzetes verzióban érhető el. Éles alkalmazások ajánlott felhasználói folyamat használja, de új funkció teszteléséhez elérhetővé váló egyéb verziói közül választhat. Ne használja a javasolt felhasználói folyamatok régebbi verzióit.

## <a name="v1"></a>1. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Igen | Lehetővé teszi egy felhasználó egy új jelszót választhat az e-mailjeikhez ellenőrzése után. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilszerkesztés | Igen | Lehetővé teszi egy felhasználó a felhasználói attribútumok konfigurálása. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| erőforrás tulajdonosának | Nem | Lehetővé teszi a helyi fiókkal való bejelentkezéshez közvetlenül a natív alkalmazások (nem kötelező böngésző). Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li></ul> |
| Bejelentkezés | Nem | Lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>Bejelentkezés letiltása</li><li>Jelszó-visszaállítás kényszerítése</li><li>(KMSI) bejelentkezve szeretnék maradni</ul><br>Ezzel a folyamattal a felhasználói felület nem szabhatja testre. |
| Regisztráció | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Igen | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot, vagy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>2. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó kérése 2. verzió | Nem | Lehetővé teszi egy felhasználó egy új jelszót választhat az e-mailjeikhez ellenőrzése után. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Bejelentkezés 2. verzió | Nem | Lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>Bejelentkezési oldal testreszabása</li></ul> |
| Regisztráció 2. verzió | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Biztonságijogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés 2. verzió | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot, vagy jelentkezzen be fiókjával. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
