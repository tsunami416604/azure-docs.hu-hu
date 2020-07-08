---
title: Az Azure Access panel bővítményének üzembe helyezése az IE-ben csoportházirend-objektum használatával | Microsoft Docs
description: Az Internet Explorer bővítmény telepítése a csoportházirend használatával a saját alkalmazások portálon.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c434a2892060acfdd56c496a31e41597c21357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763431"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Útmutató: a hozzáférési panel bővítményének üzembe helyezése az Internet Explorer csoportházirend használatával

Ez az oktatóanyag bemutatja, hogyan használható a csoportházirend az Internet Explorerhez készült hozzáférési panel távoli telepítésére a felhasználói gépeken. Ez a bővítmény olyan Internet Explorer-felhasználók számára szükséges, akiknek a [jelszó-alapú egyszeri bejelentkezéssel](what-is-single-sign-on.md#password-based-sso)konfigurált alkalmazásokba kell bejelentkezniük.

Javasoljuk, hogy a rendszergazdák automatizálják ennek a bővítménynek a telepítését. Ellenkező esetben a felhasználóknak le kell tölteniük és telepíteniük kell a bővítményt, ami a felhasználói hibákra van kitéve, és rendszergazdai engedélyekkel kell rendelkeznie. Ez az oktatóanyag a szoftverek központi telepítésének a csoportházirend használatával történő automatizálásának egyik módszerét ismerteti. [További információ a csoportházirendről.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A hozzáférési panel kiterjesztése a [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) és a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998)számára is elérhető, és egyik sem igényli a telepítéshez szükséges rendszergazdai jogosultságokat.

## <a name="prerequisites"></a>Előfeltételek

* Beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és csatlakoztatta a felhasználói gépeket a tartományhoz.
* A Csoportházirend objektum (GPO) szerkesztéséhez a "beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: tartományi rendszergazdák, vállalati rendszergazdák és Csoportházirend létrehozói tulajdonosok. [Részletek](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>1. lépés: a terjesztési pont létrehozása

Először el kell helyeznie a telepítőcsomagot egy olyan hálózati helyre, amelyet azok a gépek érhetik el, amelyekhez távolról telepíteni kívánja a bővítményt. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a kiszolgálóra rendszergazdaként.
1. A **Kiszolgálókezelő** ablakban válassza a **fájlok és tárolási szolgáltatások**lehetőséget.

    ![Fájlok és tárolási szolgáltatások megnyitása](./media/deploy-access-panel-browser-extension/files-services.png)

1. Lépjen a **megosztások** lapra. Ezután kattintson a **feladatok**  >  **új megosztás...** elemre.

    ![Képernyőfelvétel: a feladatok képernyőn megjelenő új megosztás helye](./media/deploy-access-panel-browser-extension/shares.png)

1. Fejezze be az **új megosztás varázslót** , és állítson be engedélyeket annak biztosítására, hogy elérhető legyen a felhasználói gépekről. [További információ a megosztásokról.](https://technet.microsoft.com/library/cc753175.aspx)
1. Töltse le a következő Microsoft Windows Installer-csomagot (. msi fájl): [hozzáférési Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Másolja a telepítőcsomagot a megosztás kívánt helyére.

    ![Másolja az. msi fájlt a megosztásba.](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ellenőrizze, hogy az ügyfélszámítógépek képesek-e hozzáférni a telepítési csomaghoz a megosztásból.

## <a name="step-2-create-the-group-policy-object"></a>2. lépés: a csoportházirend-objektum létrehozása

1. Jelentkezzen be a Active Directory tartományi szolgáltatások (AD DS) telepítést futtató kiszolgálóra.
1. A Kiszolgálókezelőben lépjen az **eszközök**  >  **csoportházirend felügyelet**elemre.

    ![Válassza az eszközök > Csoportházirend felügyelet](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. A **csoportházirend felügyeleti** ablak bal oldali ablaktábláján tekintse meg a szervezeti egység (OU) hierarchiáját, és határozza meg, hogy melyik hatókörben szeretné alkalmazni a csoportházirendet. Dönthet például úgy, hogy egy kis szervezeti egységet szeretne üzembe helyezni néhány felhasználónak a teszteléshez, vagy ha egy legfelső szintű szervezeti egységet szeretne üzembe helyezni a teljes szervezet számára.

   > [!NOTE]
   > Ha szeretné létrehozni vagy szerkeszteni a szervezeti egységeket (OU-ket), váltson vissza a Kiszolgálókezelő elemre, és válassza az **eszközök**  >  **Active Directory felhasználók és számítógépek**lehetőséget.

1. Miután kiválasztott egy szervezeti egységet, kattintson rá a jobb gombbal, és válassza a **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt..** . lehetőséget.

    ![Képernyőfelvétel az új csoportházirend-objektum létrehozása lehetőségről](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Az **új csoportházirend** -objektum parancssorában adja meg az új csoportházirend objektum nevét.
1. Kattintson a jobb gombbal a létrehozott Csoportházirend objektumra, majd válassza a **Szerkesztés**lehetőséget.

## <a name="step-3-assign-the-installation-package"></a>3. lépés: a telepítési csomag kiosztása

1. Döntse el, hogy a bővítményt a **Számítógép konfigurációja** vagy a **Felhasználó konfigurációja**alapján szeretné-e telepíteni. Számítógép- [konfiguráció](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)használatakor a bővítmény telepítve van a számítógépen, függetlenül attól, hogy mely felhasználók jelentkeznek be. A [Felhasználó konfigurációja](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)esetében a felhasználóknak telepítve kell lennie a bővítménynek, függetlenül attól, hogy mely számítógépeken jelentkeznek be.
1. A **csoportházirend-felügyeleti szerkesztő** ablak bal oldali ablaktábláján válassza a következő elérési utak egyikét, attól függően, hogy milyen típusú konfigurációt választott:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Kattintson a jobb gombbal a **Szoftvertelepítés**elemre, majd válassza az **új**csomag lehetőséget.  >  **..**
1. Nyissa meg a telepítőcsomagot tartalmazó megosztott mappát az 1. [lépés: hozza létre a terjesztési pontot](#step-1-create-the-distribution-point), válassza ki az. msi fájlt, és kattintson a **Megnyitás**gombra.

   > [!IMPORTANT]
   > Ha a megosztás ugyanazon a kiszolgálón található, győződjön meg arról, hogy a hálózati fájl elérési útján keresztül éri el az. msi fájlt a helyi fájl elérési útja helyett.

    ![Válassza ki a telepítőcsomagot a megosztott mappából.](./media/deploy-access-panel-browser-extension/select-package.png)

1. A **szoftver központi telepítése** parancssorban válassza a **hozzárendelve** lehetőséget a telepítési módszerhez. Ezt követően kattintson az **OK** gombra.

A bővítmény mostantól a kiválasztott szervezeti egységre van telepítve. [További információ a Csoportházirend-alapú szoftvertelepítésról.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>4. lépés: az Internet Explorer bővítményének automatikus engedélyezése

A telepítő futtatása mellett az Internet Explorer minden bővítményét explicit módon engedélyezni kell, hogy használni lehessen. Az alábbi lépéseket követve engedélyezheti a hozzáférési panel bővítményt a csoportházirend használatával:

1. A **csoportházirend-felügyeleti szerkesztő** ablakban válassza a következő elérési utak egyikét, attól függően, hogy milyen típusú konfigurációt választott a [3. lépés: a telepítési csomag hozzárendeléséhez](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Kattintson a jobb gombbal **a bővítmény listára**, és válassza a **Szerkesztés**lehetőséget.

    ![Kattintson a jobb gombbal a "bővítmények" listára, és válassza a "szerkesztés" lehetőséget.](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. A **bővítmény lista** ablakban válassza az **engedélyezve**lehetőséget. Ezután a **Beállítások** szakaszban kattintson a **Megjelenítés...** elemre.

    ![Kattintson az engedélyezés, majd a megjelenítés... elemre.](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. A **tartalom megjelenítése** ablakban hajtsa végre a következő lépéseket:

   1. Az első oszlophoz (az **érték neve** mezőhöz) másolja és illessze be a következő OSZTÁLYAZONOSÍTÓ-azonosítót:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. A második oszlophoz (az **érték** mezőhöz) írja be a következő értéket:`1`
   1. A **tartalom megjelenítése** ablak bezárásához kattintson **az OK** gombra.

      ![Adja meg az előző lépésben megadott értékeket](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kattintson **az OK** gombra a módosítások alkalmazásához és a **kiegészítő lista** ablak bezárásához.

A bővítményt most engedélyezni kell a kiválasztott szervezeti egységben lévő gépekhez. [További információ a csoportházirend használatáról az Internet Explorer bővítményeinek engedélyezéséhez vagy letiltásához.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>5. lépés (nem kötelező): "jelszó megjegyzése" kérés letiltása

Amikor a felhasználók a hozzáférési panel bővítmény használatával jelentkeznek be a webhelyekre, az Internet Explorer a következő promptot jeleníti meg: "szeretné tárolni a jelszavát?"

![A következőt jeleníti meg: "szeretné tárolni a jelszavát..." gyors](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Ha meg szeretné akadályozni, hogy a felhasználók lássák ezt a kérdést, kövesse az alábbi lépéseket, hogy megakadályozza a jelszavak megjegyzésének automatikus befejeződését:

1. Az **csoportházirend-felügyeleti szerkesztő** ablakban lépjen az alább látható elérési útra. Ez a konfigurációs beállítás csak a **felhasználói konfiguráció**területen érhető el.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Keresse meg az **űrlapon a felhasználónevek és jelszavak automatikus kiegészítése funkció bekapcsolása**beállítást.

   > [!NOTE]
   > A Active Directory korábbi verzióiban előfordulhat, hogy ez a beállítás **nem teszi lehetővé az automatikus kiegészítést a jelszavak mentéséhez**. Az adott beállítás konfigurációja eltér az oktatóanyagban ismertetett beállításoktól.

    ![Ne felejtse el megkeresni ezt a felhasználói beállítások alatt](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Kattintson a jobb gombbal a fenti beállításra, majd válassza a **Szerkesztés**lehetőséget.
1. Az **űrlapon lévő felhasználónevek és jelszavak automatikus kiegészítése funkció bekapcsolása**ablakban válassza a **Letiltva**lehetőséget.

    ![Válassza ki a "Letiltva" lehetőséget az automatikus kiegészítés bekapcsolása funkcióhoz](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. A módosítások alkalmazásához és az ablak bezárásához kattintson **az OK** gombra.

A felhasználók többé nem tudják tárolni a hitelesítő adataikat, vagy az automatikus kiegészítés használatával érhetik el a korábban tárolt hitelesítő adatokat. Ez a szabályzat azonban lehetővé teszi, hogy a felhasználók továbbra is használják az automatikus kiegészítést más típusú űrlapmezők, például a keresési mezők esetében.

> [!WARNING]
> Ha a házirend engedélyezve van, miután a felhasználók bizonyos hitelesítő adatokat tárolnak, akkor ez a szabályzat *nem* törli a már tárolt hitelesítő adatokat.

## <a name="step-6-testing-the-deployment"></a>6. lépés: a központi telepítés tesztelése

Az alábbi lépéseket követve ellenőrizheti, hogy a bővítmény központi telepítése sikeres volt-e:

1. Ha **számítógép-konfiguráció**használatával telepítette a rendszert, jelentkezzen be egy olyan ügyfélszámítógépre, amely a 2. lépésben kiválasztott szervezeti egységhez tartozik, és [hozza létre a csoportházirend objektumot](#step-2-create-the-group-policy-object). Ha a **felhasználói konfiguráció**használatával telepítette, ügyeljen arra, hogy az adott szervezeti egységhez tartozó felhasználóként jelentkezzen be.
1. Előfordulhat, hogy a csoportházirend módosításainak egy pár bejelentkezést is igénybe vehet a gép teljes frissítéséhez. A frissítés kényszerítéséhez nyisson meg egy **parancssori** ablakot, és futtassa a következő parancsot:`gpupdate /force`
1. A telepítés megkezdéséhez újra kell indítania a számítógépet. A rendszerindítási szolgáltatás a szokásosnál jóval több időt vehet igénybe a bővítmény telepítésekor.
1. Az újraindítás után nyissa meg az **Internet Explorert**. Az ablak jobb felső sarkában kattintson az **eszközök** elemre (a fogaskerék ikonra), majd válassza a **Bővítmények kezelése**lehetőséget.
1. A **Bővítmények kezelése** ablakban ellenőrizze, hogy a **hozzáférési panel bővítmény** telepítve van-e, és hogy az **állapota** **engedélyezve**értékre van-e állítva.

   ![Annak ellenőrzése, hogy a hozzáférési panel bővítmény telepítve és engedélyezve van-e](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Tudjon meg többet

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés Azure Active Directory](what-is-single-sign-on.md)
* [Az Internet Explorerhez készült hozzáférési panel bővítmény hibaelhárítása](manage-access-panel-browser-extension.md)
