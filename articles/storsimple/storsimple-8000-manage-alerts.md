---
title: "A StorSimple 8000 series eszköz riasztások megtekintése és kezelése |} Microsoft Docs"
description: "Ismerteti a StorSimple riasztási feltételek és a súlyosság, a riasztási értesítések konfigurálása és a riasztások kezeléséről a StorSimple Device Manager szolgáltatás használata."
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
ms.workload: NA
ms.date: 08/22/2017
ms.author: alkohli
ms.openlocfilehash: b7f9a2b7eb3dbf4cc97fac9a410359e068e67eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>A StorSimple Device Manager szolgáltatással a StorSimple-riasztások megtekintése és kezelése

## <a name="overview"></a>Áttekintés

A **riasztások** StorSimple Device Manager szolgáltatási panelen áttekintheti, és törölje a StorSimple eszköz – kapcsolatos riasztások valós idejű alapon megoldást kínál. Ezen a panelen központilag figyelheti a ügynökállapottal kapcsolatos hibákkal a StorSimple eszköz és a teljes Microsoft Azure StorSimple megoldáshoz.

Ez az oktatóanyag ismerteti a gyakori riasztási állapot, a riasztás súlyossági szintek és a riasztási értesítések beállítása. Ezenkívül azt is riasztás rövid összefoglaló táblák, amelyekkel gyorsan keresse meg az adott riasztásra, és megfelelő választ.

