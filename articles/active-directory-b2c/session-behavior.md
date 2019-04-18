---
title: Konfigurálja a munkamenet-viselkedés – Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C munkamenet-viselkedés.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1140a3cbb43e86bf222c73c95a03b11871f7a2d0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685923"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása az Azure Active Directory B2C-vel

Ez a funkció lehetővé teszi részletesebb szabályozás érdekében az egy [folyamat felhasználónkénti](active-directory-b2c-reference-policies.md), a:

- Azure AD B2C által felügyelt webes alkalmazás munkamenetek élettartamát.
- Egyszeri bejelentkezés (SSO) viselkedését több alkalmazást és az Azure AD B2C-bérlőben a felhasználói folyamatok.

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el.

Az Azure AD B2C támogatja a [OpenID Connect hitelesítési protokoll](active-directory-b2c-reference-oidc.md) biztonságos bejelentkezési webes alkalmazásokhoz való engedélyezéséhez szükséges. A következő tulajdonságok használatával webes alkalmazás munkameneteket kezelhessen:

## <a name="session-behavior-properties"></a>Munkamenet-viselkedés tulajdonságai

- **Webalkalmazás munkamenet élettartama (perc)** – a felhasználó böngészőjében a sikeres hitelesítést követően tárolt Azure AD B2C munkameneti cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimális (inkluzív) = 15 perc.
    - (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
- **Webalkalmazás munkamenet-időkorlátja** – Ha ez a kapcsoló beállítása **abszolút**, a felhasználónak kötelező a megadott időszak után újra hitelesíteni **webalkalmazás munkamenet élettartama (perc)** telik. Ha ez a kapcsoló beállítása **működés közbeni** (az alapértelmezett beállítás), a felhasználó bejelentkezve marad mindaddig, amíg a felhasználó a webalkalmazásban folyamatosan aktív.
- **Egyszeri bejelentkezés beállításainak** Ha több alkalmazás és a felhasználói folyamatok a B2C-bérlőben, felhasználói interakció érdekében kezelheti azok között használatával a **egyszeri bejelentkezési konfigurációjának** tulajdonság. A tulajdonsága a következő beállítások egyikére:
    - **Bérlő** – Ez a beállítás az alapértelmezett érték. Ezzel a beállítással lehetővé teszi, hogy több alkalmazás és a felhasználói folyamatok megosztani az ugyanazon felhasználói munkamenetet a B2C-bérlőben. Például ha egy felhasználó bejelentkezik, egy alkalmazásba, a felhasználó is zökkenőmentesen be tud jelentkezni egy másik egy, Contoso Gyógyszertári, elérésekor.
    - **Alkalmazás** – Ez a beállítás lehetővé teszi, hogy kizárólag az alkalmazáshoz, független más alkalmazásokat a felhasználói munkamenetek fenntartását. Például ha szeretné a felhasználót, hogy jelentkezzen be a Contoso Gyógyszertári (ugyanazokat a hitelesítő adatokat), akkor is, ha a felhasználó már bejelentkezett Contoso vásárlás, egy másik alkalmazás ugyanazon B2C bérlői. 
    - **A házirend** – Ez a beállítás lehetővé teszi, hogy kizárólag a felhasználói folyamat, az azt használó alkalmazások független a felhasználói munkamenetek fenntartását. Például ha a felhasználó már bejelentkezett, és egy többszörös többtényezős hitelesítés (MFA) lépés befejeződött, a felhasználónak is hozzáférést kell biztosítani több alkalmazás részei magasabb biztonsági mindaddig, amíg a felhasználói folyamat kapcsolódik a munkamenet le nem jár.
    - **Letiltott** – Ez a beállítás kényszeríti a felhasználót, hogy a szabályzat minden végrehajtás révén a teljes felhasználói folyamatot futtat.

A következő használati esetek engedélyezve vannak ezek a tulajdonságok használatával:

- Megfelel az iparági biztonsági és megfelelőségi követelményeknek a megfelelő webes alkalmazás munkamenet beállításával élettartama.
- Egy felhasználó és a magas biztonsági szintű részét a webes alkalmazás közötti interakció során beállított idő elteltével hitelesítés kényszerítése. 

## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD B2C-bérlő tartalmazó könyvtárra.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **felhasználókövetési adatai (szabályzatok)**.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot. 
6. Válassza ki **tulajdonságok**.
7. Konfigurálása **webalkalmazás munkamenet élettartama (perc)**, **webalkalmazás munkamenet-időkorlátja**, **egyszeri bejelentkezési konfigurációjának**, és **szükséges azonosító jogkivonat a kijelentkezési kérelmekben**  igény szerint.

    ![Munkamenet-viselkedés konfigurálása](./media/session-behavior/session-behavior.png)
    
8. Kattintson a **Save** (Mentés) gombra.



