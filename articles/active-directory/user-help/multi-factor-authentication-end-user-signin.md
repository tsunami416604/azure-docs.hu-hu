---
title: Bejelentkezés munkahelyi vagy iskolai fiókkal történő hitelesítéssel – Azure AD
description: További információ arról, hogyan jelentkezz be munkahelyi vagy iskolai fiókodba a különböző kétfaktoros ellenőrzési módszerekkel.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064087"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Jelentkezzen be munkahelyi vagy iskolai fiókjába a kétfaktoros ellenőrzési módszerrel

> [!NOTE]
> A cikk célja, hogy egy tipikus bejelentkezési élményt. A bejelentkezéssel kapcsolatos segítségért vagy a problémák elhárításához olvassa el [Az Azure többtényezős hitelesítéssel kapcsolatos problémák at.](multi-factor-authentication-end-user-troubleshoot.md)

## <a name="what-will-your-sign-in-experience-be"></a>Mi lesz a bejelentkezési élmény?
A bejelentkezési élmény attól függően változik, hogy mit használ második tényezőként: telefonhívásként, hitelesítési alkalmazásként vagy SMS-ként. Válassza ki azt a lehetőséget, amely a legjobban leírja, hogy mit csinál:

| Hogyan lehet bejelentkezni? |
| --- |
| [Mobiltelefonra vagy irodai telefonra irányuló telefonhívással](#signing-in-with-a-phone-call) |
| [Sms-sel a mobiltelefonomra](#signing-in-with-a-text-message)
| [A Microsoft Authenticator alkalmazás értesítéseivel](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| A Microsoft Authenticator alkalmazás ellenőrző kódjaival |
| [Egy másik módszerrel, mert jelenleg nem tudom használni a kívánt módszert](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Bejelentkezés telefonhívással
Az alábbi információk a kétlépéses ellenőrzési élményt ismertetik a mobiltelefonra vagy az irodai telefonra irányuló hívással kapcsolatban.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például az Office 365-be a felhasználónevével és jelszavával.  
2. A Microsoft felhívja Önt.  
3. Vedd fel a telefont, és nyomd meg a # gombot.  

## <a name="signing-in-with-a-text-message"></a>Bejelentkezés szöveges üzenettel
Az alábbi információk a mobiltelefonra küldött szöveges üzenettel kapcsolatos kétlépéses ellenőrzési élményt ismertetik:

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például az Office 365-be a felhasználónevével és jelszavával.
2. A Microsoft egy számkódot tartalmazó szöveges üzenetet küld.
3. Írja be a kódot a bejelentkezési oldalon található mezőbe.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator alkalmazással
Az alábbi információk a Microsoft Authenticator alkalmazás kétlépéses ellenőrzésekhez való használatának élményét ismertetik. Az alkalmazás használatának két különböző módja van. Fogadhat leküldéses értesítéseket az eszközén, vagy megnyithatja az alkalmazást egy ellenőrző kód benyomásához.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator alkalmazás értesítésével
1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például az Office 365-be a felhasználónevével és jelszavával.
2. A Microsoft értesítést küld az eszközön lévő Microsoft Authenticator alkalmazásnak.

   ![A Microsoft értesítést küld](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Nyissa meg az értesítést a telefonon, és válassza az **Ellenőrzés gombot.** Ha a vállalatnak PIN-kódra van szüksége, írja be ide.
4. Most már be kell jelentkeznie.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Bejelentkezés ellenőrző kód használatával a Microsoft Authenticator alkalmazással

Ha a Microsoft Authenticator alkalmazással kap ellenőrző kódokat, akkor az alkalmazás megnyitásakor megjelenik egy szám a fiókneve alatt. Ez a szám 30 másodpercenként változik, így nem használja ugyanazt a számot kétszer. Amikor a rendszer ellenőrző kódot kér, nyissa meg az alkalmazást, és használja az aktuálisan megjelenített számot.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például az Office 365-be a felhasználónevével és jelszavával.
2. A Microsoft ellenőrző kódot kér.

   ![Ellenőrző kód megadása](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást a telefonján, és írja be a kódot a bejelentkezéshez megadott mezőbe.

## <a name="signing-in-with-an-alternate-method"></a>Bejelentkezés alternatív módszerrel
Előfordulhat, hogy nem rendelkezik a preferált ellenőrzési módszerként beállított telefonnal vagy eszközzel. Ezért javasoljuk, hogy állítson be biztonsági mentési módszereket a fiókjához. A következő szakasz bemutatja, hogyan jelentkezhet be egy másik módszerrel, ha az elsődleges módszer nem érhető el.

1. Jelentkezzen be egy alkalmazásba vagy szolgáltatásba, például az Office 365-be a felhasználónevével és jelszavával.
2. Válassza **a Másik ellenőrzési lehetőség használata lehetőséget.** A beállítások alapján különböző ellenőrzési lehetőségek jelennek meg.
3. Válasszon egy másik módszert, és jelentkezzen be.

   ![Alternatív módszer használata](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>További lépések
- Ha problémái vannak a kétlépéses ellenőrzéssel való bejelentkezéssel, további információt kaphat az [Azure többtényezős hitelesítéssel kapcsolatos problémákról.](multi-factor-authentication-end-user-troubleshoot.md)

- További információ a [kétlépéses ellenőrzési beállítások kezeléséről.](multi-factor-authentication-end-user-manage-settings.md)

- Megtudhatja, hogy miként [kezdheti el a Microsoft Authenticator alkalmazást,](user-help-auth-app-download-install.md) hogy sms-ek és telefonhívások helyett értesítéseket használhasson a bejelentkezéshez.
