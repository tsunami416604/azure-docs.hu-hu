---
title: Az Azure MFA jelentkezzen be a kétlépéses ellenőrzés |} A Microsoft Docs
description: Ezen a lapon, útmutatást nyújtanak a, hogy hová lépjen a különböző bejelentkezési módszerekről elérhető az Azure MFA megtekintéséhez.
keywords: felhasználói hitelesítés, a bejelentkezési élmény, jelentkezzen be a mobiltelefon, jelentkezzen be az irodai telefonomra
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: aafc960c0c26a94a8be672d0d72a46dfcb559013
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157006"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication a bejelentkezési élmény
> [!NOTE]
> Ez a cikk célja egy szokásos bejelentkezési élmény bemutatására. Bejelentkezés, vagy a problémák elhárításához lásd [problémája van az Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Mi lesz a bejelentkezési élmény?
A bejelentkezési felület használatához a második tényezőként beállításoktól függően eltérő: a telefonhívás, a hitelesítési alkalmazás vagy a szövegek. Válassza ki a legjobban a mi végeznek:

| Hogyan meg bejelentkezni? |
| --- |
| [A telefonon hívja fel a mobil- vagy office telefonomra](#signing-in-with-a-phone-call) |
| [Az SMS-t kérek a mobiltelefonomra](#signing-in-with-a-text-message)
| [Az értesítések a Microsoft Authenticator alkalmazásból](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Az ellenőrző kódok kezelésére a Microsoft Authenticator alkalmazásból](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Az alternatív módszert mert jelenleg nem használható az előnyben részesített módszere](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Bejelentkezés a telefonhívás
A következő információkat a mobil- vagy office telefonjára hívás a kétlépéses ellenőrzés élményt ismerteti.

1. Jelentkezzen be egy alkalmazás vagy szolgáltatás, például az Office 365-be a felhasználónevét és jelszavát.  
2. A Microsoft hív meg.  
3. Fogadja a hívást, és nyomja le a # billentyűt.  

## <a name="signing-in-with-a-text-message"></a>Bejelentkezés az SMS-ben
A következő információkat a mobiltelefonjára szöveges üzenetben a kétlépéses ellenőrzés élményt ismerteti:

1. Jelentkezzen be egy alkalmazás vagy szolgáltatás, például az Office 365-be a felhasználónevét és jelszavát.
2. A Microsoft egy kódot tartalmazó szöveges üzenetet küld.
3. A bejelentkezési oldalon a megfelelő mezőbe írja be a kódot.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Bejelentkezés a Microsoft Authenticator alkalmazás
A következő információkat ismerteti a Microsoft Authenticator alkalmazás használatát a kétlépéses ellenőrzést. Használhatják az alkalmazást két különböző módja van. Fogadhat leküldéses értesítéseket az eszközén, vagy megnyithatja az ellenőrző kód kérése az alkalmazás.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazásból értesítést felhasználóval való bejelentkezéshez
1. Jelentkezzen be egy alkalmazás vagy szolgáltatás, például az Office 365-be a felhasználónevét és jelszavát.
2. A Microsoft értesítést küld a Microsoft Authenticator alkalmazást az eszközön.

  ![A Microsoft értesítést küld](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Nyissa meg az értesítést a telefonján és válassza ki a **ellenőrizze** kulcsot. Ha a munkahelyi PIN-kód szükséges, itt adhatja meg.
4. Meg kell a bejelentkezés megtörtént.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Jelentkezzen be egy ellenőrző kódot, a Microsoft Authenticator alkalmazás

Ha a Microsoft Authenticator alkalmazás használatával ellenőrző kódok beolvasása, majd az alkalmazás megnyitásakor megjelenik egy számot a fiók neve alatt. Ez a szám 30 másodpercenként úgy módosítja, az azonos számú kétszer nem használja. Ha kéri a rendszer az ellenőrző kódot, nyissa meg az alkalmazást, és szám látható használja.

1. Jelentkezzen be egy alkalmazás vagy szolgáltatás, például az Office 365-be a felhasználónevét és jelszavát.
2. A Microsoft egy ellenőrző kódot kér.

  ![Ellenőrzőkód megadása](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást a telefonra, és írja be a kódot a mezőbe, ahol most jelentkezik be.

## <a name="signing-in-with-an-alternate-method"></a>Bejelentkezik egy alternatív módszert
Néha nem rendelkezik a telefon vagy eszközt, akkor állíthatja be az előnyben részesített ellenőrzési módszert. Ez a helyzet van, ezért azt javasoljuk, hogy beállította a biztonsági mentési módszerek a fiókjához. A következő szakasz bemutatja, hogyan kattintva jelentkezhet be alternatív módszert, ha az elsődleges módszer nem érhető el.

1. Jelentkezzen be egy alkalmazás vagy szolgáltatás, például az Office 365-be a felhasználónevét és jelszavát.
2. Válassza ki **másik ellenőrzési lehetőség használata**. Láthatja, hogy másik ellenőrzési lehetőség, hogy hány rendelkezik a telepítő-alapú.
3. Válasszon egy másik módszert, és jelentkezzen be.

  ![Alternatív módszer](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>További lépések

Ha problémába ütközik a kétlépéses ellenőrzés jelentkezik, részletes tájékoztatást: [problémája van az Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Ismerje meg, hogyan [a kétlépéses ellenőrzés beállításait kezelheti](multi-factor-authentication-end-user-manage-settings.md).

Megtudhatja, hogyan [Ismerkedés a Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) , hogy az értesítések segítségével jelentkezzen be, a szövegek és a telefonhívások helyett.
