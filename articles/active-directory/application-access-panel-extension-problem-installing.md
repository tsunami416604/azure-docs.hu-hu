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
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 26dc5d5ffce84206450123132c0633c2aa323e9f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>A probléma a alkalmazás-hozzáférés telepítése panel bővítmény

A hozzáférési Panel egy webes portál, amely lehetővé teszi a felhasználó, aki rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) egy megtekintheti és elindíthatja felhőalapú alkalmazásokhoz az Azure AD-rendszergazda engedélyezte őket a hozzáférést. Azure AD-verziók rendelkező felhasználó az önkiszolgáló csoportkezelési és a hozzáférési Panel az alkalmazás-felügyeleti képességeit használhatja. A hozzáférési Panel elkülönül az Azure-portálon, és nem igényli a felhasználók Azure-előfizetésre.

Jelszó-alapú egyszeri bejelentkezést (SSO) használ a hozzáférési panelen, a hozzáférési Panel bővítményt telepíteni kell a felhasználó böngészőben. A bővítmény le automatikusan, ha a felhasználó megadja egy alkalmazás, amely jelszóalapú SSO van konfigurálva.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel értekezlet böngészőre vonatkozó követelményei

A hozzáférési Panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezve van. Jelszó-alapú egyszeri bejelentkezést (SSO) használ a hozzáférési panelen, a hozzáférési Panel bővítményt telepíteni kell a felhasználó böngészőben. A bővítmény le automatikusan, ha a felhasználó megadja egy alkalmazás, amely jelszóalapú SSO van konfigurálva.

Jelszó-alapú egyszeri bejelentkezéshez a végfelhasználó böngészőkkel lehet:

-   Peremhálózati Windows 10 évforduló Edition vagy újabb 

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   Firefox 26.0 vagy újabb – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb verzió

-   Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb (korlátozott támogatás)
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

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>A személyes alkalmazások biztonságos bejelentkezési bővítmény használata?
A bővítmény saját alkalmazások alapértelmezett URL-Címének módosítása

Egy másik saját alkalmazások URL-címet mint https://myapps.microsoft.com használata majd konfigurálnia kell az alapértelmezett URL-cím, ha az alábbi lépéseket:
1. Amíg nem írta be a bővítmény **kattintson a jobb gombbal** a bővítmény ikonra.
2. Kattintson a **válassza ki a saját alkalmazások URL-cím** a menüből.
3. **Válassza ki** az alapértelmezett URL-cím.
4. Kattintson a bővítmény ikonra.
5. Jelentkezzen be a bővítmény kiválasztásával **jelentkezzen be a kezdéshez**.

Jelentkezzen be közvetlenül a böngészőből az alkalmazásba
1. A bővítmény bejelentkezés kiválasztásával a bővítmény telepítését követően **jelentkezzen be a kezdéshez**.
2. Keresse meg a **sign-on URL-cím** az alkalmazást szeretne bejelentkezni, és ezt az általában az URL-CÍMÉT, amely a bejelentkezési űrlap jeleníti meg.
3. A bővítmény kell módosította az állapotát, és lehetővé teszik, hogy tudja, hogy a jelszó érhető el, kattintson a a **bővítmény ikon** való bejelentkezéshez.

Indítsa el az alkalmazást, a bővítmény
1. A bővítmény bejelentkezés kiválasztásával a bővítmény telepítését követően **jelentkezzen be a kezdéshez**.
2. Kattintson a bővítmény ikonra kattintva nyissa meg a **menü**.
3. **Keresési** a saját alkalmazások portálon elérhető alkalmazás.
4. Kattintson a az alkalmazásból a **keresési eredmények** elindításához.
5. Elindítja az utolsó három alkalmazásokat is megjelenik a **legutóbbi** helyi listája

> [!NOTE]
> A beállítások csak az él, Chrome, Firefox érhetők el.

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorerben

A csoportházirend lehetővé tevő távoli telepítését a felhasználók gépeken az Internet Explorer a hozzáférési Panel bővítmény beállítása.

Az Előfeltételek a következők:

-   Ezzel beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.

-   A csoportházirend-objektum (GPO) szerkesztése "Beállítások módosítása" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai ezzel az engedéllyel rendelkeznek: a tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [További információk](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Az útmutató bevezeti Önt [a hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](active-directory-saas-ie-group-policy.md) részletes útmutatást a csoportházirend konfigurálásához, és telepítheti azt felhasználók számára.

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>A hozzáférési Panel bővítményét az Internet Explorer hibaelhárítása

Kövesse a [hibaelhárítása a hozzáférési Panel bővítményét az Internet Explorer](active-directory-saas-ie-troubleshooting.md) útmutatót a hozzáféréshez olyan diagnosztikai eszköz és részletes útmutatást tartalmaz az a bővítmény konfigurálása az Internet Explorer.

> [!NOTE]
> Internet Explorer korlátozottan támogatja a, és többé nem kap új szoftverfrissítéseket. Peremhálózati a javasolt böngésző.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépéseket nem oldja meg a problémát

támogatási jegy megnyitása a következő információkat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail címe)

-   A TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeretre során hiba történik.

-   Fiddler nyomkövetések

## <a name="next-steps"></a>További lépések
[Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
