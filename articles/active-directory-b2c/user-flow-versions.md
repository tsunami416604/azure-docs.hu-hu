---
title: Felhasználói folyamat verziók az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg a felhasználói folyamatok elérhető az Azure Active Directory B2C verzióit.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed57a9fa3b041961ce220e8f10d9aed5e7bef60e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511927"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói folyamat verziók

>[!IMPORTANT]
> Minden olyan felhasználói folyamatot, a cikkben szereplő tekinthető nyilvános előzetes verzióban érhető el, ha azonosította az eseményt **ajánlott**. Ajánlott felhasználókövetési adatai csak használja az éles üzemű alkalmazás számára.

Az Azure Active Directory (Azure AD) B2C felhasználói folyamatok segítségével állítsa be a közös [házirendek](active-directory-b2c-reference-policies.md) ismertetheti teljes ügyfél identitással kapcsolatos műveletet. Ezek a tapasztalatok közé tartozik a regisztrációs, bejelentkezési, jelszó-visszaállítás vagy a profil szerkesztését. Az Azure AD B2C-t javasolt felhasználókövetési adatai és a felhasználói folyamatok előzetes gyűjteménye közül választhat. 

Új felhasználói folyamatokat új verziók hozzá szeretné adni. Felhasználói folyamatok egyre stabil, akkor lesz lehet ajánlott. Felhasználói folyamatok vannak megjelölve **ajánlott** Ha, hogy alaposan tesztelt. Felhasználói folyamatok minősülnek mindaddig, amíg az ajánlott megjelölve előzetes verzióban érhető el. Éles alkalmazások ajánlott felhasználói folyamat használja, de új funkció teszteléséhez elérhetővé váló egyéb verziói közül választhat. Ne használja a javasolt felhasználói folyamatok régebbi verzióit.

## <a name="v1"></a>1. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Igen | Lehetővé teszi egy felhasználó egy új jelszót választhat az e-mailjeikhez ellenőrzése után. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profil szerkesztése | Igen | Lehetővé teszi egy felhasználó a felhasználói attribútumok konfigurálása. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li></ul> |
| erőforrás tulajdonosának | Nem | Lehetővé teszi a helyi fiókkal való bejelentkezéshez közvetlenül a natív alkalmazások (nem kötelező böngésző). Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li></ul> |
| Bejelentkezés | Nem | Lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li><li>Bejelentkezés letiltása</li><li>Jelszó-visszaállítás kényszerítése</li><li>(KMSI) bejelentkezve szeretnék maradni</ul><br>Ezzel a folyamattal a felhasználói felület nem szabhatja testre. |
| Regisztráció | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Igen | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot, vagy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>2. verzió

| Felhasználói folyamat | Ajánlott | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó kérésére vonatkozó v2 | Nem | Lehetővé teszi egy felhasználó egy új jelszót választhat az e-mailjeikhez ellenőrzése után. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Jelentkezzen a v2-ben | Nem | Lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be a fiókjába. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>Bejelentkezési oldal testreszabása</li></ul> |
| Regisztráljon a v2 | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Jogkivonat élettartama](active-directory-b2c-reference-tokens.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet-viselkedés</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés a v2-ben | Nem | Lehetővé teszi, hogy a felhasználót, hogy hozzon létre egy fiókot, vagy jelentkezzen be fiókjával. Ez a felhasználói folyamat használata esetén konfigurálhatja: <ul><li>[Többtényezős hitelesítés](active-directory-b2c-reference-mfa.md)</li><li>[Korhatáralapú](basic-age-gating.md)</li><li>[Jelszó-összetettségi követelményeknek](active-directory-b2c-reference-password-complexity.md)</li></ul> |
