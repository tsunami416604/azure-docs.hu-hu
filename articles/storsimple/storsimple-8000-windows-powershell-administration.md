---
title: PowerShell a StorSimple eszközkezeléséhez
description: Megtudhatja, hogy a StorSimple-eszköz kezeléséhez hogyan kezelheti a Windows PowerShell for StorSimple alkalmazást.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277110"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Eszköz felügyelete a Windows PowerShell StorSimple-bővítményével

## <a name="overview"></a>Áttekintés

A Windows PowerShell for StorSimple parancssori felületet biztosít, amely a Microsoft Azure StorSimple-eszköz kezeléséhez használható. Ahogy a neve is sugallja, ez egy Windows PowerShell-alapú, parancssori felület, amely egy korlátozott runspace épül. A felhasználó szempontjából a parancssorban, egy korlátozott runspace jelenik meg a Windows PowerShell korlátozott verziója. A Windows PowerShell néhány alapvető képességének fenntartása mellett ez a felület további dedikált parancsmagokkal rendelkezik, amelyek a Microsoft Azure StorSimple-eszköz kezelésére irányulnak.

Ez a cikk ismerteti a Windows PowerShell for StorSimple szolgáltatásait, beleértve azt is, hogy miként csatlakozhat ehhez a felülethez, és olyan részletes eljárásokra vagy munkafolyamatokra mutató hivatkozásokat tartalmaz, amelyeket ezzel a felülettel hajthat végre. A munkafolyamatok között szerepel, hogyan regisztrálhatja az eszközt, konfigurálhatja a hálózati adaptert az eszközön, hogyan telepítheti azokat a frissítéseket, amelyek megkövetelik, hogy az eszköz karbantartási módban legyen, módosítsa az eszköz állapotát, és elhárítsa az esetlegesen felmerülő problémákat.

Elolvasása után ezt a cikket, akkor képes lesz arra, hogy:

* Csatlakozzon a StorSimple-eszközhöz a Windows PowerShell storSimple-hez használatával.
* A StorSimple-eszköz felügyelete a Windows PowerShell storSimple-hez használatával.
* Segítség a StorSimple-hez való Windows PowerShellben.

