---
title: "A probléma a alkalmazás-hozzáférés telepítése panel bővítmény |} Microsoft Docs"
description: "Megoldásával kapcsolatban gyakran hibákat észlelt, amikor a hozzáférési panel bővítmény telepítése"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 66e002b092e02f92a33c6e498b176331ebdc190f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>A probléma a alkalmazás-hozzáférés telepítése panel bővítmény

A hozzáférési Panel egy webes portál, amely lehetővé teszi a felhasználó, aki rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) egy megtekintheti és elindíthatja felhőalapú alkalmazásokhoz az Azure AD-rendszergazda engedélyezte őket a hozzáférést. Azure AD-verziók rendelkező felhasználó az önkiszolgáló csoportkezelési és a hozzáférési Panel az alkalmazás-felügyeleti képességeit használhatja. A hozzáférési Panel elkülönül az Azure-portálon, és nem igényli a felhasználók Azure-előfizetésre.

Jelszó-alapú egyszeri bejelentkezést (SSO) használ a hozzáférési panelen, a hozzáférési Panel bővítményt telepíteni kell a felhasználó böngészőben. A bővítmény le automatikusan, ha a felhasználó megadja egy alkalmazás, amely jelszóalapú SSO van konfigurálva.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel értekezlet böngészőre vonatkozó követelményei

A hozzáférési Panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezve van. Jelszó-alapú egyszeri bejelentkezést (SSO) használ a hozzáférési panelen, a hozzáférési Panel bővítményt telepíteni kell a felhasználó böngészőben. A bővítmény le automatikusan, ha a felhasználó megadja egy alkalmazás, amely jelszóalapú SSO van konfigurálva.

Jelszó-alapú egyszeri bejelentkezéshez a végfelhasználó böngészőkkel lehet:

-   Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb

-   Peremhálózati Windows 10 évforduló Edition vagy újabb 

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   Firefox 26.0 vagy újabb – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb verzió

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel bővítmény telepítése

A hozzáférési Panel bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) valamelyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a szoftver telepítéséhez megkérdezi **telepítés**.

4.  A böngésző alapján kell irányítani a letöltési hivatkozás. **Adja hozzá** az bővítményt, hogy a böngészőben.

5.  Ha a böngésző, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Korábban telepítve, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha **indítása** a jelszó-egyszeri bejelentkezés alkalmazásokhoz

Az alábbi közvetlen hivatkozások közül a Chrome és a peremhálózati is letöltheti a bővítmény:

-   [Chrome hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Peremhálózati hozzáférési Panel bővítmény](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorerben

A csoportházirend lehetővé tevő távoli telepítését a felhasználók gépeken az Internet Explorer a hozzáférési Panel bővítmény beállítása.

Az Előfeltételek a következők:

-   Ezzel beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.

-   A csoportházirend-objektum (GPO) szerkesztése "Beállítások módosítása" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai ezzel az engedéllyel rendelkeznek: a tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [További információk](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Az útmutató bevezeti Önt [a hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](active-directory-saas-ie-group-policy.md) részletes útmutatást a csoportházirend konfigurálásához, és telepítheti azt felhasználók számára.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>A hozzáférési Panel az Internet Explorer hibaelhárítása

Kövesse a [hibaelhárítása a hozzáférési Panel bővítményét az Internet Explorer](active-directory-saas-ie-troubleshooting.md) útmutatót a hozzáféréshez olyan diagnosztikai eszköz és részletes útmutatást tartalmaz az a bővítmény konfigurálása az Internet Explorer.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépéseket nem oldja meg a problémát

támogatási jegy megnyitása a következő információkat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail címe)

-   A TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeretre során hiba történik.

-   Fiddler nyomkövetések

## <a name="next-steps"></a>Következő lépések
[Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
