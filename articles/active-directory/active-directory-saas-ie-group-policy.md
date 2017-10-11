---
title: "Az Internet Explorer egy csoportházirend-objektummal Azure hozzáférési Panel bővítmény telepítése |} Microsoft Docs"
description: "Hogyan kell az Internet Explorer bővítményt a saját alkalmazások portál telepítése a csoportházirenddel."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b402ae326ab34ec71ad9de966e22be00045fee3e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>A hozzáférési Panel bővítmény telepítése az Internet Explorer csoportházirend használatával
Ez az oktatóanyag bemutatja, hogyan távoli telepítése a felhasználók gépeken az Internet Explorer a hozzáférési Panel bővítmény a csoportházirend segítségével. A bővítmény szükség az Internet Explorer felhasználók ki kell jelentkeznie az alkalmazásokat, amelyek használatával vannak konfigurálva [jelszó-alapú egyszeri bejelentkezést](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Javasoljuk, hogy a rendszergazdák a bővítmény telepítésének automatizálását. Ellenkező esetben a felhasználóknak kell töltse le és telepítse a bővítményt, amely vannak téve a felhasználói és rendszergazdai engedélyekkel kell rendelkeznie. Ez az oktatóanyag ismerteti egyik lehetséges módja automatizálása a szoftverek központi telepítése csoportházirend használatával. [További információ a csoportházirend.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A hozzáférési Panel bővítmény érhető el is [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) és [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), egyike sem telepítéséhez rendszergazdai jogosultság szükséges.

## <a name="prerequisites"></a>Előfeltételek
* Ezzel beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.
* A csoportházirend-objektum (GPO) szerkesztése "Beállítások módosítása" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai ezzel az engedéllyel rendelkeznek: a tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [Részletek](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>1. lépés: A terjesztési pont létrehozása
A telepítőcsomag először a távoli telepítéséhez a bővítményt a gépek által elérhető hálózati helyen kell elhelyezni. Ehhez kövesse az alábbi lépéseket:

1. Rendszergazdaként jelentkezzen be a kiszolgálóra
2. Az a **Kiszolgálókezelő** ablakban, keresse fel **fájl- és tárolási szolgáltatások**.
   
    ![Nyissa meg a fájl- és tárolási szolgáltatások](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Lépjen a **megosztások** fülre. Kattintson a **feladatok** > **új megosztás...**
   
    ![Nyissa meg a fájl- és tárolási szolgáltatások](./media/active-directory-saas-ie-group-policy/shares.png)
4. Fejezze be a **új megosztás varázsló** és annak érdekében, hogy az elérhető a felhasználók gépek engedélyek beállítása. [További információ a megosztásokat.](https://technet.microsoft.com/library/cc753175.aspx)
5. A következő Microsoft Windows Installer (.msi fájlt) csomag: [hozzáférési Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. Másolja a telepítőcsomag a megosztást a kívánt helyre.
   
    ![Az .msi fájlt másolja a megosztásba.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Ellenőrizze, hogy az ügyfél gépek tudja a telepítőcsomag a megosztás eléréséhez. 

## <a name="step-2-create-the-group-policy-object"></a>2. lépés: A csoportházirend-objektum létrehozása
1. Jelentkezzen be a kiszolgálóra, amelyen az Active Directory tartományi szolgáltatások (AD DS) telepítése.
2. A Kiszolgálókezelőben nyissa meg a **eszközök** > **csoportházirend-kezelő**.
   
    ![Kattintson az eszközök > csoport házirend kezelése](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. A bal oldali ablaktáblán, a **csoportházirend-kezelő** ablakban, a szervezeti egység (OU) hierarchia megtekintése, és határozza meg, melyik hatókörből szeretné alkalmazni a csoportházirend. Például úgy is dönt, hogy válassza ki a telepítendő néhány felhasználó tesztelési kis OU, vagy előfordulhat, hogy válassza ki a legfelső szintű szervezeti egység központi telepítése a teljes szervezet számára.
   
   > [!NOTE]
   > Ha azt szeretné, hozzon létre vagy szerkessze a szervezeti egységekhez (OU-k), váltson vissza a Kiszolgálókezelőhöz, és navigáljon a **eszközök** > **Active Directory – felhasználók és számítógépek**.
   > 
   > 
4. Miután kiválasztotta a szervezeti egység, a jobb gombbal kattintson rá, és válassza ki **egy csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás létrehozása itt...**
   
    ![Hozzon létre egy új csoportházirend-objektum](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. Az a **új csoportházirend-objektum** parancssort, írja be az új csoportházirend-objektum nevét.
   
    ![Az új csoportházirend-objektum neve](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Kattintson a jobb gombbal a csoportházirend-objektumot létrehozta, és válassza ki **szerkesztése**.
   
    ![Az új csoportházirend-objektum szerkesztéséhez](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>3. lépés: A telepítési csomag hozzárendelése
1. Határozza meg, hogy szeretné-e telepíteni a bővítmény alapján **számítógép konfigurációja** vagy **felhasználói konfiguráció**. Használata esetén [számítógép konfigurációja](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a bővítmény a bejelentkezett, függetlenül a számítógépen telepítve van. A [felhasználói konfiguráció](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), felhasználók kiterjesztése a telepítve vannak, függetlenül azok a számítógépek jelentkeznek be őket.
2. A bal oldali ablaktáblán, a **Csoportházirendkezelés-szerkesztő** ablakban, keresse fel a következő mappák elérési útjaiban, attól függően, hogy milyen típusú konfigurációs úgy döntött, hogy valamelyikét:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Kattintson a jobb gombbal **Szoftvertelepítés**, majd jelölje be **új** > **csomag...**
   
    ![Hozzon létre egy új telepítési csomag](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Nyissa meg a megosztott mappához, amely tartalmazza a telepítőcsomag a [1. lépés: a terjesztési pont létrehozása](#step-1-create-the-distribution-point), válassza ki az .msi fájlt, és kattintson a **nyitott**.
   
   > [!IMPORTANT]
   > Ha a megosztás ugyanazon a kiszolgálón található, győződjön meg arról, hogy Ön keresztül érik el az .msi fájl hálózati elérési útját, nem pedig a helyi fájl elérési útját.
   > 
   > 
   
    ![Válassza ki a telepítési csomagot a megosztott mappából.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. Az a **szoftver központi telepítése** rákérdezés, jelölje be **hozzárendelt** a központi telepítési módszer. Ezután kattintson az **OK** gombra.
   
    ![Válassza ki a hozzárendelt, majd kattintson az OK gombra.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

A bővítmény már telepítve van a kiválasztott szervezeti Egységhez. [További információ a Csoportházirend szoftver telepítése.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>4. lépés: Automatikus engedélyezése az Internet Explorer bővítmény
Mellett a telepítő fut, az Internet Explorer minden bővítmény explicit módon engedélyeznie kell használat előtt. A hozzáférési Panel bővítmény csoportházirenddel engedélyezéséhez az alábbi lépésekkel:

1. Az a **Csoportházirendkezelés-szerkesztő** ablakban, keresse fel vagy attól függően, hogy milyen típusú konfigurációs beállítást választja, a következő elérési [3. lépés: a telepítési csomag hozzárendelése](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Kattintson a jobb gombbal **Bővítménylista**, és válassza ki **szerkesztése**.
    ![Bővítménylista szerkesztése.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. Az a **Bővítménylista** ablakban válassza ki **engedélyezve**. Ekkor a a **beállítások** kattintson **megjelenítése...** .
   
    ![Kattintson az Engedélyezés parancsra, majd kattintson a megjelenítése...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. Az a **tartalom megjelenítése** ablak, hajtsa végre a következő lépéseket:
   
   1. Az első oszlop (a **Azonosítónév** mező), másolja és illessze be a következő osztály azonosítója:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. A második oszlopa (a **érték** mező), írja be a következő értéket:`1`
   3. Kattintson a **OK** bezárásához a **tartalom megjelenítése** ablak.
      
      ![Töltse ki a fenti értékek.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Kattintson a **OK** alkalmazza a módosításokat, és zárja be a **Bővítménylista** ablak.

A bővítmény most engedélyezni kell a gépek a kijelölt szervezeti egységen. [Engedélyezheti vagy tilthatja le a gyakori csoportházirend használatával kapcsolatos további tudnivalókért.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>5. lépés (nem kötelező): Prompt "Jelszó megjegyzése" letiltása
Ha a felhasználók jelentkezzen be a hozzáférési Panel kiterjesztéssel webhelyek, az Internet Explorer előfordulhat, hogy megjelenítése a következő megkérdezi "Szeretné tárolni a jelszó?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Ha kívánja, hogy a felhasználók ne jelent meg a kérdés, majd automatikusan hajthat végre a jelszavak tárolása megelőzése érdekében az alábbi lépésekkel:

1. Az a **Csoportházirendkezelés-szerkesztő** ablakban, keresse fel az alábbi elérési út. A konfigurációs beállítás csak érhető el a **felhasználói konfiguráció**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Találja a beállítást, nevű **kapcsolja be az automatikus kitöltés funkciót a felhasználónevek és jelszavak űrlapokon**.
   
   > [!NOTE]
   > Az Active Directory korábbi verzióiban ezt a beállítást, a nevű sorolhatja **nem teszik lehetővé az automatikus kitöltés jelszavak mentése**. A konfigurációs beállítás eltér az ebben az oktatóanyagban leírt beállítások.
   > 
   > 
   
    ![Ne felejtse el, keresse meg a felhasználói beállítások alapján.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Kattintson a jobb gombbal a fenti beállítás, és válassza ki **szerkesztése**.
4. A ablakban című **kapcsolja be az automatikus kitöltés funkciót a felhasználónevek és jelszavak űrlapokon**, jelölje be **letiltott**.
   
    ![Válassza ki a letiltása](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Kattintson a **OK** alkalmazza a módosításokat, és az ablak bezárásához.

Felhasználók már nem lesz képes tárolni a hitelesítő adatok vagy az automatikus kitöltés használatát, a korábban tárolt hitelesítő adatok eléréséhez. Azonban ez a házirend engedélyezése a felhasználók számára továbbra is használhatja az automatikus kitöltés az űrlap mezőkön, például a keresési mezők más típusú.

> [!WARNING]
> Ha a házirend engedélyezve van, miután a felhasználók úgy döntött, hogy néhány házirend ezzel hitelesítő adatok tárolására *nem* már tárolt hitelesítő adatok törlése.
> 
> 

## <a name="step-6-testing-the-deployment"></a>6. lépés: A központi telepítés tesztelése
Ha a bővítmény telepítése sikeres volt-e ellenőrizni az alábbi lépésekkel:

1. Ha használatával telepített **számítógép konfigurációja**, jelentkezzen be egy ügyfélszámítógépre, amely a kiválasztott szervezeti Egységhez tartozik [2. lépés: a csoportházirend-objektum létrehozása](#step-2-create-the-group-policy-object). Ha használatával telepített **felhasználói konfiguráció**, ügyeljen arra, hogy jelentkezzen be egy felhasználó számára, hogy a szervezeti Egységhez tartozik.
2. Modulok a teljes módosul a csoportházirend frissítése néhány bejelentkezési vehet fel a géppel. A frissítés kényszerítéséhez nyisson meg egy **parancssor** ablakot, és futtassa az alábbi parancsot:`gpupdate /force`
3. Újra kell indítania a gépet, a telepítés kerül sor. Rendszerindítási szokásos közben a bővítmény telepítése jelentősen több időt is igénybe vehet.
4. Az újraindítás után nyissa meg a **Internet Explorer**. Kattintson az ablak jobb felső sarkában **eszközök** (fogaskerék ikonra), majd válassza ki **bővítmények kezelése**.
   
    ![Kattintson az eszközök > Bővítmények kezelése](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. A a **bővítmények kezelése** ablakban ellenőrizze, hogy a **hozzáférési Panel bővítmény** telepítve van, és hogy a **állapot** értékre lett állítva **engedélyezve**.
   
    ![Győződjön meg arról, hogy a hozzáférési Panel bővítmény telepítve és engedélyezve van.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md)
* [A hozzáférési Panel bővítményét az Internet Explorer hibaelhárítása](active-directory-saas-ie-troubleshooting.md)