> [!NOTE]
> * A Windows PowerShell storSimple-parancsmagok lehetővé teszik a StorSimple-eszköz kezelését egy soros konzolról vagy távolról a Windows PowerShell-alapú távoli eljáráson keresztül. Az ezen a felületen használható egyes parancsmagokról a Windows [PowerShell storSimple-hez való parancsmag-hivatkozási hivatkozása](https://technet.microsoft.com/library/dn688168.aspx)című részében talál további információt.
> * Az Azure PowerShell StorSimple parancsmagok egy másik gyűjteménye parancsmagok, amelyek lehetővé teszik a StorSimple szolgáltatásszintű és áttelepítési feladatok automatizálását a parancssorból. Az Azure PowerShell-parancsmagok storSimple, látogasson el az [Azure StorSimple parancsmag referencia.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure)


A Windows PowerShell for StorSimple az alábbi módszerek egyikével érhető el:

* [Csatlakozás a StorSimple eszköz soros konzoljához](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Csatlakozás távolról a StorSimple-hez a Windows PowerShell használatával](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Csatlakozás a StorSimple hez használható Windows PowerShellhez az eszköz soros konzolján keresztül

[Letöltheti a PuTTY](https://www.putty.org/) vagy hasonló terminálemulációs szoftvert a Windows PowerShell storSimple-hez való csatlakozáshoz. A PuTTY-t kifejezetten a Microsoft Azure StorSimple eszköz eléréséhez kell konfigurálnia. Az alábbi témakörök részletes lépéseket tartalmaznak a PuTTy konfigurálásáról és az eszközhöz való csatlakozásról. A soros konzol különböző menüelemeit is elmagyarázzák.

### <a name="putty-settings"></a>PuTTY-beállítások

Győződjön meg arról, hogy a következő PuTTY-beállításokat használja a Windows PowerShell-felülethez való csatlakozáshoz a soros konzolról.

#### <a name="to-configure-putty"></a>A PuTTY konfigurálása

1. A PuTTY **újrakonfigurálása** párbeszédpanel **Kategória** ablaktáblájában válassza a **Billentyűzet**lehetőséget.
2. Győződjön meg arról, hogy a következő beállítások vannak kiválasztva (ezek az alapértelmezett beállítások új munkamenet indításakor).
   
   | Billentyűzetelem | Válassza ezt: |
   | --- | --- |
   | Backspace kulcs |Irányítás-? (127) |
   | Otthoni és záró billentyűk |Standard |
   | Funkciógombok és billentyűzet |ESC[n~ |
   | A kurzorbillentyűk kezdeti állapota |Normál |
   | A numerikus billentyűzet kezdeti állapota |Normál |
   | További billentyűzetfunkciók engedélyezése |A Control-Alt különbözik az AltGr-től |
   
    ![Támogatott gitt beállítások](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kattintson az **Alkalmaz** gombra.
4. A **Kategória** ablaktáblán válassza a **Fordítás**lehetőséget.
5. A **Távoli karakterkészlet** listában válassza az **UTF-8**lehetőséget.
6. A **Vonalrajzi karakterek kezelése**csoportban jelölje be a **Unicode vonalrajzi kódpontok használata jelölőnégyzetet.** A következő képernyőképen látható a megfelelő PuTTY választás.
   
    ![UTF putty beállítások](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kattintson az **Alkalmaz** gombra.

Most már használhatja PuTTY csatlakozni az eszköz soros konzol a következő lépéseket.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>A soros konzolról

Amikor a StorSimple-eszköz Windows PowerShell felületét a soros konzolon keresztül éri el, egy szalagcímüzenet jelenik meg, amelyet a menübeállítások követnek.

A szalagcímüzenet alapvető StorSimple-eszközinformációkat tartalmaz, például a modellt, a nevet, a telepített szoftververziót és az elérni kívánt vezérlő állapotát. Az alábbi képen egy szalagcímüzenet látható.

![Soros szalagcím üzenet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> A szalagcímüzenet segítségével azonosíthatja, hogy a vezérlő, amelyhez csatlakozik, _aktív_ vagy _passzív._

Az alábbi képen a soros konzol menüjében elérhető különböző runspace-beállítások láthatók.

![A készülék regisztrálása 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Az alábbi beállítások közül választhat:

1. **Bejelentkezés teljes hozzáféréssel** Ez a beállítás lehetővé teszi, hogy csatlakozzon (a megfelelő hitelesítő adatokkal) a helyi vezérlő **SSAdminConsole** runspace-hez. (A helyi vezérlő az a vezérlő, amelyet jelenleg a StorSimple-eszköz soros konzolján keresztül ér el.) Ezzel a beállítással a Microsoft támogatási szolgálata is hozzáférhet a korlátlan runspace-hez (egy támogatási munkamenethez) az esetleges eszközproblémák elhárításához. Az 1. További információt a [Támogatási munkamenet indítása .](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
   
2. **Bejelentkezés teljes hozzáféréssel a társvezérlőbe** Ez a beállítás megegyezik az 1. **SSAdminConsole** Mivel a StorSimple eszköz egy magas rendelkezésre állású eszköz két vezérlő egy aktív-passzív konfiguráció, peer utal, hogy a másik vezérlő az eszközön, amely a soros konzolon keresztül érhető el).
   Az 1.

3. **Csatlakozás korlátozott hozzáféréssel** Ez a beállítás a Windows PowerShell-felület korlátozott módban való elérésére szolgál. A rendszer nem kéri a hozzáférési hitelesítő adatok megadását. Ez a beállítás az 1.  Az 1.*cannot*
   
   * Visszaállítás a gyári beállításokra
   * A jelszó módosítása
   * A támogatási hozzáférés engedélyezése vagy letiltása
   * Frissítések alkalmazása
   * Gyorsjavítások telepítése

     > [!NOTE]
     > Ez az előnyben részesített lehetőség, ha elfelejtette az eszköz rendszergazdai jelszavát, és nem tud csatlakozni az 1-es vagy 2-es lehetőségen keresztül.

4. **Nyelv módosítása** Ez a beállítás lehetővé teszi a megjelenítési nyelv módosítását a Windows PowerShell felületén. A támogatott nyelvek az angol, a japán, az orosz, a francia, a dél-koreai, a spanyol, az olasz, a német, a kínai és a brazil portugál.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Csatlakozás távolról a StorSimple-hez a Windows PowerShell for StorSimple használatával

A Windows PowerShell-távszolgáltatás használatával csatlakozhat a StorSimple-eszközhöz. Ha így csatlakozik, nem jelenik meg a menü. (Csak akkor jelenik meg a menü, ha az eszköz soros konzolját használja a csatlakozáshoz. A távolról történő csatlakozás sal közvetlenül a soros konzolon az "1. lehetőség – teljes hozzáférés" megfelelője.) A Windows PowerShell-szolgáltatás sal való kapcsolatának használatával egy adott runspace-hez csatlakozhat. Megadhatja a megjelenítési nyelvet is.

A megjelenítési nyelv független a soros konzol menüjének **Nyelv módosítása** beállításával beállított nyelvtől. A távoli PowerShell automatikusan felveszi annak az eszköznek a területi beállítását, amelyről csatlakozik, ha nincs megadva.

> [!NOTE]
> Ha a Microsoft Azure virtuális gazdagépekkel és a StorSimple felhőalapú készülékekkel dolgozik, használhatja a Windows PowerShell-távadó és a virtuális gazdagép segítségével a felhőalapú berendezéshez való csatlakozást. Ha beállított egy megosztási helyet az állomáson, amelyen adatokat szeretne menteni a Windows PowerShell-munkamenetből, vegye figyelembe, hogy a _Mindenki_ egyszerű csak hitelesített felhasználókat tartalmaz. Ezért ha úgy állította be a megosztást, hogy mindenki _számára_ engedélyezze a hozzáférést, és hitelesítő adatok megadása nélkül csatlakozik, a rendszer a nem hitelesített névtelen egyszerű stafétakat fogja használni, és hibaüzenet et fog látni. A probléma megoldásához a megosztási állomáson engedélyeznie kell a Vendég fiókot, majd teljes hozzáférést kell biztosítania a Vendég fióknak a megosztáshoz, vagy meg kell adnia érvényes hitelesítő adatokat a Windows PowerShell-parancsmaggal együtt.


Http vagy HTTPS használatával csatlakozhat a Windows PowerShell-kapcsolaton keresztül. Kövesse az alábbi útmutatók utasításait:

* [Csatlakozás távolról HTTP használatával](storsimple-8000-remote-connect.md#connect-through-http)
* [Csatlakozás távolról HTTPS használatával](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>A kapcsolat biztonságának szempontjai

Amikor eldönti, hogyan csatlakozhat a StorSimple-alapú Windows PowerShellhez, vegye figyelembe az alábbiakat:

* Közvetlenül az eszköz soros konzoljához való csatlakozás biztonságos, de a soros konzolhoz való hálózati kapcsolókon keresztül való csatlakozás nem. Legyen óvatos a biztonsági kockázat, amikor csatlakozik az eszköz soros hálózati kapcsolókon keresztül.
* A HTTP-munkameneten keresztül történő csatlakozás nagyobb biztonságot nyújthat, mint a soros konzolon keresztül ion keresztüli csatlakozás a hálózaton keresztül. Bár ez nem a legbiztonságosabb módszer, megbízható hálózatokon is elfogadható.
* Https-munkameneten keresztüli csatlakozás a legbiztonságosabb és az ajánlott beállítás.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>A StorSimple-eszköz felügyelete a StorSimple-hez való Windows PowerShell használatával

Az alábbi táblázat a StorSimple-eszköz Windows PowerShell-felületén elvégezhető összes gyakori felügyeleti feladat és összetett munkafolyamat összegzését mutatja be. Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a megfelelő bejegyzésre a táblázatban.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell StorSimple munkafolyamatokhoz

| Ha ezt akarod csinálni... | Használja ezt az eljárást. |
| --- | --- |
| Eszköz regisztrálása |[Az eszköz konfigurálása és regisztrálása a StorSimple-hez való Windows PowerShell használatával](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| A webproxy konfigurálása</br>Webproxy-beállítások megtekintése |[Webproxy konfigurálása a StorSimple eszközhöz](storsimple-8000-configure-web-proxy.md) |
| Data 0 hálózati illesztő beállításainak módosítása az eszközön |[A DATA 0 hálózati felület módosítása a StorSimple eszközhöz](storsimple-8000-modify-data-0.md) |
| Vezérlő leállítása </br> Vezérlő újraindítása vagy leállítása </br> Eszköz leállítása</br>Az eszköz visszaállítása a gyári alapértelmezett beállításokra |[Eszközvezérlők kezelése](storsimple-8000-manage-device-controller.md) |
| Karbantartási mód frissítéseinek és gyorsjavításainak telepítése |[Az eszköz frissítése](storsimple-update-device.md) |
| Adja meg a karbantartási módot </br>Kilépés a karbantartási módból |[StorSimple eszközmódok](storsimple-8000-device-modes.md) |
| Támogatási csomag létrehozása</br>Támogatási csomag visszafejtése és szerkesztése |[Támogatási csomag létrehozása és kezelése](storsimple-8000-create-manage-support-package.md) |
| Támogatási munkamenet indítása</br> |[Támogatási munkamenet indítása a StorSimple-hez biztosított Windows PowerShellben](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Segítség a StorSimple-hez való Windows PowerShellben

A Windows PowerShell storSimple rendszerhez, parancsmag súgó érhető el. A súgó online, naprakész változata is elérhető, amelynek segítségével frissítheti a súgót a rendszeren.

A windows PowerShell hez hasonló a súgó beszerzése ezen a felületen, és a súgóval kapcsolatos parancsmagok többsége működni fog. A Windows PowerShell súgóját online találja: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Az alábbiakban röviden ismertetjük a Windows PowerShell-felület súgótípusait, beleértve a súgó frissítését is.

### <a name="to-get-help-for-a-cmdlet"></a>Segítség a parancsmaghoz

* Bármely parancsmaghoz vagy funkcióhoz a következő paranccsal kaphat segítséget:`Get-Help <cmdlet-name>`
* Ha bármely parancsmaghoz online súgót szeretne beszerezni, `-Online` használja az előző parancsmatot a következő paraméterrel:`Get-Help <cmdlet-name> -Online`
* A teljes súgóhasználatához használhatja a `–Full` paramétert, `–Examples` és példákként használhatja a paramétert.

### <a name="to-update-help"></a>A súgó frissítése

A Súgó egyszerűen frissíthető a Windows PowerShell felületén. Hajtsa végre az alábbi lépéseket a súgó frissítéséhez a rendszeren.

#### <a name="to-update-cmdlet-help"></a>A parancsmag súgójának frissítése
1. Indítsa el a Windows PowerShell t a **Futtatás rendszergazdaként** beállítással.
2. A parancssorba írja be a következőt:`Update-Help`
3. A frissített súgófájlok telepítése megkezdődik.
4. A súgófájlok telepítése után `Get-Help Get-Command`írja be a következőt: . Ekkor megjelenik azoknak a parancsmagoknak a listája, amelyekhez a súgó elérhető.

> [!NOTE]
> A runspace összes elérhető parancsmag listájának lehívásához jelentkezzen be a megfelelő `Get-Command` menüpontba, és futtassa a parancsmalistát.


## <a name="next-steps"></a>További lépések

Ha a fenti munkafolyamatok valamelyikének végrehajtásakor bármilyen problémát tapasztal a StorSimple-eszközzel kapcsolatban, olvassa el a [StorSimple-telepítések hibaelhárítását szolgáló eszközök című témakört.](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)

