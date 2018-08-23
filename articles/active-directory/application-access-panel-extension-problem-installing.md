---
title: Telepítse az alkalmazás hozzáférési panel böngészőbővítményének használatánál – Azure |} A Microsoft Docs
description: A hozzáférési panel böngészőbővítményének használatánál telepítésekor tapasztalt gyakori hibák javítása.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/18
ms.author: barbkess
ms.reviewer: japere,asteen
ms.openlocfilehash: 642f07be0eb58e4d10630bc4482d26bd00e08a94
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055118"
---
# <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngészőbővítményének használatánál telepítése

A hozzáférési panel egy olyan webes portál. Ha rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), a hozzáférési panel segítségével megtekintheti, és indítsa el a felhőalapú alkalmazások, amelyek az Azure AD-rendszergazda engedélyezte a hozzáférést. 

Ha az Azure AD-verziók használata esetén önkiszolgáló csoport- és alkalmazáskezelési lehetőségeket a hozzáférési panelen keresztül is használhatja. 

A hozzáférési panelen elkülönül az Azure Portalon. Nem igényel, hogy rendelkezik Azure-előfizetéssel.

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

A hozzáférési panel a böngészőben, amely támogatja a JavaScript szükséges minimális, és engedélyezve van a CSS. Aláírt alkalmazások jelszóalapú egyszeri bejelentkezés a hozzáférési panelen keresztül, a hozzáférési panel bővítmény telepítve van a böngészőben kell rendelkeznie. Amikor kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés automatikusan letöltődik a bővítményt.

Jelszavas egyszeri bejelentkezéshez az alábbi böngészők bármelyike használhatja:

- **Edge**: a Windows 10 Évfordulós kiadása vagy újabb. 
- **Chrome**: Windows 7-es vagy újabb, és a MacOS X rendszeren vagy újabb.
- **Firefox 26.0 vagy újabb**: a Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngészőbővítményének használatánál telepítése

A hozzáférési panel böngészőbővítményének használatánál telepítéséhez tegye a következőket:

1.  A támogatott böngészők egyikét, nyissa meg a [hozzáférési panel](https://myapps.microsoft.com), majd jelentkezzen be felhasználóként az Azure AD-fiókjával.

2.  Jelszavas egyszeri bejelentkezés alkalmazást választhat ki.

3.  Amikor a rendszer kéri, válassza ki a **telepítés most**.  
    A rendszer átirányítja a letöltési hivatkozás a kiválasztott böngésző. 
    
4.  Válassza a **Hozzáadás** lehetőséget.

5.  Ha a rendszer kéri, vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután a telepítés befejeződött, indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelen, és megtekintheti, hogy elkezdheti a jelszóalapú egyszeri bejelentkezés alkalmazásokhoz.

A bővítmény is letöltheti a Chrome és a peremhálózati közvetlenül a következő helyek:

- [Chrome-bővítményt](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge-bővítmény](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Használja a saját alkalmazások biztonságos bejelentkezési bővítménye
* Ha nem használ egy saját alkalmazások URL-cím `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
   1. Amíg nem *nem* bejelentkezett a bővítményt, kattintson a bővítmény ikonra a jobb gombbal.
   2. Válassza a menü **saját alkalmazások URL-cím**.
   3. Válassza ki az alapértelmezett URL-címe.
   4. A bővítmény ikonra.
   5. Jelentkezzen be a bővítményt, jelölje be **jelentkezzen be a kezdéshez**.

* Jelentkezzen be közvetlenül egy alkalmazást a böngészőből, tegye a következőket:
   1. Miután telepítette a bővítményt, bejelentkezni kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Jelentkezzen be az alkalmazás bejelentkezési URL-címét.  
       A bejelentkezési URL-cím általában a az URL-címét, amely a bejelentkezési képernyőn jeleníti meg.
      A bővítmény kell módosítani az állapotát, és értesíti róla, hogy rendelkezésre áll-e a jelszó.
   3. Jelentkezzen be, hogy a bővítmény ikonra.

* A bővítmény alkalmazás indításához tegye a következőket:
   1. Miután telepítette a bővítményt, bejelentkezni kiválasztásával **jelentkezzen be a kezdéshez**.
   2. A bővítmény ikonra a menü megnyitásához.
   3. Keressen rá egy alkalmazást, amely a saját alkalmazások portál érhető el.
   4. A keresési eredmények listájában válassza ki az alkalmazást.  
       Már használta az utolsó három alkalmazások megjelennek a **a legutóbb használt** helyi listát.
       
* Miközben távoli, a belső vállalati URL-címeket használnak, tegye a következőket:
    1. [Proxy konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) a bérlő
    2. [Az alkalmazás közzététele](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) és proxyn keresztül történő alkalmazás URL-címe
    3. Telepítse a bővítményt, és jelentkezzen be, bejelentkezési kiválasztásával a kezdéshez
    4. Most megnyithatja a belső vállalati URL-cím távoli közben is

> [!NOTE]
> Az előző beállítások csak az Edge, Chrome és a Firefox érhetők el.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorer

Beállíthat olyan csoportházirenddel, amely lehetővé teszi, hogy távolról telepíteni a hozzáférési panel bővítményt az Internet Explorerben a felhasználók gépein is.

A csoportházirend beállítása előtt győződjön meg arról, hogy:

-   Beállított [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.

-   A csoportházirend-objektumot (GPO) szerkesztéséhez kell *beállításainak szerkesztése* engedélyeket. Alapértelmezés szerint ezt az engedélyt a következő biztonsági csoportok tagjai számára: a tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok.

A Csoportházirend konfigurálásával, és a felhasználók számára történő telepítésével kapcsolatos részletes utasításokért lásd: [üzembe helyezése a hozzáférési panel bővítmény csoportházirend használatával az Internet Explorer](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Az Internet Explorerben a hozzáférési panel bővítmény hibaelhárítása

Hozzáférés egy diagnosztikai eszköz és az Internet Explorer a bővítmény konfigurálásáról, tekintse meg a [a hozzáférési panel bővítmény hibaelhárítása az Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Az Internet Explorer korlátozott támogatás, és többé nem kap új szoftverfrissítéseket. Edge egy ajánlott a böngészőben.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Ha a fenti lépések nem oldják meg a probléma

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója
-   Egyszerű felhasználónév (felhasználó e-mail-címe)
-   TenantID
-   Böngésző típusa
-   Időzóna és az idő vagy időkeretét, ha a hiba történt
-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md)
