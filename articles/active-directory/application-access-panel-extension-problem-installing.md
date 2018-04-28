---
title: Az alkalmazás hozzáférési panel bővítmény - Azure telepítése |} Microsoft Docs
description: A hozzáférési panel bővítmény telepítésekor előforduló gyakori hibák elhárítása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 3903e0f55e996d2ff793f17fb710843c5c64127f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel bővítmény telepítése

A hozzáférési panel egy webes portál. Ha rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD), a hozzáférési panel segítségével megtekintheti, és indítsa el a felhőalapú alkalmazásokat, amelyek egy Azure AD-rendszergazda hozzáférést adott. 

Azure AD-verziók használata, önkiszolgáló csoportkezelési és felügyeleti képességeit a hozzáférési panel keresztül is használhatja. 

A hozzáférési panel egy külön Azure-portálról. Azt nem kell Azure-előfizetésre.

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

Legalább a hozzáférési panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezve van. Aláírt alkalmazások keresztül jelszó-alapú egyszeri Bejelentkezést a hozzáférési panelen, a böngésző telepítve hozzáférési panel kiterjesztéssel kell rendelkeznie. A bővítmény le automatikusan, amikor kiválaszt egy alkalmazást, amely jelszóalapú SSO van konfigurálva.

Jelszó-alapú egyszeri Bejelentkezést az alábbi böngészők bármelyike használhatja:

- **Peremhálózati**: a Windows 10 évforduló Edition vagy újabb. 
- **Chrome**: Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb.
- **Firefox 26.0 vagy újabb**: Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel bővítmény telepítése

A hozzáférési panel bővítmény telepítéséhez tegye a következőket:

1.  A támogatott böngészőkkel egyikét, nyissa meg a [hozzáférési panel](https://myapps.microsoft.com), majd jelentkezzen be az Azure AD-fiókot egy felhasználói.

2.  Válassza ki a jelszó-alapú egyszeri bejelentkezés alkalmazásokhoz.

3.  Amikor a rendszer kéri, válassza ki a **telepítés**.  
    A letöltés hivatkozásra a kiválasztott böngésző irányítja. 
    
4.  Válassza a **Hozzáadás** lehetőséget.

5.  Ha a rendszer felszólítja, vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután a telepítés befejeződött, indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelre, és hogy elindíthatja az Egyszeri jelszó alapú alkalmazások megjelenítéséhez.

Emellett letöltheti a bővítmény Chrome és peremhálózati közvetlenül a következő helyekről:

- [Chrome-bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Peremhálózati bővítmény](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Használja a alkalmazásaimat biztonságos bejelentkezési bővítmény
* Használata saját alkalmazások URL-címe eltérő `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
   1. Miközben az *nem* jelentkezve a bővítményt, kattintson a jobb gombbal bővítmény.
   2. Válassza a menü **saját alkalmazások URL-cím**.
   3. Válassza ki az alapértelmezett URL-cím.
   4. Válassza ki a bővítmény ikonra.
   5. Jelentkezzen be a bővítményt, jelölje be **jelentkezzen be a kezdéshez**.

* Jelentkezzen be az alkalmazások számára közvetlenül a böngészőből, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be az kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Jelentkezzen be az alkalmazás és a bejelentkezési URL-CÍMÉT.  
       A bejelentkezési URL-címet az általában az URL-CÍMÉT, amely megjeleníti a bejelentkezési képernyőt.
      A bővítmény kell módosította az állapotát, és jelzi, hogy rendelkezésre áll-e jelszót.
   3. Jelentkezzen be, válassza a kiterjesztés ikonra.

* Az alkalmazás indításához a bővítményből származó, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be az kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Válassza ki a bővítmény ikonra a menü megnyitásához.
   3. Keresse meg a személyes alkalmazások portálon elérhető alkalmazás.
   4. A keresési eredmények listájában válassza ki az alkalmazást.  
       Már használta az utolsó három alkalmazások megjelennek a **legutóbbi** helyi listát.

> [!NOTE]
> Az előző beállítások csak él, Chrome és Firefox érhetők el.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorerben

Olyan csoportházirenddel, amely lehetővé teszi a távoli telepítést a hozzáférési panel bővítményét az Internet Explorer a felhasználók gépeken állíthat be.

A csoportházirend beállítása előtt győződjön meg arról, hogy:

-   Ezzel beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.

-   A csoportházirend-objektum (GPO) szerkesztéséhez rendelkeznie kell *beállításainak szerkesztése* engedélyek. Alapértelmezés szerint ezt az engedélyt a következő biztonsági csoportok tagjai: a tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok.

A csoportházirend konfigurálásához, és telepíti azokat a felhasználók számára vonatkozó részletes utasításokért lásd: [központi telepítése a hozzáférési panel bővítmény csoportházirend használatával az Internet Explorer](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>A hozzáférési panel bővítményét az Internet Explorer hibaelhárítása

A diagnosztikai eszköz és az Internet Explorer a bővítmény konfigurálásával kapcsolatos információkat a hozzáférést, lásd: [hibaelhárítása a hozzáférési panel bővítményét az Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Az Internet Explorer korlátozottan támogatja a, és többé nem kap új szoftverfrissítéseket. Peremhálózati a javasolt böngésző.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Ha az előző lépések nem megoldani a problémát

Támogatási jegy megnyitása a következő információkat, amennyiben az rendelkezésre áll:

-   Megfelelési hiba azonosítója
-   Egyszerű felhasználónév (felhasználó e-mail címe)
-   TenantID
-   Böngésző típusa
-   Időzóna és az idő vagy időkereteket, ha a hiba történt
-   Fiddler nyomkövetések

## <a name="next-steps"></a>További lépések
[Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
