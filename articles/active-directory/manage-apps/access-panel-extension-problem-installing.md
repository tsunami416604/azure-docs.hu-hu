---
title: Az alkalmazás-hozzáférési panel böngésző bővítményének telepítése – Azure | Microsoft Docs
description: A hozzáférési panel böngésző bővítményének telepítésekor előforduló gyakori hibák elhárítása.
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
ms.openlocfilehash: 580207bb10680e84cfda7d4b1874f2b460602973
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082165"
---
# <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

A hozzáférési panel egy webalapú portál. Ha Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókja van, a hozzáférési panelen megtekintheti és elindíthatja azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. 

Ha Azure AD-kiadásokat használ, a hozzáférési panelen is használhatja az önkiszolgáló csoportokat és az alkalmazás-felügyeleti képességeket. 

A hozzáférési panel elkülönül a Azure Portaltól. Nincs szükség Azure-előfizetésre.

## <a name="web-browser-requirements"></a>Böngészőre vonatkozó követelmények

A hozzáférési panelen legalább egy olyan böngésző szükséges, amely támogatja a JavaScript használatát, és lehetővé teszi a CSS használatát. Ahhoz, hogy a hozzáférési panelen jelszó-alapú egyszeri bejelentkezéssel lehessen bejelentkezni az alkalmazásokba, a böngészőbe telepíteni kell a hozzáférési panel bővítményt. A rendszer automatikusan letölti a bővítményt, ha olyan alkalmazást választ ki, amely jelszó alapú egyszeri bejelentkezéshez van konfigurálva.

Jelszó alapú egyszeri bejelentkezés esetén a következő böngészők bármelyikét használhatja:

- **Microsoft Edge**: Windows 10 évfordulós kiadáson vagy újabb rendszeren. 
- **Chrome**: Windows 7 vagy újabb rendszeren, valamint MacOS X vagy újabb rendszereken.
- **Firefox 26,0 vagy újabb verzió**: Windows XP SP2 vagy újabb rendszeren, valamint Mac OS X 10,6 vagy újabb rendszeren.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

A hozzáférési panel böngésző bővítményének telepítéséhez tegye a következőket:

1.  Az egyik támogatott böngészőben nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com), majd jelentkezzen be felhasználóként az Azure ad-fiókjába.

2.  Válassza ki a jelszó-alapú SSO-alkalmazást.

3.  Ha a rendszer kéri, válassza a **Telepítés most**lehetőséget.  
    A kiválasztott böngésző letöltési hivatkozására van átirányítva. 
    
4.  Válassza a **Hozzáadás** lehetőséget.

5.  Ha a rendszer kéri, **engedélyezze** **vagy engedélyezze** a bővítményt.

6.  A telepítés befejezése után indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze, hogy el tudja-e indítani a jelszó-alapú SSO-alkalmazásokat.

A Chrome és a Microsoft Edge bővítményét közvetlenül a következő helyekről töltheti le:

- [Chrome-bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-bővítmény](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox-bővítmény](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>A saját alkalmazások biztonságos bejelentkezési bővítményének használata
* Ha nem a `https://myapps.microsoft.com`a saját alkalmazások URL-címét használja, konfigurálja az alapértelmezett URL-címet a következő módon:
   1. Ha *nincs* bejelentkezve a bővítménybe, kattintson a jobb gombbal a bővítmény ikonra.
   2. A menüben válassza a **saját alkalmazások URL-cím**elemet.
   3. Válassza ki az alapértelmezett URL-címet.
   4. Válassza a bővítmény ikont.
   5. A bővítménybe való bejelentkezéshez válassza a **bejelentkezés lehetőséget a kezdéshez**.

* Ha közvetlenül a böngészőből szeretne bejelentkezni egy alkalmazásba, tegye a következőket:
   1. Miután telepítette a bővítményt, jelentkezzen be a bejelentkezés lehetőségre a **kezdéshez**.
   2. Jelentkezzen be az alkalmazásba a bejelentkezési URL-címmel.  
       A bejelentkezési URL-cím általában az alkalmazás URL-címe, amely a bejelentkezési űrlapot jeleníti meg.
      A bővítménynek meg kell változtatnia az állapotot, és tudnia kell, hogy a jelszó elérhető.
   3. A bejelentkezéshez válassza a bővítmény ikont.

* Ha egy alkalmazást a bővítményből szeretne elindítani, tegye a következőket:
   1. Miután telepítette a bővítményt, jelentkezzen be a bejelentkezés lehetőségre a **kezdéshez**.
   2. Válassza a bővítmény ikont a menü megnyitásához.
   3. Keressen egy olyan alkalmazást, amely elérhető a saját alkalmazások portálon.
   4. A keresési eredmények listájában válassza ki az alkalmazást.  
       Az utolsó három alkalmazás a **legutóbb használt** billentyűparancsok listájában jelenik meg.
       
* Ha a belső vállalati URL-címeket távoli használatra szeretné használni, tegye a következőket:
    1. [Alkalmazásproxy konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) a bérlőn
    2. [Az alkalmazás és az](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) URL-cím közzététele az Application proxyn keresztül
    3. Telepítse a bővítményt, és jelentkezzen be a bejelentkezés lehetőségre a kezdéshez.
    4. Mostantól a belső vállalati URL-címet is megnyithatja a távoli

> [!NOTE]
> Az előző beállítások csak a Microsoft Edge, a Chrome és a Firefox esetében érhetők el.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorerhez

Beállíthat egy csoportházirendet, amely lehetővé teszi, hogy távolról telepítse a hozzáférési panel bővítményt az Internet Explorer számára a felhasználói gépeken.

A csoportházirend beállítása előtt győződjön meg az alábbiakról:

-   Beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és csatlakoztatta a felhasználói gépeket a tartományhoz.

-   A Csoportházirend objektum (GPO) szerkesztéséhez *szerkesztési beállításokra* van szükség. Alapértelmezés szerint ez az engedély a következő biztonsági csoportok tagjai számára érhető el: tartományi rendszergazdák, vállalati rendszergazdák és Csoportházirend-létrehozó tulajdonosok.

A csoportházirend konfigurálásának és a felhasználók számára történő telepítésének részletes ismertetését lásd: [a hozzáférési panel bővítményének telepítése az Internet Explorerhez csoportházirend használatával](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>A hozzáférési panel kiterjesztésének hibáinak megoldása az Internet Explorerben

A diagnosztika eszközhöz való hozzáféréshez és az Internet Explorer bővítményének konfigurálásával kapcsolatos információkért lásd: [az Internet Explorer hozzáférési panel bővítményének hibáinak megoldása](manage-access-panel-browser-extension.md).

> [!NOTE]
> Az Internet Explorer korlátozott támogatást nyújt, és többé nem kap új szoftverfrissítéseket. A Microsoft Edge az ajánlott böngésző.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Ha a fenti lépések nem oldják meg a problémát

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail-cím)
-   TenantID
-   Böngésző típusa
-   Az időzóna és az idő vagy az időkeret a hiba bekövetkezésekor
-   Hegedűs nyomkövetései

## <a name="next-steps"></a>Következő lépések
[Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](what-is-single-sign-on.md)
