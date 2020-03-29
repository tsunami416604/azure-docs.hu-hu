---
title: Az Azure Access Panel extension for IE telepítése gpo használatával | Microsoft dokumentumok
description: A csoportházirend használata az Internet Explorer bővítmény telepítéséhez a Saját alkalmazások portálhoz.
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
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807686"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Útmutató: Az Internet Explorer Access Panel bővítményének telepítése csoportházirend használatával

Ez az oktatóanyag bemutatja, hogyan telepítheti távolról az Internet Explorer Access Panel bővítményét a felhasználók gépein a csoportházirend del. Ez a bővítmény szükséges az Internet Explorer azon felhasználói számára, akiknek be kell jelentkezniük a [jelszóalapú egyszeri bejelentkezéssel](what-is-single-sign-on.md#password-based-sso)konfigurált alkalmazásokba.

Javasoljuk, hogy a rendszergazdák automatizálják a bővítmény üzembe helyezését. Ellenkező esetben a felhasználóknak maguknak kell letölteniük és telepíteniük a bővítményt, amely hajlamos a felhasználói hibákra, és rendszergazdai engedélyeket igényel. Ez az oktatóanyag a szoftvertelepítések csoportházirend használatával történő automatizálásának egyik módszerét ismerteti. [További információ a csoportházirendről.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Az Access Panel bővítmény a [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) és a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998)számára is elérhető, amelyek közül egyik sem igényel rendszergazdai engedélyeket a telepítéshez.

## <a name="prerequisites"></a>Előfeltételek

* Beállította az [Active Directory tartományi szolgáltatásokat,](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)és csatlakozott a felhasználók gépeihez a tartományhoz.
* A csoportházirend-objektum szerkesztéséhez "Beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: Tartományi rendszergazdák, Vállalati rendszergazdák és Csoportházirend-létrehozó tulajdonosok. [További információ.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>1. lépés: Az elosztási pont létrehozása

Először a telepítőcsomagot olyan hálózati helyre kell helyeznie, amelyhez a bővítményt távolról telepíteni kívánt gépek hozzáférhetnek. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a kiszolgálóra rendszergazdaként.
1. A **Kiszolgálókezelő** ablakban nyissa meg a **Fájlok és tárolószolgáltatások lehetőséget.**

    ![Fájlok és tárolási szolgáltatások megnyitása](./media/deploy-access-panel-browser-extension/files-services.png)

1. Nyissa meg a **Megosztások** lapot. Ezután kattintson **a Feladatok** > **új megosztása...**

    ![A Képernyőképen látható, hogy hol található az Új megosztás a Feladatok képernyőn](./media/deploy-access-panel-browser-extension/shares.png)

1. Töltse ki az **Új megosztás varázslót,** és állítson be engedélyeket annak biztosítására, hogy a felhasználók gépeiről elérhető legyen. [További információ a megosztásokról.](https://technet.microsoft.com/library/cc753175.aspx)
1. A következő Microsoft Windows Installer csomag letöltése (.msi fájl): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Másolja a telepítőcsomagot a megosztás kívánt helyére.

    ![Az .msi fájl másolása a megosztásra](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ellenőrizze, hogy az ügyfélgépek el tudják-e érni a telepítőcsomagot a megosztásból.

## <a name="step-2-create-the-group-policy-object"></a>2. lépés: A csoportházirend-objektum létrehozása

1. Jelentkezzen be az Active Directory tartományi szolgáltatások (AD DS) telepítését tartalmazó kiszolgálóra.
1. A Kiszolgálókezelőben nyissa meg az **Eszközök** > **csoportházirend-kezelés című**részt.

    ![Nyissa meg az Eszközök > csoportházirend-kezelés](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. A **Csoportházirend kezelése** ablak bal oldali ablaktáblájában tekintse meg a szervezeti egység (OU) hierarchiáját, és határozza meg, hogy melyik hatókörnél szeretné alkalmazni a csoportházirendet. Dönthet például úgy, hogy kiválaszt egy kis szervezeti egységet, amelyet néhány felhasználónak telepít tesztelésre, vagy választhat egy legfelső szintű szervezeti egységet a teljes szervezetre való telepítéshez.

   > [!NOTE]
   > Ha szervezeti egységeket szeretne létrehozni vagy szerkeszteni, váltson vissza a Kiszolgálókezelőre, és nyissa meg az Eszközök Active Directory – felhasználók és számítógépek > **eszközeit.** **Tools**

1. Miután kiválasztott egy szervezeti egységet, kattintson rá a jobb gombbal, és válassza **a Csoportházirend-kiszolgáló létrehozása parancsot ebben a tartományban, és itt kapcsolja össze...**

    ![A képernyőképen az Új csoportházirend-csoportházirend-szervezet létrehozása beállítás látható](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Az **Új csoportházirend-objektum** parancssorába írja be az új csoportházirend-objektum nevét.
1. Kattintson a jobb gombbal a létrehozott csoportházirend-objektumra, és válassza a **Szerkesztés parancsot.**

## <a name="step-3-assign-the-installation-package"></a>3. lépés: A telepítőcsomag hozzárendelése

1. Határozza meg, hogy a bővítményt a **Számítógép konfigurációja** vagy a **Felhasználó konfigurációja**alapján szeretné-e telepíteni. A [számítógép konfigurációjának](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)használatakor a bővítmény attól függetlenül telepítve van a számítógépen, hogy melyik felhasználó jelentkezik be. A [felhasználói konfigurációval](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)a felhasználók a bővítményt telepítették, függetlenül attól, hogy melyik számítógépre jelentkeznek be.
1. A **Csoportházirend kezelése szerkesztő** ablak bal oldali ablaktáblájában a választott konfiguráció típusától függően lépjen az alábbi mappaelérési utak valamelyikére:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Kattintson a jobb gombbal **a Szoftvertelepítés**elemre, majd válassza az **Új** > **csomag...**
1. Ugrás a telepítőcsomagot tartalmazó megosztott mappára az [1.](#step-1-create-the-distribution-point) **Open**

   > [!IMPORTANT]
   > Ha a megosztás ugyanazon a kiszolgálón található, ellenőrizze, hogy az .msi fájlt a helyi fájl elérési útja helyett a hálózati fájl elérési útján keresztül éri-e el.

    ![A telepítőcsomag kiválasztása a megosztott mappából](./media/deploy-access-panel-browser-extension/select-package.png)

1. A **Szoftver telepítése** parancsban válassza a **Deployment metódushoz rendelt** lehetőséget. Ezt követően kattintson az **OK** gombra.

A bővítmény most már telepítve van a kiválasztott szervezeti egységben. [További információ a csoportházirend szoftvertelepítéséről.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>4. lépés: A bővítmény automatikus engedélyezése az Internet Explorer programban

A telepítő futtatása mellett az Internet Explorer minden bővítményét explicit módon engedélyezni kell a használat előtt. Az alábbi lépéseket követve engedélyezze a Hozzáférési panel bővítményt csoportházirendhasználatával:

1. A **Csoportházirend kezelése szerkesztő** ablakban lépjen az alábbi elérési utak valamelyikére, attól függően, hogy milyen típusú konfigurációt választott a [3.](#step-3-assign-the-installation-package)

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Kattintson a jobb gombbal **a Bővítménylista elemre,** és válassza **a Szerkesztés parancsot.**

    ![Kattintson a jobb gombbal a "Bővítménylista" lehetőségre, és válassza a "Szerkesztés" lehetőséget](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. A **Bővítménylista ablakban** válassza az **Engedélyezve**lehetőséget. Ezután a **Beállítások** csoportban kattintson a **Megjelenítés gombra.**

    ![Kattintson az Engedélyezés, majd a Megjelenítés gombra.](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. A **Tartalom megjelenítése ablakban** hajtsa végre az alábbi lépéseket:

   1. Az első oszlophoz (az **Érték neve** mezőhöz) másolja és illessze be a következő osztályazonosítót:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. A második oszlophoz (az **Érték** mezőhöz) írja be a következő értéket:`1`
   1. A Tartalom megjelenítése ablak bezárásához kattintson az **OK** **gombra.**

      ![Az előző lépésben megadott értékek kitöltése](./media/deploy-access-panel-browser-extension/show-contents.png)

1. A módosítások alkalmazásához és a **Bővítménylista** ablak bezárásához kattintson az **OK** gombra.

A bővítményt most engedélyezni kell a kiválasztott szervezeti egységben lévő gépekhez. [További információ az Internet Explorer-bővítmények engedélyezéséhez vagy letiltásához a csoportházirend használatáról.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>5. lépés (nem kötelező): A jelszó visszamegjegyzése parancs

Amikor a felhasználók az Access Panel Extension használatával jelentkeznek be a webhelyekre, az Internet Explorer a következő kérdést jelenítheti meg: "Szeretné tárolni a jelszavát?"

![A "Szeretné tárolni a jelszavát..." Gyors](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Ha meg szeretné akadályozni, hogy a felhasználók lássák ezt a kérdést, kövesse az alábbi lépéseket, hogy az automatikus kiegészítés ne emlékezzen a jelszavakra:

1. A **Csoportházirend kezelése szerkesztő** ablakban nyissa meg az alábbi elérési utat. Ez a konfigurációs beállítás csak a **Felhasználó konfigurációja csoportban**érhető el.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Keresse meg a **Felhasználónevek és jelszavak automatikus kiegészítési szolgáltatásának bekapcsolása az űrlapokon.**

   > [!NOTE]
   > Az Active Directory korábbi verziói felsorolhatják ezt a beállítást a **Jelszavak automatikus kiegészítésének engedélyezésével.** A beállítás konfigurációja eltér az oktatóanyagban ismertetett beállítástól.

    ![Ne felejtse el megkeresni ezt a Felhasználói beállítások alatt](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Kattintson a jobb gombbal a fenti beállításra, és válassza a **Szerkesztés parancsot.**
1. Az Űrlapokon a **felhasználónevek és jelszavak automatikus kiegészítési funkciójának bekapcsolása ablakban**válassza a **Letiltva**lehetőséget.

    ![Válassza a "Letiltva" lehetőséget az automatikus kiegészítés bekapcsolása funkcióhoz](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. A módosítások alkalmazásához és az ablak bezárásához kattintson az **OK** gombra.

A felhasználók a továbbiakban nem tárolhatják hitelesítő adataikat, és nem használhatják az automatikus kiegészítést a korábban tárolt hitelesítő adatok eléréséhez. Ez a házirend azonban lehetővé teszi a felhasználók számára, hogy továbbra is automatikus kiegészítést használjanak más típusú űrlapmezőkhöz, például a keresési mezőkhöz.

> [!WARNING]
> Ha ez a házirend engedélyezve van, miután a felhasználók úgy döntöttek, hogy bizonyos hitelesítő adatokat tárolnak, ez a házirend *nem* törli a már tárolt hitelesítő adatokat.

## <a name="step-6-testing-the-deployment"></a>6. lépés: A telepítés tesztelése

Az alábbi lépéseket követve ellenőrizze, hogy a bővítmény telepítése sikeres volt-e:

1. Ha a **Számítógép-konfiguráció**használatával telepítette a rendszert, jelentkezzen be egy olyan ügyfélgépre, amely a [2.](#step-2-create-the-group-policy-object) Ha a **Felhasználó konfigurációja**használatával telepítette a telepítést, győződjön meg arról, hogy az adott szervezeti egységhez tartozó felhasználóként jelentkezik be.
1. Előfordulhat, hogy néhány bejelentkezést a csoportházirend-módosítások teljes frissítés ezzel a géppel. A frissítés kényszerítéséhez nyisson meg egy **parancssori** ablakot, és futtassa a következő parancsot:`gpupdate /force`
1. A telepítéshez újra kell indítani a gépet. A rendszerindítás a szokásosnál lényegesen több időt vehet igénybe, amíg a bővítmény telepítése megtörténik.
1. Az újraindítás után nyissa meg **az Internet Explorer t.** Az ablak jobb felső sarkában kattintson az **Eszközök** (a fogaskerék ikon) elemre, majd válassza **a Bővítmények kezelése**lehetőséget.
1. A Bővítmények kezelése ablakban ellenőrizze, hogy a **Hozzáférési panel bővítmény** telepítve **van-e,** és hogy **az állapota** Engedélyezve értékre **van-e állítva.**

   ![Annak ellenőrzése, hogy a Hozzáférési panel bővítmény telepítve van-e és engedélyezve van-e](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Részletek

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](what-is-single-sign-on.md)
* [Az Internet Explorer Hozzáférési panelbővítményének hibaelhárítása](manage-access-panel-browser-extension.md)
