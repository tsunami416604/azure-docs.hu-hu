---
title: "A StorSimple eszközök felügyeletére PowerShell |} Microsoft Docs"
description: "Ismerje meg, hogyan használható a Windows PowerShell-lel a StorSimple eszköz kezeléséhez."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/03/2017
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 89e1054117f19e787da5330932021351fb016209
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Az eszköz felügyelete a Windows PowerShell-lel segítségével

## <a name="overview"></a>Áttekintés

A Windows PowerShell-lel egy parancssori felületet biztosít, amely segítségével kezelheti a Microsoft Azure StorSimple eszközt. A nevet javasol, mivel a Windows PowerShell-alapú, parancssori felület egy korlátozott futási térrel részét képező is. A parancssorban a felhasználó szemszögéből egy korlátozott futási térrel egy Windows PowerShell korlátozott verziója jelenik meg. Néhány alapvető funkciót, a Windows PowerShell megőrzésével Ez az interfész rendelkezik kezelése a Microsoft Azure StorSimple eszköz körétől további dedikált parancsmagokat.

Ez a cikk ismerteti a StorSimple funkciót, beleértve a hogyan kapcsolódhat a felület a Windows PowerShell, és részletes eljárásokat vagy hajthat végre a kapcsolat használatával munkafolyamatok mutató hivatkozásokat tartalmaz. A munkafolyamatok venni, hogyan lehet regisztrálni az eszközt, a hálózati kapcsolat konfigurálása az eszközön, a karbantartási módban, módosíthatja az eszköz állapotát, és bármely problémák léphetnek fel az eszköz igénylő frissítések telepítése.

A cikk elolvasása után fogja tudni:

* A StorSimple eszközt a StorSimple Windows PowerShell használatával kapcsolódni.
* A StorSimple eszközt a StorSimple Windows PowerShell segítségével felügyelheti.
* Segítség a Windows PowerShellben a StorSimple.

