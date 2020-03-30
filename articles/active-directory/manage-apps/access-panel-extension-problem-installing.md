---
title: Alkalmazás-hozzáférési panel böngészőbővítményének telepítése – Azure AD
description: Javítsa ki a hozzáférési panel böngészőbővítményének telepítésekor előforduló gyakori hibákat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275860"
---
# <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngészőbővítményének telepítése

A hozzáférési panel egy webalapú portál. Ha rendelkezik egy munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), a hozzáférési panel segítségével megtekintheti és elindíthatja azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést adott. 

Ha Azure AD-kiadásokat használ, önkiszolgáló csoport- és alkalmazáskezelési funkciókat is használhat a hozzáférési panelen keresztül. 

A hozzáférési panel elkülönül az Azure Portaltól. Nem igényel Azure-előfizetést.

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

A hozzáférési panelen legalább olyan böngészőre van szükség, amely támogatja a JavaScriptet, és engedélyezve van a CSS. Ahhoz, hogy a hozzáférési panelen jelszóalapú egyszeri bejelentkezéssel be lehessen jelentkezni az alkalmazásokba, a böngészőben telepítve kell lennie a hozzáférési panel bővítménynek. A bővítmény automatikusan letöltődik, amikor kiválaszt egy olyan alkalmazást, amely jelszóalapú egyszeri bejelentkezéshez van konfigurálva.

Jelszóalapú egyszeri bejelentkezés esetén az alábbi böngészők bármelyikét használhatja:

- **Microsoft Edge**: Windows 10 Anniversary Edition vagy újabb rendszeren. 
- **Chrome:** Windows 7 vagy újabb, valamint MacOS X vagy újabb rendszeren.
- **Firefox 26.0 vagy újabb**verzió: Windows XP SP2 vagy újabb, valamint Mac OS X 10.6 vagy újabb rendszeren.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngészőbővítményének telepítése

A hozzáférési panel böngészőbővítményének telepítéséhez tegye a következőket:

1.  A támogatott böngészők egyikében nyissa meg a [hozzáférési panelt,](https://myapps.microsoft.com)majd jelentkezzen be felhasználóként az Azure AD-fiókjában.

2.  Válasszon egy jelszóalapú egyszeri bejelentkezést tartalmazó alkalmazást.

3.  Amikor a program kéri, válassza a **Telepítés most**lehetőséget.  
    A program a kiválasztott böngésző letöltési linkjére irányítja. 
    
4.  Válassza a **Hozzáadás** lehetőséget.

5.  Ha a rendszer kéri, **engedélyezze** vagy **engedélyezze** a bővítményt.

6.  A telepítés befejezése után indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze, hogy el indíthatja-e a jelszóalapú egyszeri bejelentkezéseket.

A Chrome és a Microsoft Edge bővítményét közvetlenül a következő webhelyekről is letöltheti:

- [Chrome kiterjesztés](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge bővítmény](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox kiterjesztés](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>A Saját alkalmazások biztonságos bejelentkezési bővítményének használata
* Ha a programtól `https://myapps.microsoft.com`eltérő Saját alkalmazások URL-címet használ, állítsa be az alapértelmezett URL-címet az alábbi módon:
   1. Amíg *nincs* bejelentkezve a bővítménybe, kattintson a jobb gombbal a bővítmény ikonjára.
   2. A menüben válassza a **Saját alkalmazások URL-címét.**
   3. Válassza ki az alapértelmezett URL-címet.
   4. Válassza a bővítmény ikont.
   5. A bővítménybe való bejelentkezéshez válassza a Bejelentkezés lehetőséget **a kezdéshez.**

* Ha közvetlenül szeretne bejelentkezni egy alkalmazásba a böngészőből, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be a Bejelentkezés gombra a **kezdéshez.**
   2. Jelentkezzen be az alkalmazásba a bejelentkezési URL-címmel.  
       A bejelentkezési URL általában annak az alkalmazásnak az URL-címe, amely a bejelentkezési űrlapot jeleníti meg.
      A bővítménynek módosítania kell az állapotát, és tudatnia kell, hogy elérhető-e jelszó.
   3. A bejelentkezéshez kattintson a bővítmény ikonjára.

* Ha alkalmazást szeretne elindítani a bővítményből, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be a Bejelentkezés gombra a **kezdéshez.**
   2. A menü megnyitásához kattintson a bővítmény ikonra.
   3. Keressen egy alkalmazást, amely elérhető a Saját alkalmazások portálon.
   4. A keresési eredmények listájában jelölje ki az alkalmazást.  
       Az utoljára használt három alkalmazás megjelenik a **Legutóbb használt** parancsikonlistában.
       
* Ha a belső vállalati URL-címeket távoli körülmények között szeretné használni, tegye a következőket:
    1. [Alkalmazásproxy konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) a bérlőn
    2. [Az alkalmazás](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) és az URL közzététele az alkalmazásproxyn keresztül
    3. Telepítse a bővítményt, és jelentkezzen be a Bejelentkezés gombra a kezdéshez
    4. Most már böngészhet a belső cég URL-jét, még akkor is, ha a távoli

> [!NOTE]
> Az előző beállítások csak a Microsoft Edge, a Chrome és a Firefox esetében érhetők el.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorer programhoz

Beállíthat egy csoportházirendet, amely lehetővé teszi az Internet Explorer hozzáférési panelbővítményének távoli telepítését a felhasználók gépein.

Csoportházirend beállítása előtt győződjön meg arról, hogy:

-   Beállította az [Active Directory tartományi szolgáltatásokat,](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)és csatlakozott a felhasználók gépeihez a tartományhoz.

-   A csoportházirend-objektum szerkesztéséhez *szerkesztési beállításokkal* kell rendelkeznie. Alapértelmezés szerint ez az engedély a következő biztonsági csoportok tagjainak kap: tartományi rendszergazdák, vállalati rendszergazdák és csoportházirend-létrehozók tulajdonosai.

A csoportházirend konfigurálásáról és a felhasználóknak való üzembe helyezéséről a [Hozzáférés panelbővítmény telepítése az Internet Explorer hez csoportházirend használatával című](deploy-access-panel-browser-extension.md)témakörben talál.

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>A hozzáférési panel bővítmény hibáinak elhárítása az Internet Explorer programban

A diagnosztikai eszközökhöz való hozzáférést és a bővítmény Internet Explorer hez való konfigurálásával kapcsolatos információkat [az Internet Explorer hozzáférési panelbővítményének hibaelhárítása című](manage-access-panel-browser-extension.md)témakörében talál.

> [!NOTE]
> Az Internet Explorer korlátozott támogatással rendelkezik, és már nem kap új szoftverfrissítéseket. A Microsoft Edge az ajánlott böngésző.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Ha az előző lépések nem oldják meg a problémát

Nyisson meg egy támogatási jegyet a következő információkkal, ha az elérhető:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail cím)
-   TenantID
-   Böngésző típusa
-   Időzóna és a hiba bekövetkezésének időpontja vagy időkerete
-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](what-is-single-sign-on.md)
