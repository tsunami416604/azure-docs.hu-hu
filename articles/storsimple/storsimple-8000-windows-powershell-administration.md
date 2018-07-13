---
title: A StorSimple-felügyelethez PowerShell |} A Microsoft Docs
description: 'Útmutató: a StorSimple-eszközök kezelése a storsimple-höz készült Windows PowerShell használatával.'
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 291de21d3d98bf9eb65adc7d506f3549171ee0a0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696820"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Eszköz felügyelete a storsimple-höz készült Windows PowerShell használatával

## <a name="overview"></a>Áttekintés

Windows PowerShell-bővítménye olyan parancssori felületet, amelyek segítségével kezelheti a Microsoft Azure StorSimple-eszköz biztosít. Ahogy a neve is sugallja, egy Windows PowerShell-alapú parancssori felületen, amely egy korlátozott futási térrel jön létre. A parancssorban a felhasználó szemszögéből egy korlátozott futási térrel egy Windows PowerShell korlátozott verziója jelenik meg. Miközben a Windows PowerShell alapvető funkciói, ez az interfész rendelkezik további dedikált parancsokat, amelyek kezelése a Microsoft Azure StorSimple-eszköz irányába.

Ez a cikk ismerteti a Windows PowerShell StorSimple-funkciókat, például a hogyan kapcsolódhat a kapcsolat, és részletes eljárásokat és munkafolyamatok, amelyek a felhasználói felületén is végezhet mutató hivatkozásokat tartalmaz. A munkafolyamatok bemutatják, hogyan regisztrálja az eszközt, konfigurálja a hálózati adaptert az eszközön, az eszköz karbantartási üzemmódban legyen, módosítsa az eszköz állapotát, és elháríthat bármilyen hibát tapasztalhat, igénylő frissítések telepítése.

Ez a cikk elolvasása után fogja tudni:

* A StorSimple-eszköz storsimple-höz készült Windows PowerShell használatával kapcsolódni.
* A StorSimple-eszköz storsimple-höz készült Windows PowerShell használatával felügyelhető.
* Kérjen segítséget a Windows PowerShell storsimple-höz készült.