![Riasztások lap](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási állapot

A StorSimple eszköz feltételek különböző válaszul riasztásokat állít elő. A leggyakoribb riasztások feltételek a következők:

* **Hardverproblémák** – ezek a riasztások adja meg a hardver állapotát. Azt jelzi, ha a belső vezérlőprogram frissítések szükségesek, ha egy adott hálózati csatoló problémákkal rendelkezik, vagy ha valamelyik, a meghajtók probléma van a használatukkal.
* **Kapcsolódási problémák** – ezek a riasztások fordulhat elő, ha az adatok átvitele nehézségekbe ütközik. Kommunikációs problémák adatátvitel során fordulhatnak elő, hogy és az Azure storage-fiók vagy az eszközök és a StorSimple Device Manager szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs problémák nagyon nehéz javítsa ki, mert nincs olyan sok ponton felmerülő hibákat. Meg kell először mindig ellenőrizze, hogy hálózati kapcsolatot és az Internet-hozzáférés elérhető speciális hibaelhárítás be a folytatás előtt. Segítség a hibaelhárításban, [kapcsolatos problémák elhárítása a Test-Connection parancsmaggal](storsimple-troubleshoot-deployment.md).
* **Teljesítményproblémák** – ezek a riasztások vannak okozza, ha a rendszer nem optimális, például a nagy terhelés alatt van.

Ezenkívül jelenhet meg biztonsági, a frissítések vagy a sikertelen feladatok kapcsolatos riasztásokat.

## <a name="alert-severity-levels"></a>Riasztások súlyossági szintjei

Riasztások különböző súlyossági szintek, attól függően, hogy a hatás, hogy a riasztás helyzet és a riasztás választ az rendelkezik. A súlyossági szintek a következők:

* **Kritikus** – Ez a riasztás olyan feltétel, amely érinti a sikeres, a rendszer teljesítménye válaszul van. TEENDŐ annak érdekében, hogy a StorSimple szolgáltatás nem szakad meg.
* **Figyelmeztetés** – Ez az állapot kritikus, ha nem sikerül válhat. Vizsgálja meg a helyzet kell, és teendője kell törölnie a problémát.
* **Információ** – Ez a riasztás információkat tartalmaznak, amelyek nyomon követése és a rendszer kezelése hasznos lehet.

## <a name="configure-alert-settings"></a>A riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretné-e az egyes, a StorSimple eszközök riasztási feltételek e-mailben értesítést kapni. Ezenkívül azonosítsa a más riasztási értesítés címzettjeinek írja be az e-mail címüket a **más e-mailek címzettjeinek** mezőbe pontosvesszővel elválasztva.

> [!NOTE]
> Legfeljebb 20 e-mail címek eszközönként adhat meg.

Egy eszköz számára e-mail értesítések engedélyezése után az értesítési listán tagjai minden alkalommal, amikor egy kritikus riasztás esetén e-mailt fog kapni. Az üzenetek küldését  *storsimple-alerts-noreply@mail.windowsazure.com*  és leírja a riasztást kiváltó körülmény. Címzettek kattintva **Unsubscribe** maguk eltávolítása az e-mail értesítési listán.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>A riasztások egy eszköz számára e-mail értesítések engedélyezése
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájában válassza ki, majd kattintson az eszközre, amelyet szeretne beállítani.
2. Ugrás a **beállítások** > **általános** az eszközön.

   ![Riasztások panel](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Az a **általános beállítások** paneljén lépjen **riasztási beállítások** és állítsa be a következőket:
   
   1. Az a **értesítés küldése e-mailben** mezőben válassza **Igen**.
   2. Az a **szolgáltatás-rendszergazdák E-mail** mezőben válassza **Igen** számára a szolgáltatás rendszergazdája, és minden társrendszergazdák a riasztási értesítéseket.
   3. Az a **más e-mailek címzettjeinek** mezőbe írja be a többi a riasztási értesítéseket kapjanak címzett e-mail címét. Írja be nevét a formátumban  *someone@somewhere.com* . Az e-mail címek elválasztásához használjon pontosvesszőt. Legfeljebb 20 e-mail címek eszközönként konfigurálhatja. 
      
3. Egy teszt e-mail értesítést küldeni, kattintson a **teszt e-mail küldése**. A tesztértesítés továbbítja a StorSimple Device Manager szolgáltatás jelennek-állapotüzenetek.

    ![a riasztási beállítások](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Megjelenik egy értesítés, ha a teszt e-mailt küld. 
   
    ![Riasztások által küldött értesítő e-mail tesztelése](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > A teszt értesítési üzenet nem küldhető el, ha a StorSimple Device Manager szolgáltatás megfelelő hibaüzenet jelenik meg. Várjon néhány percet, és próbálja meg újra elküldeni az értesítési tesztüzenet. 

5. A konfiguráció befejezése után kattintson **mentése**. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Riasztások által küldött értesítő e-mail tesztelése](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Megtekintheti, és nyomon követéséhez értesítések

A StorSimple Device Manager szolgáltatás összefoglaló panel tesz lehetővé az eszközök, súlyossági szint szerint a riasztások számának kiadványok.

![Riasztások irányítópult](./media/storsimple-8000-manage-alerts/device-summary4.png)

Milyen súlyossági szintről kattintva megnyílik a **riasztások** panelen. Az eredmények csak az adott súlyossági szint megfelelő riasztások tartalmazzák.

Kattintson a listában riasztást biztosít további részleteket a riasztást, többek között a legutóbbi, a riasztás érkezett jelentés, az eszközön, és a riasztás feloldásához jelenítse meg a javasolt művelet a riasztás előfordulásainak száma. Ha a hardver-riasztások, azt is azonosítja a hardverösszetevő.

![Riasztási hardver – példa](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

A riasztás részleteit szövegfájlba másolhatja, ha elküldi az adatokat a Microsoft Support van szüksége. A javaslat követni, és feloldotta a riasztási feltétel a helyszíni, meg kell a jelet az eszközről a riasztást a riasztás kiválasztása a **riasztások** panel megnyitásához, és kattintson a **törölje**. Több riasztás törléséhez minden riasztás választásához kattintson a bármely oszlop, kivéve a **riasztás** oszlop, és kattintson **törölje** törölni kell minden riasztás kiválasztása után. Vegye figyelembe, hogy egyes riasztások automatikusan törlődnek a probléma megoldása után, vagy ha a rendszer frissíti a riasztást az új adatokkal.

Amikor rákattint **egyértelmű**, lesz lehetősége van arra, hogy a riasztás és a lépéseket, amelyek vett igénybe, ha a probléma megoldására vonatkozó megjegyzések. Egyes események törlődik a rendszer, ha egy másik esemény akkor váltódik ki, új információkkal. Ebben az esetben a következő üzenet jelenik meg.

![Törölje a riasztási üzenet](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>A rendezés és tekintse át a riasztások

Szükség lehet arra, hogy tekintse át, és törölje őket a csoportok a jelentések futtatásához riasztások hatékonyabb. Emellett a **riasztások** panel is legfeljebb 250 riasztások megjelenítése. Túllépte a riasztások számát is, ha nem az összes riasztás jelenik meg az alapértelmezett nézetben. Kombinálhatja testreszabása, mely riasztások jelennek meg a következő mezőket:

* **Állapot** – jelenítheti meg vagy **aktív** vagy **nincs bejelölve** riasztásokat. Aktív továbbra is folyamatban figyelmeztetéseket a rendszeren, amíg üres riasztások vagy manuálisan törölték egy rendszergazda, vagy programozott módon nincs bejelölve, mert a rendszer a riasztást kiváltó körülmény frissülnek az új adatokkal.
* **Súlyossági** – az összes súlyossági szint (kritikus, figyelmeztetés, tájékoztatás), vagy csak egy bizonyos súlyossági, például csak a kritikus riasztások riasztások jelenítheti meg.
* **Forrás** – forrásokból származó riasztások megjelenítése, vagy a riasztások körét származik, vagy a szolgáltatás vagy egy vagy több eszközt is korlátozható.
* **Időtartománynak** – megadásával a **a** és **való** dátumok és időbélyegeket, vessen egy pillantást riasztások érdekli időszakban.

![Riasztások listája](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Riasztások rövid összefoglalása

A következő táblázat a Microsoft Azure StorSimple riasztásokat, amelyek akkor léphetnek fel, valamint további információkra és ajánlásokra ahol az rendelkezésre áll olyan. A StorSimple eszköz riasztások egyet a következő kategóriákba sorolhatók:

* [Felhő csatlakozási riasztások](#cloud-connectivity-alerts)
* [Fürt riasztások](#cluster-alerts)
* [Vész-helyreállítási riasztások](#disaster-recovery-alerts)
* [Hardver riasztások](#hardware-alerts)
* [Figyelmeztetések](#job-failure-alerts)
* [Helyileg rögzített kötet riasztás](#locally-pinned-volume-alerts)
* [Hálózatkezelés riasztások](#networking-alerts)
* [Teljesítményével kapcsolatos riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)
* [Támogatási csomag riasztások](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhő csatlakozási riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Kapcsolat <*felhő hitelesítő adat neve*> nem hozható létre. |Nem lehet csatlakozni a tárfiókhoz. |Úgy tűnik, előfordulhat, hogy az eszköz csatlakozási probléma. Futtassa a `Test-HcsmConnection` azonosíthatja és megoldhatja a problémát az eszközön a Windows PowerShell-felületet a StorSimple parancsmagjával. A beállítások helyesek, ha a probléma lehet a tárfiók, amelyre a riasztás kiadását okozó hitelesítő adataival. Ebben az esetben használja a `Test-HcsStorageAccountCredential` parancsmagot, hogy vannak-e problémák megoldása.<ul><li>Ellenőrizze a hálózati beállításokat.</li><li>A tárfiók hitelesítő adatainak ellenőrzése.</li></ul> |
| Nem érkezett szívverés az eszközről az elmúlt <*szám*> perc. |Nem lehet csatlakozni az eszköz. |Úgy tűnik, az eszköz csatlakozási probléma van. Használja a `Test-HcsmConnection` parancsmag a Windows PowerShell-felületet a StorSimple eszközén azonosítására és hárítsa el a problémát, vagy forduljon a rendszergazdához. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple működés, ha a felhő-kapcsolat sikertelen

Mi történik, ha nem sikerül felhő kapcsolat az éles környezetben futó StorSimple eszköz?

Ha felhő kapcsolat nem sikerül, a StorSimple éles eszköze, majd az eszköz állapotától függően a következő történik:

* **Az eszközön a helyi adatok**: egy kis ideig lesz nem működőképes, és olvasási műveletek továbbra is jeleníthető meg. Azonban kiugró IO száma növekszik, és hossza meghaladja a korlátot, az olvasási műveletek elindítása volt sikertelen.

    Attól függően, hogy az adatok mennyisége az eszközön az írási műveletek továbbra is a felhő kapcsolat az első néhány óra a megszakítás után fordul elő. Az írási műveletek fog majd lelassul, és végül indítása sikertelen lesz, ha a felhő kapcsolat megszakad több órán keresztül. (Nincs ideiglenes tárolási adatokat, a felhő lehet leküldeni az eszközre. Ez a terület ki van ürítve, amikor az adatküldést. Kapcsolat sikertelen lesz, ha a tárolási terület adatokat nem lehet leküldeni a felhőbe, és IO sikertelen lesz.)
* **Az adatok a felhőben**: A legtöbb felhő kapcsolódási hibákat, hibát ad vissza. Ha a kapcsolat visszaállítására, az IOs folytatódnak anélkül, hogy a felhasználónak nem kell a kötet online állapotba. Bizonyos ritkán előforduló esetekben felhasználói beavatkozás lehet szükség, annak érdekében, ismét a kötet online az Azure portálról.
* **A folyamatban lévő felhőalapú pillanatfelvételek**: a rendszer néhány alkalommal belül 4-5 óra ismét megkísérli a művelet és a kapcsolat nem állítja vissza, ha a felhőalapú pillanatfelvételek sikertelen lesz.

### <a name="cluster-alerts"></a>Fürt riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Eszköz átadja a <*eszköznév*>. |Eszköz karbantartási módban van. |Eszköz keresztül bevitelét, vagy a karbantartási módból való kilépéssel miatt sikertelen volt. Ez nem jelent problémát, és nincs szükség beavatkozásra. Után ez a riasztás rendelkezik nyugtázott, törölje a jelet a riasztások lapján. |
| Eszköz átadja a <*eszköznév*>. |Eszköz belső vezérlőprogram vagy a szoftver csak frissült. |Hiba történt egy fürtön belüli feladatátvétel miatt egy frissítést. Ez nem jelent problémát, és nincs szükség beavatkozásra. Után ez a riasztás rendelkezik nyugtázott, törölje a jelet a riasztások lapján. |
| Eszköz átadja a <*eszköznév*>. |A tartományvezérlő le volt leállt vagy újraindult. |Eszköz keresztül nem sikerült, mert az aktív vezérlővel leállt vagy egy rendszergazda újra. Nincs szükség beavatkozásra. Után ez a riasztás rendelkezik nyugtázott, törölje a jelet a riasztások lapján. |
| Eszköz átadja a <*eszköznév*>. |Tervezett feladatátvétel. |Győződjön meg arról, hogy ez volt a tervezett feladatátvételt. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján. |
| Eszköz átadja a <*eszköznév*>. |Nem tervezett feladatátvételt. |StorSimple-t automatikusan helyreállni a nem tervezett feladatátvételt. Ha ezek a riasztások nagy számú látja, forduljon a Microsoft Support. |
| Eszköz átadja a <*eszköznév*>. |Egyéb/ismeretlen ok. |Ha ezek a riasztások nagy számú látja, forduljon a Microsoft Support. Miután a probléma megoldódott, törölje ezt a riasztást a riasztások lapon. |
| Egy kritikus eszközök szolgáltatás azt jelenti állapota sikertelen. |DataPath szolgáltatás hibája. |Segítségért forduljon a Microsoft Support. |
| Virtuális hálózati adapter IP-címét <*DATA #*> Jelentések állapota sikertelen. |Egyéb/ismeretlen ok. |Egyes esetekben ideiglenes okok miatt jöhet létre ezeket a riasztásokat. Ha ez a helyzet, majd ezt a riasztást automatikusan törölve lesz egy kis idő múlva. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. |
| Virtuális hálózati adapter IP-címét <*DATA #*> Jelentések állapota sikertelen. |A kapcsolat nevét: <*DATA #*> IP-cím <IP address> nem kell online állapotba, mert egy ismétlődő IP-címet észlelt a hálózaton. |Győződjön meg arról, hogy az ismétlődő IP-cím van-e távolítani a hálózatról, vagy konfigurálja újra a egy másik IP-című kapcsolaton. |

### <a name="disaster-recovery-alerts"></a>Vész-helyreállítási riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Helyreállítási művelet nem tudta visszaállítani a beállítások ezt a szolgáltatást. Eszköz konfigurációs adatokat az egyes eszközök esetében inkonzisztens állapotban van. |Adatok inkonzisztencia észlelhető katasztrófa utáni helyreállítás után. |A szolgáltatás a titkosított adatok nincs szinkronizálva, az eszközön. Engedélyezi az eszköz <*eszköznév*> a StorSimple eszköz Manager a szinkronizálás megkezdéséhez. A Windows PowerShell-felületet a StorSimple segítségével futtathatja a `Restore-HcsmEncryptedServiceData` eszközön <*eszköznév*> parancsmag, ez a parancsmag csak a biztonsági profil visszaállítása bemenetként a régi jelszó megadása. Ezután futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmaggal frissítsen a szolgáltatásadat-titkosítási kulcs. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján. |

### <a name="hardware-alerts"></a>Hardver riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Hardverösszetevő <*összetevő-azonosító*> állapotának jelentések <*állapot*>. | |Egyes esetekben ideiglenes okok miatt jöhet létre ezeket a riasztásokat. Ha igen, ez a riasztás automatikusan törölve lesz egy kis idő múlva. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. |
| Passzív vezérlő hibásan működik. |A passzív (másodlagos) vezérlő nem működik. |Az eszköz működik, de a vezérlőket hibásan működik. Indítsa újra, hogy az adott. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support. |

### <a name="job-failure-alerts"></a>Figyelmeztetések

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| A biztonsági mentési <*kötet csoport forrásazonosító*> nem sikerült. |Biztonsági mentési feladat sikertelen volt. |Kapcsolódási problémák megakadályozzák, hogy a biztonsági mentési művelet sikeres befejezését. Nincsenek kapcsolódási problémák, ha lehetséges, hogy túllépte a biztonsági másolatok maximális számát. Törölje az összes biztonsági másolatot, amely már nem szükséges, és próbálja megismételni a műveletet. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján. |
| A klón <*adatforrás biztonsági mentési elem azonosítója*> a <*cél kötet sorozatszámokat*> nem sikerült. |Klónozás feladat sikertelen volt. |Frissítse a biztonsági mentési listáját annak ellenőrzéséhez, hogy a biztonsági mentés továbbra is érvényes. Ha a biztonsági mentés érvényes, akkor lehetséges, hogy a felhő kapcsolódási problémák akadályozzák a Klónozási művelet sikeres befejezését. Nincsenek kapcsolódási problémák, ha lehetséges, hogy túllépte a tárolási korlátját. Törölje az összes biztonsági másolatot, amely már nem szükséges, és próbálja megismételni a műveletet. Miután elvégezte a megfelelő művelettel hárítsa el a problémát, törölje ezt a riasztást a riasztások lapján. |
| Állítsa vissza a <*adatforrás biztonsági mentési elem azonosítója*> nem sikerült. |Állítsa vissza a feladat sikertelen volt. |Frissítse a biztonsági mentési listáját annak ellenőrzéséhez, hogy a biztonsági mentés továbbra is érvényes. Ha a biztonsági mentés érvényes, akkor lehetséges, hogy a felhő kapcsolódási problémák akadályozzák a visszaállítási folyamat sikeres végrehajtása. Nincsenek kapcsolódási problémák, ha lehetséges, hogy túllépte a tárolási korlátját. Törölje az összes biztonsági másolatot, amely már nem szükséges, és próbálja megismételni a műveletet. Miután elvégezte a megfelelő művelettel hárítsa el a problémát, törölje ezt a riasztást a riasztások lapján. |

### <a name="locally-pinned-volume-alerts"></a>Helyileg rögzített kötet riasztás

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Helyi kötet létrehozása <*kötetneve*> nem sikerült. |A kötet-létrehozási feladat sikertelen volt. <*A sikertelen hibakód megfelelő hibaüzenet*>. |Kapcsolódási problémák megakadályozzák, hogy a hely létrehozási művelet sikeres befejezését. Helyileg rögzített kötet kiosztása és a hely létrehozása magában foglalja a felhőbe a rétegzett kötetek kiömlést. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy a helyi terület az eszközön kimerített. Határozza meg, hogy terület létezik-e az eszközön mielőtt megpróbálná megismételni a műveletet. |
| Helyi kötet bővítése <*kötetneve*> nem sikerült. |A kötet módosítása feladat nem tudta miatt <*hibaüzenet jelenik meg a sikertelen hibakód megfelelő*>. |Kapcsolódási problémák megakadályozzák, hogy a kötet bővítése művelet sikeres befejezését. Helyileg rögzített kötet kiosztása és a meglévő terület kiterjesztése magában foglalja a felhőbe a rétegzett kötetek kiömlést. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy a helyi terület az eszközön kimerített. Határozza meg, hogy terület létezik-e az eszközön mielőtt megpróbálná megismételni a műveletet. |
| Kötet konvertálása <*kötetneve*> nem sikerült. |A kötet átalakítási feladat is alakítható át a kötet típusa a helyileg rögzített rétegzett sikertelen. |Rétegzett kötet konverzió helyileg rögzített típusa nem lehet végrehajtani. Győződjön meg arról, hogy nincsenek-e a művelet sikeres befejezését kapcsolódási problémák. Hibaelhárítási kapcsolódási problémák Ugrás [kapcsolatos problémák elhárítása a Test-HcsmConnection parancsmag](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Az eredeti helyileg rögzített kötet most jelöléssel lett a rétegzett kötetek óta a helyileg rögzített kötet adatok egy részét rendelkezik kiömlött a felhőbe a konverzió során. Az eredő rétegzett kötet még mindig van elfoglaló helyi terület az eszközön, amely nem nyerhető a későbbi helyi kötetekhez.<br>Hárítson el minden kapcsolódási problémát, törölje a riasztást, és alakítani a kötetet az eredeti helyileg rögzített kötet típus minden az adatok helyben újra elérhetővé biztosításához. |
| Kötet konvertálása <*kötetneve*> nem sikerült. |A kötet átalakítási feladat is alakítható át a kötet típusa a rétegzett a helyileg rögzített nem sikerült. |A kötet-típusra történő konvertálást rétegzett a helyileg rögzített nem fejezhető be. Győződjön meg arról, hogy nincsenek-e a művelet sikeres befejezését kapcsolódási problémák. Hibaelhárítási kapcsolódási problémák Ugrás [kapcsolatos problémák elhárítása a Test-HcsmConnection parancsmag](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Az eredeti rétegzett kötet most megjelölve egy helyileg rögzített kötet az átalakítási folyamat része továbbra is fennáll, hogy az adatok a felhőben található, amíg a thickly kiosztott lemezterület az eszközön a kötet már nem szabadítható fel a jövőbeni helyi kötetekhez.<br>Hárítson el minden kapcsolódási problémát, törölje a riasztást, és alakítani a kötetet az eredeti rétegzett kötet típus annak érdekében, hogy az eszközön kiosztása helyi terület szabadítható fel. |
| Helyi pillanatképeinek helyi lemezterület-felhasználást hamarosan <*kötet csoport neve*> |A biztonsági mentési házirend helyi pillanatképeket előfordulhat, hogy hamarosan elfogy a helyet, és érvényteleníti állomás írási hibák elkerülése érdekében. |A biztonsági mentési házirend csoporthoz tartozó köteteken lévő magas adatforgalommal mellett gyakori helyi pillanatképeket helyi terület okozó gyorsan fel az eszközön. Törölje a meglévő helyi pillanatképeket, amely már nem szükséges. Frissítse a helyi pillanatkép ütemezése, a ritkábban helyi pillanatképeket, és győződjön meg arról, hogy felhő pillanatfelvételeket készít rendszeresen biztonsági mentési házirend. Ezeket a műveleteket a rendszer nem hajtja végre, ha ezek a pillanatképek helyi terület amint lehet, hogy kimerül, és a rendszer automatikusan törli őket, annak érdekében, hogy állomás írások továbbra is importálni. |
| A pillanatképek helyi <*kötet csoportnév*> érvénytelenítve lett. |A helyi pillanatképeket létrehozni a(z) <*kötet csoportnév*> érvénytelenítve és majd törölve, mert azok volt meghaladja a helyi terület az eszközön. |Győződjön meg arról, ez nem ismétlődnek a jövőben, tekintse át a helyi pillanatfelvétel ütemezések a biztonsági mentési házirend, és törölje a meglévő helyi pillanatképeket, amely már nem szükséges. A biztonsági mentési házirend csoporthoz tartozó köteteken lévő magas adatforgalommal mellett gyakori helyi pillanatképeket okozhat helyi terület gyorsan fel az eszközön. |
| Állítsa vissza a <*adatforrás biztonsági mentési elem azonosítója*> nem sikerült. |A helyreállítási feladat sikertelen volt. |Ha helyileg rögzített vagy a biztonsági mentési házirend, a helyileg rögzített és a rétegzett kötetek vegyesen annak ellenőrzésére, hogy a biztonsági mentési listájának frissítése a biztonsági mentés továbbra is érvényes lesz. Ha a biztonsági mentés érvényes, akkor lehetséges, hogy a felhő kapcsolódási problémák akadályozzák a visszaállítási folyamat sikeres végrehajtása. A helyileg rögzített kötetekhez a pillanatkép-csoport részeként helyreállított alatt nem rendelkeznek az összes, az adatokat, le kell az eszközt, és ha rétegzett és helyileg rögzített kötetek vegyesen a pillanatkép-csoportban található, nem fogja szinkronban vannak egymással. A visszaállítási művelet sikeresen befejeződik, a köteteket a csoport offline állapotba a gazdagépen a hálózatról, és próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a kötet adatait a visszaállítási folyamat során végrehajtott módosításai elvesznek. |

### <a name="networking-alerts"></a>Hálózatkezelés riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Nem lehetett elindítani a StorSimple szolgáltatás(oka)t. |DataPath hiba |Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |
| Ismétlődő IP-cím észlelhető a következőnél: "Data0". | |A rendszer észlelte az IP-cím "10.0.0.1" ütközés. Az eszközön a hálózati erőforrás "Data0"  *<device1>*  offline állapotban. Győződjön meg arról, hogy az IP-címet nem használja ezt a hálózatot minden más entitás. Hálózati probléma elhárításához lépjen [kapcsolatos problémák elhárítása a Get-NetAdapter parancsmaggal](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). A probléma megoldásához forduljon a rendszergazdához. Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |
| IPv4-(vagy IPv6) címmel "Data0" offline állapotban. | |A hálózati erőforrás "Data0" IP-cím "10.0.0.1." előtag hossza "22" az eszközön és  *<device1>*  offline állapotban. Gondoskodjon arról, hogy a kapcsoló portjaihoz, amelyhez az illesztő csatlakozik működési. Hálózati probléma elhárításához lépjen [kapcsolatos problémák elhárítása a Get-NetAdapter parancsmaggal](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nem sikerült kapcsolódni a hitelesítési szolgáltatás. |DataPath hiba |A URLthat hitelesítéséhez használt nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazza-e a StorSimple eszközhöz megadott URL-mintával. Az Azure portál URL-mintával további információkért látogasson el https://aka.ms/ss-8000-network-reqs. Ha Azure Government felhő, nyissa meg az URL-cím minták https://aka.ms/ss8000-gov-network-reqs használja.|

### <a name="performance-alerts"></a>Teljesítményével kapcsolatos riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Az eszköz terhelés túllépte <*küszöbérték*>. |Lassabb, mint a várt válaszidejét. |Az eszköz a bemeneti/kimeneti túlterheltség kihasználtsági jelentés. Ez az eszköz nem működik, valamint legyen, vagy ha okozhat. Tekintse át a munkaterheléseket, hogy az eszköz van csatolva, és határozza meg, hogy vannak-e, hogy sikerült áthelyezni egy másik eszközt vagy, amely már nincs szükség.| Nem lehetett elindítani a StorSimple szolgáltatás(oka)t. |DataPath hiba |Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |és aktuális állapotát, nyissa meg a [a StorSimple Device Manager szolgáltatás használata az eszköz figyelése](storsimple-monitor-device.md) |

### <a name="security-alerts"></a>Biztonsági riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Microsoft Support munkamenet már megkezdődött. |Külső támogatási munkamenet érhető el. |Ellenőrizze, hogy a hozzáférés engedélyezése. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján. |
| A jelszó <*elem*> múlva le fog járni <*mennyi ideig*>. |Jelszó érvényessége hamarosan eléri. |Változtassa meg jelszavát még a lejárta előtt. |
| A hiányzó biztonsági konfigurációadatait <*Elemazonosító*>. | |A kötettároló társított kötetek nem használható a StorSimple-konfiguráció. Győződjön meg arról, hogy biztonságosan tárolja az adatokat, azt javasoljuk, hogy törli a kötettároló és a kötettároló társított köteteket. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján. |
| <*szám*> a sikertelen bejelentkezési kísérletek <*Elemazonosító*>. |Több sikertelen bejelentkezési kísérletek. |Lehet, hogy az eszközét támadás érte, vagy egy jogosulatlan felhasználó helytelen jelszóval próbál csatlakozni.<ul><li>Lépjen kapcsolatba a jogosult felhasználók, és győződjön meg arról, hogy van-e ezeket a kísérleteket megbízható forrásból. Ha továbbra is találkozik nagy számú sikertelen bejelentkezési kísérlet, fontolja meg a Távfelügyelet letiltása, és a hálózati rendszergazda. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján.</li><li>Ellenőrizze, hogy a helyes jelszót a Snapshot Manager példányai vannak konfigurálva. Miután elvégezte a megfelelő műveletet, törölje ezt a riasztást a riasztások lapján.</li></ul>További információkért látogasson el [lejárt eszköz jelszó módosítása](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Egy vagy több hiba történt a szolgáltatásadat-titkosítási kulcs módosítása közben. | |A szolgáltatásadat-titkosítási kulcs módosítása során hibák történtek. Miután a hibaállapotok foglalkoztak, futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` frissíti a szolgáltatást az eszközön a Windows PowerShell-felületet a StorSimple parancsmagjával. Ha nem szűnik meg a hiba, forduljon a Microsoft támogatási szolgálatához. Ha elhárította a problémát, törölje ezt a riasztást a riasztások lapon. |

### <a name="support-package-alerts"></a>Támogatási csomag riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült létrehozni a támogatási csomag. |StorSimple nem tudta létrehozni a csomagot. |Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. Miután a probléma megoldódott, törölje ezt a riasztást a riasztások lapon. |

## <a name="next-steps"></a>Következő lépések

További információ [StorSimple hibák és működési eszköz hibaelhárítása](storsimple-troubleshoot-operational-device.md).

