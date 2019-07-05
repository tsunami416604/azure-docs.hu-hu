---
title: Az Azure hozzáférési Panel bővítmény telepítése Internet Explorer egy csoportházirend-objektummal |} A Microsoft Docs
description: Hogyan lehet az Internet Explorer-bővítmény a saját alkalmazások portál telepítése a csoportházirenddel.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf665362e2d20f26c17e8a4ae9da29fc30cb47ce
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481292"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Útmutató: A hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer

Ez az oktatóanyag bemutatja, hogyan csoportházirend használatával távolról telepíteni a felhasználók gépein az Internet Explorer a hozzáférési Panel bővítményt. A bővítmény szükséges az Internet Explorer-felhasználók, akik alkalmazásokat használatával vannak konfigurálva, be kell jelentkeznie az [jelszóalapú egyszeri bejelentkezés](what-is-single-sign-on.md#password-based-sso).

Javasoljuk, hogy a rendszergazdák automatizálható a ezt a bővítményt. Ellenkező esetben felhasználóknak kell töltse le és telepítse a bővítményt, amely magában rejtő, a felhasználói és rendszergazdai engedélyekkel kell rendelkeznie. Ebben az oktatóanyagban a szoftverfrissítési központi telepítések automatizálása a csoportházirend használatával több módszerét ismerteti. [További információ a csoportházirend.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A hozzáférési Panel bővítmény érhető el is [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) és [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ezek egyike sem szükséges telepítéséhez rendszergazdai jogosultságokkal.

## <a name="prerequisites"></a>Előfeltételek

* Beállított [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.
* A csoportházirend-objektumot (GPO) szerkesztését a "Beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek a ezt az engedélyt: A tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [Részletek](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>1\. lépés: A terjesztési pont létrehozása

Először be kell jelölnie a a telepítőcsomag a gépek távolról telepíteni a bővítményt a kívánt által elérhető hálózati helyen. Ehhez kövesse az alábbi lépéseket:

1. Bejelentkezés a kiszolgálóra rendszergazdaként.
1. Az a **Kiszolgálókezelő** ablakban lépjen az **fájl- és tárolási szolgáltatások**.

    ![Nyissa meg a fájl- és tárolási szolgáltatások](./media/deploy-access-panel-browser-extension/files-services.png)
1. Nyissa meg a **megosztások** fülre. Kattintson a **feladatok** > **új megosztás...**

    ![Nyissa meg a fájl- és tárolási szolgáltatások](./media/deploy-access-panel-browser-extension/shares.png)
1. Végezze el a **új megosztás varázsló** , és győződjön meg arról, hogy azt is gépekről érhető el a felhasználók az engedélyekkel. [További információ a megosztásokat.](https://technet.microsoft.com/library/cc753175.aspx)
1. Töltse le a következő Microsoft Windows Installer-csomag (.msi fájl): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Másolja a telepítőcsomag a kívánt helyre a megosztáson.

    ![A megosztás az .msi fájl másolása](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ellenőrizze, hogy a szerverek, a megosztást a telepítőcsomag elérhetik.

## <a name="step-2-create-the-group-policy-object"></a>2\. lépés: A csoportházirend-objektum létrehozása

1. Jelentkezzen be a kiszolgáló, amely az Active Directory Domain Services (AD DS) telepítése.
1. Nyissa meg a Server Manager **eszközök** > **csoportházirend-kezelő**.

    ![Lépjen a Tools > csoportházirend-kezelés](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. A bal oldali panelen, a **Csoportházirend kezelése** ablakban megtekintheti a szervezeti egység (OU) hierarchiában, és határozza meg, melyik hatókörben alkalmazza a csoportházirendet szeretne. Például dönthet úgy, hogy válasszon ki egy kis OU tesztelési néhány felhasználó számára üzembe, vagy előfordulhat, hogy válasszon ki egy legfelső szintű szervezeti Egységhez, az egész vállalattal való üzembe helyezéséhez.

   > [!NOTE]
   > Ha szeretne, hozzon létre vagy a szervezeti egységhez (OU) szerkesztése, váltson vissza a Kiszolgálókezelőhöz, és nyissa meg **eszközök** > **Active Directory – felhasználók és számítógépek**.

1. Miután kiválasztotta a szervezeti egység, kattintson a jobb gombbal, és válassza ki **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás létrehozása itt...**

    ![Képernyőképen látható a hozzon létre egy új csoportházirend-beállítás](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Az a **új csoportházirend-objektum** parancsmagról, írja be az új csoportházirend-objektum nevét.
1. Kattintson a jobb gombbal a csoportházirend-objektumot, amelyet létrehozott, és válassza ki **szerkesztése**.

## <a name="step-3-assign-the-installation-package"></a>3\. lépés: A telepítési csomag hozzárendelése

1. Határozza meg, hogy szeretné-e telepíteni a bővítményt alapján **számítógép konfigurációja** vagy **felhasználói konfiguráció**. Használata esetén [számítógép konfigurációja](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a bővítmény telepítve van a bejelentkezni, függetlenül a számítógépen. A [felhasználói konfiguráció](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), felhasználók rendelkeznek a bővítmény telepítve van a számukra, függetlenül attól, hogy mely számítógépek jelentkeznek be.
1. A bal oldali panelen, a **Csoportházirendkezelés-szerkesztő** ablakában nyissa meg a következő mappák elérési útja, attól függően, hogy milyen típusú konfigurációs választott valamelyikét:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
1. Kattintson a jobb gombbal **Szoftvertelepítés**, majd **új** > **csomag...**
1. Nyissa meg a megosztott mappához, amely tartalmazza a telepítőcsomag a [1. lépés: A terjesztési pont létrehozásának](#step-1-create-the-distribution-point), válassza ki az .msi fájlt, és kattintson a **nyílt**.

   > [!IMPORTANT]
   > Ha a megosztáshoz a ugyanazon a kiszolgálón található, győződjön meg arról, hogy Ön keresztül érik el az .msi fájl hálózati elérési útját, nem pedig a helyi fájl elérési útját.

    ![A megosztott mappát a telepítési csomag kiválasztása](./media/deploy-access-panel-browser-extension/select-package.png)

1. Az a **szoftver központi telepítése** kérdés, jelölje be **hozzárendelt** a központi telepítési módszer. Ezután kattintson az **OK** gombra.

A bővítmény már telepítve van a kiválasztott szervezeti Egységhez. [További információ a Csoportházirend szoftver telepítése.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>4\. lépés: A bővítmény automatikus – engedélyezése az Internet Explorer

A telepítő futtatása mellett minden az Internet Explorer-bővítmény explicit módon engedélyezni kell ahhoz, hogy kell használni. A hozzáférési Panel bővítmény csoportházirend használatával engedélyezéséhez az alábbi lépésekkel:

1. Az a **Csoportházirendkezelés-szerkesztő** ablakban lépjen az attól függően, hogy milyen típusú konfigurációs beállítást választja, a következő útvonalak egyikét [3. lépés: A telepítési csomag hozzárendelése](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
1. Kattintson a jobb gombbal **Bővítménylista**, és válassza ki **szerkesztése**.

    ![Kattintson a jobb gombbal a "Bővítménylista", és válassza a "Szerkesztés"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Az a **Bővítménylista** ablakban válassza **engedélyezve**. Ezt követően a **beállítások** területén kattintson **megjelenítése...** .

    ![Engedélyezés elemre, majd kattintson a megjelenítése...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Az a **tartalom megjelenítése** ablakban hajtsa végre az alábbi lépéseket:

   1. Az első oszlop (a **Azonosítónév** mező), másolja és illessze be a következő osztály azonosítója: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. A második oszlop (a **érték** mező), a következő értéket írja be: `1`
   3. Kattintson a **OK** gombra kattintva zárja be a **tartalom megjelenítése** ablak.

      ![Töltse ki az értékeket az előző lépésben megadott](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kattintson a **OK** a alkalmazni a módosításokat és zárja be a **Bővítménylista** ablak.

A bővítmény már engedélyezni kell a gépek a kiválasztott szervezeti egységben. [Tudjon meg többet engedélyezni vagy letiltani a gyakori csoportházirend használatával.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>5\. lépés (nem kötelező): "A jelszó ne felejtse el" üzenet letiltása

When users sign-in to websites using the Access Panel Extension, Internet Explorer may show the following prompt asking "Would you like to store your password?"

![Megjeleníti a "Szeretné tárolni a jelszavát..." parancssor](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Ha szeretné, hogy a felhasználók jelennek meg a parancssort, majd kövesse az alábbi lépéseket, hogy az automatikus kiegészítés szeret jelszavakat megjegyezni a:

1. Az a **Csoportházirendkezelés-szerkesztő** ablakban lépjen az alább felsorolt elérési úthoz. Ez a konfigurációs beállítás csak érhető el **felhasználói konfiguráció**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Keresse meg a beállítás nevű **kapcsolja be az automatikus kiegészítés funkció a felhasználónevek és jelszavak űrlapokon**.

   > [!NOTE]
   > Az Active Directory korábbi verzióiban ez a beállítás nevét listázhatja **ne engedélyezze az automatikus kiegészítés is menthetik a jelszavakat**. A konfigurációs beállítás eltér az ebben az oktatóanyagban leírt beállítással.

    ![Ne felejtse el ezt a felhasználói beállítások szakasz keresése](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Kattintson a jobb gombbal a fenti beállítás, és válassza ki **szerkesztése**.
1. Az ablakban című **kapcsolja be az automatikus kiegészítés funkció a felhasználónevek és jelszavak űrlapokon**válassza **letiltott**.

    ![Válassza a "Letiltott" lehetőséget a kapcsolja be az automatikus kiegészítés funkció](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Kattintson a **OK** alkalmazza a módosításokat és zárja be az ablakot.

Felhasználók többé nem lesz képes tárolni a hitelesítő adatok vagy az automatikus kiegészítés használata a korábban tárolt hitelesítő adatok elérésére. Azonban ez a szabályzat engedélyezése a felhasználóknak továbbra is használhatja az automatikus kiegészítés más típusú űrlap mezőit, például a keresési mezőket.

> [!WARNING]
> Ha a házirend engedélyezve van, miután a felhasználók úgy döntött, hogy egyes hitelesítő adatokat, a szabályzat ezzel tárolására *nem* törölje a jelet a már tárolt hitelesítő adatokat.

## <a name="step-6-testing-the-deployment"></a>6\. lépés: A központi telepítés tesztelése

Győződjön meg arról, ha a bővítmény telepítése sikeres volt-e az alábbi lépésekkel:

1. Ha üzembe helyezte a használatával **számítógép konfigurációja**, jelentkezzen be egy ügyfélszámítógépre, a kiválasztott szervezeti Egységhez tartozó [2. lépés: A csoportházirend-objektum létrehozása](#step-2-create-the-group-policy-object). Ha üzembe helyezte a használatával **felhasználói konfiguráció**, győződjön meg arról, hogy a szervezeti egységhez tartozó felhasználóként bejelentkezni.
1. Eltarthat néhány bejelentkezések a csoportházirend módosításainak teljes frissíteni ezt a gépet. A frissítés kényszerítéséhez nyisson meg egy **parancssor** ablakot, és futtassa az alábbi parancsot: `gpupdate /force`
1. A gép a telepítés életbe lépéséhez újra kell indítani. Rendszerindítási szokásos közben a bővítményt telepíti jelentősen több időt is igénybe vehet.
1. Nyissa meg az újraindítás után **az Internet Explorer**. Az ablak jobb felső sarkában kattintson **eszközök** (a fogaskerék ikonra), majd válassza ki **bővítmények kezelése**.
1. Az a **bővítmények kezelése** ablakban ellenőrizze, hogy a **hozzáférési Panel bővítmény** telepítve van, és hogy annak **állapota** értékre lett állítva **engedélyezve**.

   ![Győződjön meg arról, hogy a hozzáférési Panel bővítmény telepítve és engedélyezve van.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Részletek

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](what-is-single-sign-on.md)
* [Az Internet Explorer a hozzáférési Panel bővítmény hibaelhárítása](manage-access-panel-browser-extension.md)