> [!NOTE]
> * Windows PowerShell StorSimple-parancsmagok lehetővé teszik kezelése a StorSimple-eszköz soros konzoljához vagy távolról Windows PowerShell távoli eljáráshívás segítségével. További információ az egyes használható ezen a felületen egyes parancsmagok Ugrás [parancsmag-referencia a storsimple-höz készült Windows PowerShell](https://technet.microsoft.com/library/dn688168.aspx).
> * Az Azure PowerShell StorSimple-parancsmagok olyan parancsmagok, amelyek lehetővé teszik, hogy automatizálja a StorSimple szolgáltatásiszint- és áttelepítési feladatok a parancssorból egy másik gyűjteményt. Storsimple-höz készült Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [parancsmag-referencia az Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


A Windows PowerShell storsimple, az alábbi módszerek egyikének használatával érheti el:

* [A StorSimple-eszköz soros konzoljához való csatlakozáshoz](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Távoli csatlakozás a StorSimple Windows PowerShell-lel](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Csatlakozás Windows PowerShell storsimple-höz készült keresztül az eszköz soros konzoljához

Is [töltse le a PuTTY](http://www.putty.org/) vagy hasonló szeretne csatlakozni a Windows PowerShell storsimple-höz készült terminálemulációs szoftverre. PuTTY kifejezetten való eléréséhez a Microsoft Azure StorSimple-eszköz konfigurálásához szüksége. A következő témakörök tartalmaznak részletes lépéseket konfigurálása a putty-kapcsolaton keresztül, és csatlakozni az eszközhöz. A soros konzol különböző menüpontok is magyarázata.

### <a name="putty-settings"></a>PuTTY-beállítások

Győződjön meg arról, hogy használja-e a következő PuTTY beállításokat csatlakozni a Windows PowerShell felületét a soros konzolból.

#### <a name="to-configure-putty"></a>A PuTTY konfigurálása

1. A putty-kapcsolaton keresztül a **újrakonfigurálás** párbeszédpanel a **kategória** ablaktáblán válassza **billentyűzet**.
2. Győződjön meg arról, hogy az alábbi lehetőségek vannak-e jelölve, (amelyek az alapértelmezett beállításokat, amikor új munkamenet indításához).
   
   | Billentyűzet elem | Válassza ezt: |
   | --- | --- |
   | BACKSPACE kulcs |Vezérlő –? (127) |
   | Otthon és a záró kulcsok |Standard |
   | Funkcióbillentyűk és billentyűzet |Az ESC [n ~ |
   | A kurzor kulcsok kezdeti állapota |Normál |
   | Számbillentyűzeten kezdeti állapota |Normál |
   | Egyéb billentyűzet-szolgáltatások engedélyezése |Ellenőrzés-Alt AltGr eltér |
   
    ![Támogatott a Putty-beállítások](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kattintson az **Alkalmaz** gombra.
4. Az a **kategória** ablaktáblán válassza előbb **fordítási**.
5. Az a **távoli karakterkészlet** listáján jelölje ki **UTF-8**.
6. Alatt **Vonalrajz karakterek kezelését**válassza **használata Unicode Vonalrajz kódpontok**. Az alábbi képernyőfelvételen a megfelelő PuTTY beállításokat.
   
    ![UTF Putty-beállítások](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kattintson az **Alkalmaz** gombra.

A PuTTY segítségével mostantól a következő lépések végrehajtásával az eszköz soros konzoljához való csatlakozáshoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>A soros konzol bemutatása

Amikor fér hozzá a Windows PowerShell felületet a StorSimple eszköz szalagcímüzenet soros konzolon keresztül jelennek meg, kiegészítve a menüpontok.

Címsorában látható a StorSimple eszköz alapvető információkat, például a modellt, a nevét, a telepített szoftververzió és az Ön hozzáfér a vezérlő állapotának tartalmazza. Az alábbi képen egy címsorában látható egy példa látható.

![Soros szalagcímüzenet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Címsorában látható segítségével azonosíthatja a vezérlő, hogy csatlakozik-e _aktív_ vagy _passzív_.

Az alábbi képen látható a különböző futási térben lehetőségek, amelyek a soros konzol menüjének érhetők el.

![Regisztrálja az eszközt 2](./media/storsimple-windows-powershell-administration/IC740906.png)

A következő beállítások közül választhat:

1. **Jelentkezzen be a teljes hozzáféréssel** Ez a beállítás lehetővé teszi, hogy csatlakozni (a szükséges hitelesítő adatokat), a **SSAdminConsole** futási térben a helyi tartományvezérlőn. (A helyi tartományvezérlő a tartományvezérlővel, amely jelenleg a StorSimple-eszköz soros konzolon keresztül Ön hozzáfér.) Ezt a lehetőséget is, hogy Support eléréséhez nem korlátozott futási térrel (egy támogatási munkamenetet) esetleges lehetséges problémák megoldásához használható. 1. lehetőség használatával jelentkezzen be, miután a Microsoft Support mérnök, nem korlátozott futási térrel eléréséhez egy adott parancsmag futtatásával engedélyezheti. Részletekért tekintse meg a [indítson el egy támogatási munkamenetet](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Jelentkezzen be a teljes hozzáféréssel rendelkező társ vezérlő** Ez a lehetőség ugyanaz, mint 1. lehetőség – akkor, azzal a különbséggel, hogy csatlakozhasson (a szükséges hitelesítő adatokat) a **SSAdminConsole** futási térben a társ-vezérlőn. Mivel a két vezérlőn egy aktív / passzív konfigurációt a magas rendelkezésre állású eszköz, a StorSimple-eszköz társ hivatkozik az eszköz soros konzolon keresztül elérő a másik vezérlőre).
   Hasonló az 1. lehetőség, ezt a lehetőséget is használható, hogy a Microsoft Support társ tartományvezérlőn nem korlátozott futási térrel eléréséhez.

3. **Korlátozott hozzáférésű csatlakozás** ezzel a beállítással Windows PowerShell felületét korlátozott módban eléréséhez. Nem kéri a hozzáférési hitelesítő adatokat. Ez a beállítás 1. és 2 beállítások képest nagyobb korlátozott futási térrel csatlakozik.  1. lehetőség keresztül elérhető feladatokat, amelyek **nem* végezhető el a futási térben van:
   
   * A gyári beállításainak visszaállítása
   * A jelszó módosítása
   * Engedélyezi vagy letiltja a hozzáférést támogatási
   * Frissítések alkalmazása
   * Gyorsjavítások telepítése

    > [!NOTE]
    > Ez a előnyben részesített lehetőség, ha az eszköz rendszergazdai jelszava elfelejtette és keresztül beállítás 1 vagy 2-es nem tud kapcsolódni.

4. **Nyelvváltás** ezzel a beállítással, hogy a Windows PowerShell felületét a megjelenítési nyelvének módosítására. Támogatott nyelvek angol, japán, spanyol, francia, dél-koreai, spanyol, olasz, német, kínai és brazíliai portugál nyelveken is.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Távoli csatlakozás a StorSimple storsimple-höz készült Windows PowerShell használatával

Használhatja a Windows PowerShell-táveléréssel kapcsolódni a StorSimple-eszköz. A csatlakozáskor ezzel a módszerrel nem megjelenik egy menü. (Megjelenik egy menü csak akkor, ha a soros konzol az eszközön való kapcsolódáshoz használ. Távoli kapcsolódás megnyílik a közvetlenül az "1. lehetőség – teljes hozzáférést" a soros konzol megfelelőjét.) A Windows PowerShell-táveléréssel akkor csatlakozik egy adott futási teret is. Adja meg a megjelenítési nyelvét is.

A megjelenítési nyelv az független a nyelv, amely segítségével lát a **nyelv módosítása** a soros konzol menüjének lehetőség. Távoli PowerShell automatikusan kiesik a területi beállítás az eszköz csatlakozik, ha nincs megadva.

> [!NOTE]
> Ha a Microsoft Azure virtuális gazdagépeket és a StorSimple Cloud Appliance dolgozik, Windows PowerShell-táveléréssel, és a virtuális gazdagép használatával csatlakozhat a felhőalapú berendezéshez. Ha beállította egy megosztás helyét a gazdagépen, amelyen menteni az adatokat a Windows PowerShell-munkamenetben van, érdemes figyelembe, hogy a _mindenki_ rendszerbiztonsági tag csak a hitelesített felhasználók tartalmazza. Ezért ha úgy állította be a megosztás hozzáférésének engedélyezése _mindenki_ és a hitelesítő adatok megadása nélkül csatlakozik, a nem hitelesített névtelen egyszerű fogja használni, és hibaüzenet jelenik meg. A probléma megoldásához, a megosztást a gazdagépen, amelyet kell a Vendég fiók engedélyezve, és ezután a Vendég fiók teljes hozzáférést biztosít a megosztást, vagy adjon meg érvényes hitelesítő adatokat, valamint a Windows PowerShell-parancsmagot.


HTTP- vagy HTTPS használatával Windows PowerShell távoli eljáráshívás-n keresztül csatlakozik. Kövesse az utasításokat az alábbi oktatóanyagok:

* [Csatlakozzon távolról a HTTP-n keresztül](storsimple-8000-remote-connect.md#connect-through-http)
* [Csatlakozzon távolról a HTTPS-en keresztül](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Kapcsolat biztonsági szempontok

Amikor azt fontolgatja, hogyan csatlakozhat Windows PowerShell storsimple-höz készült, vegye figyelembe a következőket:

* Az eszköz soros konzoljához való közvetlen csatlakozás lehetőségét biztonságos, de nincs csatlakozás soros konzolon hálózati kapcsolók keresztül. Legyen óvatos az a biztonsági veszély a hálózati kapcsolók keresztül az eszköz soros kapcsolódik.
* HTTP-munkameneten keresztül csatlakozik, előfordulhat, hogy hálózaton keresztüli csatlakozás a soros konzolon keresztül nagyobb biztonságot kínálnak. Bár ez nem a legbiztonságosabb módszer, egy megbízható hálózatokon feltételei.
* Kapcsolaton keresztül egy HTTPS-KAPCSOLATON keresztül, a legbiztonságosabb és a javasolt megoldás.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Felügyelete a StorSimple-eszköz storsimple-höz készült Windows PowerShell használatával

Az alábbi táblázat a gyakori felügyeleti feladatok és a komplex munkafolyamatok végrehajtható összegzését jeleníti meg a Windows PowerShell felületét a StorSimple eszköz belül. Minden egyes munkafolyamat kapcsolatos további információkért kattintson a megfelelő bejegyzés a táblában.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell StorSimple-munkafolyamatok

| Ha azt szeretné, ehhez... | Ez az eljárás használható. |
| --- | --- |
| Az eszköz regisztrálása |[A storsimple-höz készült Windows PowerShell használatával eszköz konfigurálása és regisztrálása](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| A webproxy konfigurálása</br>Nézet webproxy beállításai |[A StorSimple eszköz webproxy konfigurálása](storsimple-8000-configure-web-proxy.md) |
| DATA 0 hálózati adapter az eszközön található beállítások módosítása |[A StorSimple eszköz DATA 0 hálózati adapter módosítása](storsimple-8000-modify-data-0.md) |
| Egy vezérlő leállítása </br> Indítsa újra, vagy egy vezérlő leállítása </br> Egy eszköz leállítása</br>Állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra |[Eszközvezérlők kezelése](storsimple-8000-manage-device-controller.md) |
| Karbantartási módú frissítések és gyorsjavítások telepítése |[Az eszköz frissítése](storsimple-update-device.md) |
| Adja meg a karbantartási mód </br>Kilépés a karbantartási módból |[A StorSimple eszköz mód](storsimple-8000-device-modes.md) |
| Hozzon létre egy támogatási csomagot</br>Visszafejtésére és a egy támogatási csomag szerkesztése |[Hozzon létre és kezelheti a támogatási csomagot](storsimple-8000-create-manage-support-package.md) |
| Indítson el egy támogatási munkamenetet</br> |[Indítson el egy támogatási munkamenetet a Windows PowerShell storsimple-höz készült](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Kérjen segítséget a Windows PowerShell storsimple-höz készült

A storsimple-höz készült Windows PowerShell a parancsmag súgójában érhető el. Egy online, naprakész verziója a Súgó is érhető el, amely a rendszeren a súgójának frissítéséhez használhatja.

Segítség kérése ezen a felületen hasonló, mint a Windows PowerShellben, és a Súgó kapcsolatos parancsmagok a legtöbb fog működni. Súgó a Windows PowerShell környezethez online találhatja meg a TechNet könyvtárban: [parancsfájlkezelés a Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Az alábbiakban látható a a Windows PowerShell felületén, beleértve a súgó frissítése súgó típusú rövid leírását.

### <a name="to-get-help-for-a-cmdlet"></a>Egy parancsmag súgójának

* Segítséget parancsmag vagy-funkció, a következő paranccsal: `Get-Help <cmdlet-name>`
* Online súgó a parancsmagokhoz, használja az előző parancsmagot a `-Online` paramétert: `Get-Help <cmdlet-name> -Online`
* A teljes segítségre van szüksége, használhatja a `–Full` paramétert, és a példákat, használja a `–Examples` paraméter.

### <a name="to-update-help"></a>Súgójának frissítéséhez

A Windows PowerShell felületén segítségével egyszerűen frissíthetők. Hajtsa végre az alábbi lépéseket a rendszeren a súgójának frissítéséhez.

#### <a name="to-update-cmdlet-help"></a>A parancsmag súgójában frissítése
1. Indítsa el a Windows Powershellt a **Futtatás rendszergazdaként** lehetőséget.
2. A parancssorba írja be:  `Update-Help`
3. A frissített súgófájlokat lesz telepítve.
4. Miután a súgófájlok telepítve vannak, írja be: `Get-Help Get-Command`. Ez megjeleníti, amelynek érhető el súgó parancsmagok listáját.

> [!NOTE]
> A futási térben az elérhető parancsmagok listájának lekéréséhez jelentkezzen be a megfelelő menüpontot, és futtassa a `Get-Command` parancsmagot.


## <a name="next-steps"></a>További lépések

Ha minden olyan problémákat tapasztal a StorSimple-eszközre a fenti munkafolyamatok egyik végrehajtásakor, [a StorSimple-környezetek hibaelhárítási eszközei](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

