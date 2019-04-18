---
title: Munkamenet és egyszeri bejelentkezés beállításainak – Azure Active Directory B2C |} A Microsoft Docs
description: Munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 674a20fc96cf5b86219222d746525a3559ae9d09
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681097"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C-vel

Ez a funkció lehetővé teszi részletesebb szabályozás érdekében az egy [folyamat felhasználónkénti](active-directory-b2c-reference-policies.md), a:

- Azure AD B2C által felügyelt webes alkalmazás munkamenetek élettartamát.
- Egyszeri bejelentkezés (SSO) viselkedését több alkalmazást és az Azure AD B2C-bérlőben a felhasználói folyamatok.

## <a name="session-behavior"></a>Munkamenet viselkedése

Az Azure AD B2C támogatja a [OpenID Connect hitelesítési protokoll](active-directory-b2c-reference-oidc.md) biztonságos bejelentkezési webes alkalmazásokhoz való engedélyezéséhez szükséges. A következő tulajdonságok használatával webes alkalmazás munkameneteket kezelhessen:

- **Webalkalmazás munkamenet élettartama (perc)** – a felhasználó böngészőjében a sikeres hitelesítést követően tárolt Azure AD B2C munkameneti cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimális (inkluzív) = 15 perc.
    - (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
- **Webalkalmazás munkamenet-időkorlátja** – Ha ez a kapcsoló beállítása **abszolút**, a felhasználónak kötelező a megadott időszak után újra hitelesíteni **webalkalmazás munkamenet élettartama (perc)** telik. Ha ez a kapcsoló beállítása **működés közbeni** (az alapértelmezett beállítás), a felhasználó bejelentkezve marad mindaddig, amíg a felhasználó a webalkalmazásban folyamatosan aktív.

A következő használati esetek engedélyezve vannak ezek a tulajdonságok használatával:

- Megfelel az iparági biztonsági és megfelelőségi követelményeknek a megfelelő webes alkalmazás munkamenet beállításával élettartama.
- Egy felhasználó és a magas biztonsági szintű részét a webes alkalmazás közötti interakció során beállított idő elteltével hitelesítés kényszerítése. 

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el.

## <a name="single-sign-on-sso-configuration"></a>Egyszeri bejelentkezés (SSO) konfigurációja

Ha több alkalmazás és a felhasználói folyamatok a B2C-bérlőben, felhasználói interakció érdekében kezelheti navigációt használ a **egyszeri bejelentkezési konfigurációjának** tulajdonság. A tulajdonsága a következő beállítások egyikére:

- **Bérlő** – Ez a beállítás az alapértelmezett érték. Ezzel a beállítással lehetővé teszi, hogy több alkalmazás és a felhasználói folyamatok megosztani az ugyanazon felhasználói munkamenetet a B2C-bérlőben. Például ha egy felhasználó bejelentkezik, egy alkalmazásba, a felhasználó is zökkenőmentesen be tud jelentkezni egy másik egy, Contoso Gyógyszertári, elérésekor.
- **Alkalmazás** – Ez a beállítás lehetővé teszi, hogy kizárólag az alkalmazáshoz, független más alkalmazásokat a felhasználói munkamenetek fenntartását. Például ha szeretné a felhasználót, hogy jelentkezzen be a Contoso Gyógyszertári (ugyanazokat a hitelesítő adatokat), akkor is, ha a felhasználó már bejelentkezett Contoso vásárlás, egy másik alkalmazás ugyanazon B2C bérlői. 
- **A házirend** – Ez a beállítás lehetővé teszi, hogy kizárólag a felhasználói folyamat, az azt használó alkalmazások független a felhasználói munkamenetek fenntartását. Például ha a felhasználó már bejelentkezett, és egy többszörös többtényezős hitelesítés (MFA) lépés befejeződött, a felhasználónak is hozzáférést kell biztosítani több alkalmazás részei magasabb biztonsági mindaddig, amíg a felhasználói folyamat kapcsolódik a munkamenet le nem jár.
- **Letiltott** – Ez a beállítás kényszeríti a felhasználót, hogy a szabályzat minden végrehajtás révén a teljes felhasználói folyamatot futtat.

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el. 

