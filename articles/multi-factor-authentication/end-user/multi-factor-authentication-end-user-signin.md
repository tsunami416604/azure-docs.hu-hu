---
title: "Az Azure MFA-bejelentkezés a kétlépéses ellenőrzéshez használttal |} Microsoft Docs"
description: "Ezen a lapon megtudhatja, miként, hol kell a különböző bejelentkezési módszer áll rendelkezésre az Azure MFA megjelenítéséhez."
keywords: "felhasználó hitelesítése, a bejelentkezés során tapasztal élmény, jelentkezzen be a mobiltelefon, bejelentkezés az irodai telefon"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: c47356b7b84e38a1db9259304c2a975958b1977c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>A bejelentkezés során tapasztal élmény Azure többtényezős hitelesítéssel
> [!NOTE]
> Ez a cikk célja egy szokásos bejelentkezési élmény bízná. Bejelentkezés, vagy a problémák elhárításában, lásd: [problémák adódtak a Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Mi lesz a bejelentkezés során tapasztal élmény?
A bejelentkezés során tapasztal élmény eltér attól függően, hogy mi használatát választja a második tényezőként: telefonhívást, hitelesítési alkalmazást vagy szövegét. Válassza ki a leginkább illik az Ön tevékenységeit:

| Hogyan bejelentkezik az? |
| --- |
| [Mobil vagy office telefonra telefonhívással](#signing-in-with-a-phone-call) |
| [Az SMS-t mobiltelefonomra](#signing-in-with-a-text-message)
| [Értesítések a Microsoft Authenticator alkalmazásról](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Az ellenőrző kódok kezelésére a Microsoft Authenticator alkalmazásról](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Az alternatív módszert mert jelenleg nem használható a kívánt módszer](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Telefonhívás bejelentkezni
A következő információkat a mobileszköz vagy irodai telefon hívása kétlépéses ellenőrzés élményt ismerteti.

1. Jelentkezzen be az alkalmazás vagy szolgáltatás, például az Office 365 a felhasználónévvel és jelszóval.  
2. Microsoft hívja meg.  
3. Fogadja a hívást, majd nyomja le a # gombot.  

## <a name="signing-in-with-a-text-message"></a>Bejelentkezés a szöveges üzenet
A következő információkat a mobiltelefonjára szöveges üzenetben a kétlépéses ellenőrzés élményt ismerteti:

1. Jelentkezzen be az alkalmazás vagy szolgáltatás, például az Office 365 a felhasználónévvel és jelszóval.
2. A Microsoft egy kódot tartalmazó szöveges üzenetet küld.
3. A bejelentkezési oldal megfelelő mezőbe írja be a kódot.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>A Microsoft hitelesítőalkalmazása bejelentkezni
A következő információkat a Microsoft Authenticator alkalmazással a kétlépéses ellenőrzést, élményt ismerteti. Nincsenek két különböző módon is használhatják az alkalmazást. Leküldéses értesítéseket kaphat az eszközön, illetve megnyithat ellenőrző kód kérése az alkalmazás.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás értesítéseinek bejelentkezni
1. Jelentkezzen be az alkalmazás vagy szolgáltatás, például az Office 365 a felhasználónévvel és jelszóval.
2. Microsoft értesítést küld a Microsoft Authenticator alkalmazást az eszközön.

  ![Microsoft értesítést küld.](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Nyissa meg az értesítést a a telefon, és válassza a **ellenőrizze** kulcs. Ha a vállalat a PIN-kód szükséges, írja be ide.
4. Meg kell a bejelentkezés megtörtént.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>A bejelentkezéshez egy ellenőrző kód használata a Microsoft Authenticator alkalmazást

Beolvasása az ellenőrző kódok kezelésére a Microsoft Authenticator alkalmazást használja, ha majd az alkalmazás megnyitásakor megjelenik egy számot a fiók alatt. Ez a szám 30 másodpercenként úgy módosítja, nem használhatja ugyanazt a számot kétszer. Ha az ellenőrző kódot megkérdezi, nyissa meg az alkalmazást, és használja a szám szerint jelenleg jelenik meg.

1. Jelentkezzen be az alkalmazás vagy szolgáltatás, például az Office 365 a felhasználónévvel és jelszóval.
2. A Microsoft ellenőrző kódot kér.

  ![Ellenőrzőkód megadása](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást a telefonra, és ahol most jelentkezik be a mezőbe írja be a kódot.

## <a name="signing-in-with-an-alternate-method"></a>Alternatív módszert bejelentkezni
Néha nem rendelkezik a telefonszám vagy eszközt, akkor állíthatja be az előnyben részesített ellenőrzési módszert. Ebben az esetben is, ezért azt javasoljuk, hogy beállított biztonsági mentési módszerek a fiókjához. A következő szakasz bemutatja, hogyan bejelentkezni alternatív módszert, ha az elsődleges módszer nem érhető el.

1. Jelentkezzen be az alkalmazás vagy szolgáltatás, például az Office 365 a felhasználónévvel és jelszóval.
2. Válassza ki **használja egy másik ellenőrzési módszerrel**. Megjelenik a telepítés hogy hány másik ellenőrzési lehetőséget.
3. Válasszon egy másik módszert, és jelentkezzen be.

  ![Alternatív módszert használja.](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Következő lépések

Ha bejelentkezik a kétlépéses ellenőrzést, akkor további információért [problémák adódtak a Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Megtudhatja, hogyan [kezelése a kétlépéses ellenőrzés beállításait](multi-factor-authentication-end-user-manage-settings.md).

Megtudhatja, hogyan [Ismerkedés a Microsoft Authenticator alkalmazást a](microsoft-authenticator-app-how-to.md) , hogy az értesítések használatával jelentkezzen be, a szövegek és telefonhívásokat helyett.