> [!NOTE]
> * StorSimple-parancsmagokhoz tartozó Windows PowerShell lehetővé teszik a StorSimple eszköz kezelését, soros konzolon vagy a Windows PowerShell távoli eljáráshívás keresztül távolról. További információ az egyes használható ezen a felületen egyes parancsmagok Ugrás [parancsmag-referencia a Windows PowerShell-lel](https://technet.microsoft.com/library/dn688168.aspx).
> * Az Azure PowerShell StorSimple parancsmagok olyan parancsmagokat, amelyek lehetővé teszik, hogy automatizálja a StorSimple szolgáltatásiszint- és áttelepítési feladatok a parancssorból egy másik gyűjteményt. A StorSimple a Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [Azure StorSimple parancsmag-referencia](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


A Windows PowerShell lel az alábbi módszerek egyikének használatával végezheti el:

* [A StorSimple eszköz soros konzoljához való csatlakozáshoz](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Távoli csatlakozás a StorSimple Windows PowerShell használatával](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>A Windows PowerShell csatlakoztatni a StorSimple eszköz soros konzolon keresztül

Is [töltse le a PuTTY](http://www.putty.org/) vagy hasonló Windows PowerShell csatlakoztatni a StorSimple terminálemulációs szoftverre. Szeretne beállítani a PuTTY kifejezetten a Microsoft Azure StorSimple eszköz elérésére. A következő témakörök tartalmaznak részletes lépéseket a PuTTy konfigurálását, és csatlakozzon az eszközhöz. A soros konzol különböző menüpontok is ismerteti.

### <a name="putty-settings"></a>PuTTY-beállítások

Győződjön meg arról, hogy használja-e a következő PuTTY beállításokat csatlakozni a Windows PowerShell felületet a soros konzolból.

#### <a name="to-configure-putty"></a>A PuTTY konfigurálása

1. A PuTTY a **újrakonfigurálás** párbeszédpanel a **kategória** ablaktáblán válassza előbb **billentyűzet**.
2. Győződjön meg arról, hogy a következő beállítások vannak-e jelölve (ezek azok az alapértelmezett beállításokat új munkamenet indítása).
   
   | Billentyűzet elem | Válassza ezt: |
   | --- | --- |
   | BACKSPACE kulcs |Ellenőrzés-? (127) |
   | Otthoni és a záró kulcsok |Standard |
   | Funkcióbillentyűk és billentyűzet |ESC [n ~ |
   | A kurzor kulcsok kezdeti állapot |Normál |
   | Számbillentyűzeten kezdeti állapota |Normál |
   | Egyéb billentyűzet szolgáltatások engedélyezése |Ellenőrzés-Alt AltGr eltér |
   
    ![Támogatott Putty-beállítások](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kattintson az **Alkalmaz** gombra.
4. Az a **kategória** ablaktáblán válassza előbb **fordítási**.
5. Az a **távoli karakterkészlet** listáján jelölje ki **UTF-8**.
6. A **sor rajz karakterek kezelését**, jelölje be **használata Unicode sor rajz kódpontokat**. Az alábbi képernyőfelvételen látható a PuTTY megfelelő beállításokat.
   
    ![UTF Putty-beállítások](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kattintson az **Alkalmaz** gombra.

A PuTTY segítségével mostantól a következő lépések végrehajtásával az eszköz soros konzoljához való csatlakozáshoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>A soros konzol bemutatása

Amikor hozzáfér a Windows PowerShell felületet a StorSimple eszköz a soros konzolon keresztül, egy szalagcím üzenet számára jelenik meg, kiegészítve a menüpont.

A szalagcím üzenet StorSimple eszköz alapvető információkat, például a modell, a nevét, a telepített szoftverek verziója és a fér hozzá a tartományvezérlő állapotát tartalmazza. Az alábbi ábrán egy példa egy szalagcím üzenetet.

![Soros szalagcím üzenet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> A szalagcím üzenet segítségével alapján megállapítható, hogy a vezérlő kapcsolódik _aktív_ vagy _passzív_.

A következő kép bemutatja a különböző futási térben lehetőségek a soros konzol menüben elérhető.

![Regisztrálja az eszközt a 2. régiója](./media/storsimple-windows-powershell-administration/IC740906.png)

A következő beállítások közül választhat:

1. **Jelentkezzen be a teljes körű hozzáférési** ezzel a beállítással kapcsolódhat (a szükséges hitelesítő adatokat) a **SSAdminConsole** futási térben a helyi tartományvezérlőn. (A helyi tartományvezérlő a tartományvezérlő, amely jelenleg fér hozzá a StorSimple eszköz soros konzolon keresztül). Ez a beállítás engedélyezése a Microsoft Support nem korlátozott futási teret (a támogatási munkamenet) bármely lehetséges problémák elérésére is használható. 1. lehetőség használatával jelentkezzen be, miután engedélyezheti a Microsoft Support engineer nem korlátozott futási térrel hozzáférni egy adott parancsmag futtatásával. További információkért tekintse meg [támogatási munkamenet indításához](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Jelentkezzen be a partner tartományvezérlő teljes hozzáféréssel rendelkező** Ez a beállítás megegyezik a 1. lehetőség – kapcsolódás (a szükséges hitelesítő adatokat), azonban a **SSAdminConsole** futási térben a társ vezérlőn. Mivel a StorSimple eszköz aktív-passzív konfigurációban két vezérlő egy magas rendelkezésre állású eszköz, társ hivatkozik a többi tartományvezérlőre, az eszköz soros konzolon keresztül elérő).
   1. lehetőség hasonló, ezt a lehetőséget is segítségével engedélyezése a Microsoft Support egy partner-tartományvezérlő nem korlátozott futási térrel eléréséhez.

3. **Korlátozott hozzáférésű csatlakozás** ezzel a beállítással Windows PowerShell felületet, korlátozott módban eléréséhez. Nem kéri a hozzáférési hitelesítő adatokat. Ezt a lehetőséget a beállítások 1 és 2 képest több korlátozott futási térrel kapcsolódik.  1. lehetőség elérhető feladatokat, amelyek **nem* végezhető el a futási térben van:
   
   * A gyári beállítások visszaállítása
   * A jelszó módosítása
   * Engedélyezheti vagy tilthatja le a támogatási hozzáférés
   * Frissítések alkalmazása
   * Gyorsjavítások telepítése

    > [!NOTE]
    > Ez a lehetőség az előnyben részesített Ha elfelejtette az eszköz rendszergazdai jelszava és 1 vagy 2 keresztül nem tud kapcsolódni.

4. **Nyelv váltása** ezzel a beállítással módosíthatja a Windows PowerShell felületet a nyelven jelennek meg. A támogatott nyelvek angol, japán, orosz, francia, dél koreai, német, olasz, német, kínai és brazíliai portugál.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Távoli csatlakozás a StorSimple-lel Windows PowerShell használatával

A Windows PowerShell távoli eljáráshívás segítségével a StorSimple eszköz csatlakozni. Ezzel a módszerrel csatlakoztatásakor menü nem jelenik meg. (Megjelenik a menü csak akkor, ha a soros konzol az eszközön való csatlakozáshoz használ. Távoli kapcsolódás közvetlenül viszi "1. lehetőség – teljes körű hozzáférési" a soros konzolon egyenértékű.) A Windows PowerShell-távelérést csatlakozhat egy adott futási teret. A megjelenítési nyelv is megadható.

A megjelenítési nyelve nem függ a nyelv, amely használatával a **nyelv váltása** beállítást a soros konzol menüből. Távoli PowerShell automatikusan felveszi a területi beállítás az eszköz csatlakozik, ha nincs megadva.

> [!NOTE]
> Ha a Microsoft Azure virtuális gazdagépek és a StorSimple felhő készülékek dolgozik, Windows PowerShell távelérési funkcióihoz és a virtuális gazdagép használhatja a felhő készülék való kapcsolódáshoz. Ha úgy állította be a helyét a gazdagépen, amelyen menteni az adatokat a Windows PowerShell-munkamenetben, amelyekkel célszerű tisztában lennie, amely a _mindenki_ rendszerbiztonsági tag csak a hitelesített felhasználók tartalmazza. Ezért ha állította be a megosztáshoz való hozzáférésének engedélyezése _mindenki_ és hitelesítő adatainak megadása nélkül csatlakoztat, a nem hitelesített névtelen egyszerű használja, hibaüzenet jelenik meg. A probléma megoldásához a megosztáson tárolni, akkor kell a Vendég fiók engedélyezése, és adja meg a Vendég fiók teljes hozzáféréssel a megosztáshoz, vagy adjon meg érvényes hitelesítő adatokat a Windows PowerShell-parancsmaggal együtt.


HTTP vagy HTTPS használatával Windows PowerShell távoli eljáráshívás keresztül csatlakozni. Kövesse az utasításokat az alábbi oktatóanyagok:

* [Csatlakozás távoli a HTTP-n keresztül](storsimple-remote-connect.md#connect-through-http)
* [Távolról a HTTPS használatával](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Kapcsolat biztonsági megfontolások

StorSimple és a Windows PowerShell összekapcsolása dönt, vegye figyelembe a következőket:

* Az eszköz soros konzoljához való közvetlen csatlakozás biztonságos, de nincs csatlakozás soros konzolon keresztül hálózati kapcsolók. Legyen óvatos, a biztonsági kockázat, hálózati kapcsolók keresztül eszköz soros kapcsolódik.
* Egy HTTP-kapcsolaton keresztül csatlakozó felajánlhatja további biztonságot nyújtana csatlakozás soros konzolon keresztül a hálózaton keresztül. Bár ez nem a legbiztonságosabb módszer, akkor elfogadható megbízható hálózatokon.
* Egy HTTPS-kapcsolaton keresztül csatlakozó értéke a legbiztonságosabb és ajánlott.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>A StorSimple eszközt a StorSimple a Windows PowerShell felügyelete

A következő táblázat a Windows PowerShell-felületet a StorSimple eszköz belül a gyakori felügyeleti feladatok és a bonyolult munkafolyamatok végrehajtható összegzését jeleníti meg. Minden munkafolyamat kapcsolatos további információkért kattintson a megfelelő bejegyzésre a táblában.

#### <a name="windows-powershell-for-storsimple-workflows"></a>A Windows PowerShell a StorSimple-munkafolyamatok

| Ha azt szeretné, hogy ehhez... | Ez az eljárás használható. |
| --- | --- |
| Regisztrálja az eszközt |[A Windows PowerShell használatával a StorSimple eszköz konfigurálása és regisztrálása](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| A webproxy konfigurálása</br>Webalkalmazás-proxy beállítások megjelenítése |[A StorSimple eszköz webalkalmazás-proxy konfigurálása](storsimple-8000-configure-web-proxy.md) |
| DATA 0 hálózati kapcsolat az eszközön található beállítások módosítása |[A StorSimple eszközt a DATA 0 hálózati adapterén módosítása](storsimple-8000-modify-data-0.md) |
| A vezérlő leállítása </br> A vezérlő leállítása vagy újraindítása </br> Egy eszköz leállítása</br>Állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra |[Eszközvezérlők kezelése](storsimple-8000-manage-device-controller.md) |
| Karbantartási mód frissítéseket és gyorsjavításokat telepítése |[Az eszköz frissítése](storsimple-update-device.md) |
| Adja meg a karbantartási mód </br>Kilépés karbantartási mód |[A StorSimple eszköz módok](storsimple-8000-device-modes.md) |
| Hozzon létre egy támogatási csomag</br>Fejti vissza és egy támogatási csomag szerkesztése |[Létrehozását és kezelését egy támogatási csomag](storsimple-8000-create-manage-support-package.md) |
| Támogatási munkamenet indítása</br> |[Támogatási munkamenetet indítani a Windows PowerShellben a StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Segítség a Windows PowerShellben a StorSimple

A Windows PowerShell-lel a parancsmag súgó áll rendelkezésre. A súgó online, naprakész verzióját is rendelkezésre áll, amelyek segítségével a rendszer a súgójának frissítése.

Ezen a felületen kapcsolatos segítség kérése hasonló a Windows PowerShellben, és a Súgó kapcsolatos parancsmagok a legtöbb fog működni. Súgó a Windows PowerShell környezethez online találja a TechNet könyvtárban: [a Windows PowerShell parancsfájlok](http://go.microsoft.com/fwlink/?LinkID=108518).

A Windows PowerShell felületén, beleértve a súgó frissítése a Súgó típusú rövid leírása a következő:

### <a name="to-get-help-for-a-cmdlet"></a>Ha segítséget szeretne kérni a parancsmag

* Ha segítséget szeretne kérni a parancsmag és funkció, a következő paranccsal:`Get-Help <cmdlet-name>`
* Bármely parancsmag online súgójának megjelenítéséhez használja az előző parancsmagot a `-Online` paraméter:`Get-Help <cmdlet-name> -Online`
* A teljes Súgó használhatják a `–Full` paramétert, és példák, használja a `–Examples` paraméter.

### <a name="to-update-help"></a>Súgó frissítése

A Windows PowerShell felületén a Súgó könnyen frissíthető. Frissítéséhez hajtsa végre a következő lépéseket a Súgó a rendszeren.

#### <a name="to-update-cmdlet-help"></a>A parancsmag súgójában frissítése
1. Indítsa el a Windows Powershellt a **Futtatás rendszergazdaként** lehetőséget.
2. A parancssorba írja be:`Update-Help`
3. A frissített súgófájlokat lesz telepítve.
4. A súgófájlok telepítve vannak-e, adja meg: `Get-Help Get-Command`. Ez megjeleníti a parancsmagok, amelynek súgója is elérhető.

> [!NOTE]
> Ahhoz, hogy az elérhető parancsmagok listáját térben, jelentkezzen be a megfelelő menüpontot, és futtassa a `Get-Command` parancsmag.


## <a name="next-steps"></a>Következő lépések

Ha probléma merül fel élmény a StorSimple eszközt a végrehajtásához a fenti munkafolyamatok egyik, tekintse meg a [StorSimple központi telepítések hibaelhárítási eszközök](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

