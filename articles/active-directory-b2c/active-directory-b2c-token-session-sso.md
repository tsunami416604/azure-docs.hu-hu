---
title: Munkamenet és egyszeri bejelentkezés konfigurálása
titleSuffix: Azure AD B2C
description: Munkamenet és egyszeri bejelentkezés (SSO) konfigurációja Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950068"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Munkamenet és egyszeri bejelentkezés konfigurálása Azure Active Directory B2C

Ez a szolgáltatás részletesen szabályozható vezérlést tesz lehetővé a következő [módon](active-directory-b2c-reference-policies.md):

- A Azure AD B2C által felügyelt webalkalmazás-munkamenetek élettartama.
- Az egyszeri bejelentkezés (SSO) viselkedése több alkalmazás és felhasználói folyamat között a Azure AD B2C-bérlőben.

## <a name="session-behavior"></a>Munkamenet viselkedése

Azure AD B2C támogatja az [OpenID Connect hitelesítési protokollt](active-directory-b2c-reference-oidc.md) a biztonságos bejelentkezés webalkalmazásokhoz való engedélyezéséhez. A webalkalmazás-munkamenetek kezeléséhez a következő tulajdonságokat használhatja:

- **Webalkalmazás-munkamenet élettartama (perc)** – sikeres hitelesítés után a felhasználó böngészőjében tárolt Azure ad B2C's-munkamenet-cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimum (inkluzív) = 15 perc.
    - Maximum (inkluzív) = 1440 perc.
- **Webalkalmazás-munkamenet időkorlátja** – ha ez a kapcsoló **abszolút**értékre van állítva, a rendszer a **webalkalmazás-munkamenet élettartama (perc)** által megadott időszak elteltével újra hitelesíti a felhasználót. Ha a kapcsoló a **működés közbeni** (alapértelmezett beállítás) értékre van állítva, a felhasználó mindaddig bejelentkezett marad, amíg a felhasználó folyamatosan aktív a webalkalmazásban.

A következő használati esetek engedélyezettek a következő tulajdonságok használatával:

- Az iparág biztonsági és megfelelőségi követelményeinek kielégítése a webalkalmazás-munkamenetek megfelelő élettartamának beállításával.
- A hitelesítés kényszerítése egy beállított időszak után a felhasználó interakciójában a webalkalmazás magas biztonsági részével.

Ezek a beállítások nem érhetők el a felhasználói folyamatok jelszavának alaphelyzetbe állításához.

## <a name="single-sign-on-sso-configuration"></a>Egyszeri bejelentkezés (SSO) konfigurációja

Ha több alkalmazással és felhasználói folyamattal rendelkezik a B2C-bérlőben, akkor az **egyszeri bejelentkezési konfigurációs** tulajdonsággal kezelheti a felhasználók interakcióit. A tulajdonságot a következő beállítások egyikére állíthatja be:

- **Bérlő** – ez a beállítás az alapértelmezett érték. Ezzel a beállítással a B2C-bérlőben több alkalmazás és felhasználói folyamat is megoszthatja ugyanazt a felhasználói munkamenetet. Ha például egy felhasználó bejelentkezik egy alkalmazásba, a felhasználó zökkenőmentesen bejelentkezhet egy másik, contoso gyógyszertárba, amikor hozzáfér.
- **Alkalmazás** – ez a beállítás lehetővé teszi, hogy a felhasználói munkamenetet kizárólag egy alkalmazáshoz, más alkalmazásoktól függetlenül kezelje. Ha például azt szeretné, hogy a felhasználó bejelentkezzen a contoso gyógyszertárba (ugyanazzal a hitelesítő adatokkal), akkor is, ha a felhasználó már be van jelentkezve a contoso Shoppingba, egy másik alkalmazás ugyanazon B2C-bérlőn.
- **Házirend** – ez a beállítás lehetővé teszi a felhasználói munkamenetek kizárólag felhasználói folyamatokhoz való fenntartását, az azt használó alkalmazásoktól függetlenül. Ha például a felhasználó már bejelentkezett, és elvégezte a többtényezős hitelesítés (MFA) lépését, a felhasználó több alkalmazás nagyobb biztonságú részeihez is hozzáférhet, ha a munkamenet a felhasználói folyamathoz kötődik, nem jár le.
- **Letiltva** – ez a beállítás arra kényszeríti a felhasználót, hogy a teljes felhasználói folyamaton keresztül fusson a szabályzat minden egyes végrehajtásán.

Ezek a beállítások nem érhetők el a felhasználói folyamatok jelszavának alaphelyzetbe állításához.

