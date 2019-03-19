---
title: A StorSimple 8000 sorozatú eszköz riasztások megtekintése és kezelése |} A Microsoft Docs
description: Ismerteti a StorSimple riasztási feltételeket és a súlyosság, a riasztási értesítések konfigurálása és a riasztások kezelése a StorSimple-Eszközkezelő szolgáltatás használatával.
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
ms.workload: NA
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 4128ad53f30e74fb72f6256b78587cc222f4c282
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838714"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a StorSimple-riasztások megtekintése és kezelése

## <a name="overview"></a>Áttekintés

A **riasztások** a StorSimple-Eszközkezelő szolgáltatás paneljén lehetővé teszi, hogy tekintse át, és törölje a valós idejű alapon StorSimple eszköz – kapcsolatos riasztásokat. Erről a panelről központilag figyelheti a StorSimple-eszközök és a teljes Microsoft Azure StorSimple megoldás az állapotbeli problémák.

Ez az oktatóanyag ismerteti a gyakori riasztási feltételeket, a riasztások súlyossági szintjei és a riasztási értesítések konfigurálása. Továbbá a riasztás rövid összefoglaló táblát is, amelyeket gyorsan keresse meg az adott riasztásra, és megfelelő választ tartalmazza.

![Riasztások lap](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple-eszköz válaszként feltételek különböző riasztásokat állít elő. Az alábbiakban a leggyakoribb riasztási feltételeket:

* **Hardverproblémák** – ezek a riasztások mutassa be, a hardver állapotát. Segítségével tudja, hogy a belső vezérlőprogramok frissítése van szükség, ha egy hálózati adapter problémákkal rendelkezik, vagy ha a probléma az adatmeghajtók egyikével.
* **Kapcsolódási problémák** – ezek a riasztások fordulhat elő, ha nehézségekbe ütközik az adatok átvitele. Kommunikációs előfordulhatnak problémák az adatok átvitel során, és az Azure storage-fiókból vagy az eszközök és a StorSimple-Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs problémák az egyes nagyon nehéz javításához, mert vannak olyan sok meghibásodási pontokat. Először mindig ellenőrizze, hogy hálózati kapcsolatot és az Internet-hozzáférés érhetők el speciális hibaelhárítás be a folytatás előtt. Segítség a hibaelhárításban, [hibaelhárítás a Test-Connection parancsmaggal](storsimple-8000-troubleshoot-deployment.md).
* **Teljesítménnyel kapcsolatos problémák** – ezek a riasztások okozott végrehajtásakor a rendszer nem optimális, például a nagy terhelés alatt van.

Ezenkívül láthatja a biztonság, a frissítések és a sikertelen feladatok kapcsolatos riasztások.

## <a name="alert-severity-levels"></a>Riasztások súlyossági szintjei

Riasztások különböző fontossági szintek, attól függően, a hatást, amelyet a riasztás helyzet fog rendelkezni, illetve a riasztásra adott válasz nincs szükség van. A súlyossági szintek a következők:

* **Kritikus fontosságú** – Ez a riasztás egy feltételt, amely befolyásolja a rendszer teljesítményét a sikeres válasz szerepel. TEENDŐ ahhoz, hogy a StorSimple szolgáltatás ne szakadjon meg.
* **Figyelmeztetés** – Ez az állapot kritikus fontosságú, ha nem szűnik meg válhat. Vizsgálja meg a helyzet kell, és semmit sem kell törölnie a problémát.
* **Információk** – Ez a riasztás tartalmaz információkat, amelyek hasznosak lehetnek a követés és a rendszer kezelése.

## <a name="configure-alert-settings"></a>A riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretné-e minden a StorSimple eszközhöz tartozó riasztási feltételek e-mailben értesítést. Ezenkívül azonosítsa a más riasztási értesítés címzettjeinek írja be az e-mail címüket a **más e-mail-címzettek** mezőbe pontosvesszővel elválasztva.

> [!NOTE]
> Megadhat egy legfeljebb 20 e-mail címet eszközönként.

Miután engedélyezte az eszköz e-mail-értesítések, a notification lista tagjainak minden alkalommal, amikor egy kritikus riasztás esetén e-mail üzenetet fog kapni. Az üzeneteket küld a *storsimple-riasztások-noreply\@mail.windowsazure.com* és leírja a riasztási feltételt. Rákattinthat a címzettek **Unsubscribe** eltávolítása magukat az e-mail értesítési listája.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>A riasztások az eszköz e-mail-értesítés engedélyezése
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Eszközök listájából válassza ki, és kattintson a konfigurálni kívánt eszközre.
2. Lépjen a **beállítások** > **általános** az eszközhöz.

   ![Riasztások panel](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Az a **általános beállítások** panelen lépjen a **riasztási beállítások** , és állítsa be a következőket:
   
   1. Az a **e-mail-értesítés küldése** mezőben válassza **Igen**.
   2. Az a **E-mail-szolgáltatás-rendszergazdák** mezőben válassza **Igen** , a szolgáltatás-rendszergazda rendelkezik, és az összes többi rendszergazda is megkapja a riasztási értesítéseket.
   3. Az a **más e-mail-címzettek** mezőbe írja be annak az összes többi címzett, akiknek meg kell kapniuk a riasztási értesítéseket. A következő formátumban adja meg a neveket *valaki\@somewhere.com*. Az e-mail címeket pontosvesszővel. Legfeljebb 20 e-mail-címet eszközönként konfigurálhatja. 
      
3. Egy teszt e-mail értesítés küldése, kattintson a **tesztelő e-mail küldése**. A StorSimple-Eszközkezelő szolgáltatás állapotüzenetek jelenik meg, akkor továbbítja a tesztértesítés.

    ![Riasztás beállításai](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Megjelenik egy értesítés, amikor a teszt e-mailt küld. 
   
    ![Figyelmeztetéseket küldött értesítő e-mail tesztelése](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Nem lehet elküldeni az értesítési tesztüzenet, ha a StorSimple-Eszközkezelő szolgáltatás egy megfelelő hibaüzenet jelenik meg. Várjon néhány percet, és próbálja meg újból elküldeni az értesítési tesztüzenet. 

5. Miután végzett a konfiguráció, kattintson a **mentése**. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Figyelmeztetéseket küldött értesítő e-mail tesztelése](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén biztosít intézhet az eszközökön a súlyossági szint szerint rendezett riasztások számát.

![Riasztások irányítópult](./media/storsimple-8000-manage-alerts/device-summary4.png)

Ha az a súlyossági szintet kattint, megnyílik a **riasztások** panelen. Az eredmények tartalmazzák a csak azokat a riasztásokat, amelyek megfelelnek a súlyossági szintet.

Kattintson a listában riasztást biztosít további részleteket a riasztás, beleértve az utolsó időpont, a riasztás lett jelentve, az eszköz és a javasolt művelet a riasztás feloldásához jelenítse meg a riasztás előfordulásainak száma. Ha egy hardver riasztás, akkor is azonosítja a hardverösszetevő.

![Hardver példája](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Egy szövegfájlba másolhatja a riasztás részleteit, ha elküldi a Microsoft Support kell. Miután követni a javaslat és feloldva a riasztási feltételt a helyszínen, törölje a riasztást az eszközről a riasztás kiválasztásával a **riasztások** panelre, majd kattintson **törölje a jelet**. Több riasztás törléséhez jelölje ki minden riasztást, kattintson bármely másik oszlopot kivéve a **riasztás** oszlop, és kattintson **törölje a jelet** törölni kell minden riasztás kiválasztása után. Vegye figyelembe, hogy egyes riasztások automatikusan törlődnek, ha a probléma megoldódott, vagy amikor a rendszer frissíti a riasztást az új adatokkal.

Amikor rákattint **egyértelmű**, meg a riasztás és a lépéseket, akkor a probléma megoldásához igénybe vett kapcsolatos megjegyzéseit lehetőséget kell. Néhány esemény törlődik a rendszer, ha egy másik eseményt akkor indítja el az új adatokkal. Ebben az esetben látni fogja a következő üzenetet.

![Törölje a jelet riasztási üzenet](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Rendezés, és tekintse át a riasztások

Azt tapasztalhatja, még hatékonyabbá teszi a jelentések futtatásához a riasztásokat, hogy tekintse át, és törölje őket a csoportokba. Ezenkívül a **riasztások** panelen legfeljebb 250 riasztások tudja megjeleníteni. A riasztások száma túllépte az, ha nem az összes riasztás megjelenik az alapértelmezett nézet. Kombinálhatja a következő mezőket, testreszabása a riasztások jelennek meg:

* **Állapot** – megjelenítheti vagy **aktív** vagy **nincs bejelölve** riasztásokat. Az Active továbbra is folyamatban van a figyelmeztetéseket a rendszerén, amíg átesett riasztások vagy manuálisan törölték a rendszergazda által, vagy programozott módon törölve, mert a rendszer a riasztási feltétel frissülnek az új adatokkal.
* **Súlyosság** – megjelenítheti az összes súlyossági szint (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos fontosság, például csak a kritikus riasztások riasztásokat.
* **Forrás** – riasztások megjelenítésének kezdete forrásokból, vagy korlátozza a riasztásokat, amelyek vagy a szolgáltatás vagy egy vagy több eszközök származnak.
* **Időtartomány** – megadásával a **a** és **való** dátum- és időbélyegek, tekintse meg riasztások során az Önt érdeklő időszakot.

![Riasztások listája](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Riasztásainak rövid összefoglalója

Az alábbi táblázatok sorolják fel, amelyek akkor fordulhatnak, valamint a további információk és javaslatok, ha elérhetők a Microsoft Azure StorSimple riasztásokat. A StorSimple eszköz riasztások az alábbi kategóriák valamelyikébe tartoznak:

* [Felhőbeli csatlakozási riasztások](#cloud-connectivity-alerts)
* [Fürt riasztások](#cluster-alerts)
* [Vész-helyreállítási riasztások](#disaster-recovery-alerts)
* [Hardver-riasztások](#hardware-alerts)
* [Feladat miatti riasztások](#job-failure-alerts)
* [Helyileg rögzített kötet riasztások](#locally-pinned-volume-alerts)
* [Hálózatkezelés riasztások](#networking-alerts)
* [Az alkalmazásteljesítmény miatti riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)
* [Támogatási csomag riasztások](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőbeli csatlakozási riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A kapcsolat <*felhőbeli hitelesítő adat neve*> nem hozható létre. |Nem lehet csatlakozni a tárfiókhoz. |Úgy tűnik, előfordulhat, hogy kapcsolódási probléma adódott az eszközt. Futtassa a `Test-HcsmConnection` parancsmagot a storsimple-höz készült Windows PowerShell interfészről azonosíthatja és megoldhatja a problémát az eszközön. A beállítások helyességét, ha a probléma lehet, amelynek a riasztás kiadását okozó storage-fiók hitelesítő adatait. Ebben az esetben használja a `Test-HcsStorageAccountCredential` parancsmag használatával határozza meg, hogy vannak-e megoldható problémák.<ul><li>Ellenőrizze a hálózati beállításokat.</li><li>Ellenőrizze a tárfiók hitelesítő adatait.</li></ul> |
| Nem érkezett szívverés az eszközétől az utolsó <*szám*> perc. |Nem lehet csatlakozni az eszközhöz. |Úgy tűnik, hogy kapcsolódási probléma adódott az eszközt. Használja a `Test-HcsmConnection` parancsmagot a storsimple-höz készült Windows PowerShell interfészről az eszköz azonosítására és a probléma megoldásához, vagy forduljon a rendszergazdához. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>A StorSimple viselkedés, amikor a cloud-kapcsolat sikertelen

Mi történik, ha nem sikerül a cloud-kapcsolat az éles környezetben futó StorSimple eszköz?

Ha felhőkapcsolatra meghiúsul üzemi a StorSimple-eszköz, majd az eszköz állapotától függően a következő történik:

* **Az eszközön a helyi adatok**: Egy kis ideig nem lesznek megszakítása nélkül, és olvasási továbbra is szeretne kiszolgálni. Azonban, a szálankénti függőben lévő IOS-es száma növekszik, és meghaladja a határértéket, az olvasások indítható sikertelen lesz.

    Az eszközön lévő adatok mennyiségétől függően az írási műveletek továbbra is az első néhány óra után a megszakítások időtartamát a felhőkapcsolatra fordulnak elő. Az írások majd lelassulnak, és a végül indítsa el a meghiúsuljon, ha a cloud-kapcsolat megszakad, több órán keresztül. (Nincs ideiglenes tárhely az eszközön az adatokat, amelyekről lehet leküldeni a felhőbe. Ez a terület ki van ürítve, ha elküldi az adatokat. Ha a kapcsolat nem sikerül, nem lesz küldve a felhőbeli adatokat a tárolás területén, és sikertelen lesz, i/o.)
* **Az adatok a felhőben**: A legtöbb felhőalapú kapcsolódási hibákat hibát ad vissza. Miután a kapcsolat helyreáll, az IOS-es folytatódnak anélkül, hogy a felhasználónak nem kell a kötet online állapotba. Ritka esetekben felhasználói beavatkozás lehet szükség a vissza a kötet online állapotba az Azure Portalról.
* **A felhőbeli pillanatképekkel folyamatban**: A rendszer 4 – 5 órán belül néhány alkalommal újra megpróbálja a műveletet, és a kapcsolat nem áll vissza, ha a felhőbeli pillanatképekkel sikertelen lesz.

### <a name="cluster-alerts"></a>Fürt riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Az eszköz feladatátvételére <*eszköznév*>. |Eszköz karbantartási módban van. |Eszköz keresztül belépő vagy a karbantartási módból való kilépéssel miatt nem sikerült. Ez nem jelent problémát, és nem kell módosítania. Miután tudomásul vette a riasztást, törölje a riasztások oldaláról. |
| Az eszköz feladatátvételére <*eszköznév*>. |Eszköz belső vezérlőprogramjának vagy a szoftver csak frissítve lett. |Fürt-feladatátvétel miatt frissítés történt. Ez nem jelent problémát, és nem kell módosítania. Miután tudomásul vette a riasztást, törölje a riasztások oldaláról. |
| Az eszköz feladatátvételére <*eszköznév*>. |Vezérlőt leállítása vagy újraindítása. |Eszköz feladatátvételére, mert az aktív vezérlőn leállította vagy újraindította a rendszergazda. Nincs szükség beavatkozásra. Miután tudomásul vette a riasztást, törölje a riasztások oldaláról. |
| Az eszköz feladatátvételére <*eszköznév*>. |Tervezett feladatátvétel. |Győződjön meg arról, hogy a tervezett feladatátvétel volt. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról. |
| Az eszköz feladatátvételére <*eszköznév*>. |Nem tervezett feladatátvételt. |A StorSimple épül, hogy automatikusan helyreálljon a nem tervezett feladatátvételeket. Ha nagy számú ilyen riasztást lát, forduljon a Microsoft Support. |
| Az eszköz feladatátvételére <*eszköznév*>. |Ismeretlen/egyéb ok. |Ha nagy számú ilyen riasztást lát, forduljon a Microsoft Support. Miután a probléma megoldódott, törölje a riasztást a riasztások oldaláról. |
| Egy kritikus fontosságú szolgáltatás sikertelenként állapotjelentést. |DataPath szolgáltatás hibája. |Segítségért forduljon a Microsoft Support. |
| Virtuális hálózati adapter IP-címet <*DATA #*> állapotjelentést, mert nem sikerült. |Ismeretlen/egyéb ok. |Néha átmeneti körülmények okozhatják ezek a riasztások. Ha ez a helyzet, majd ezt a riasztást automatikusan törlődik egy kis idő múlva. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. |
| Virtuális hálózati adapter IP-címet <*DATA #*> állapotjelentést, mert nem sikerült. |Kapcsolat neve: <*DATA #*> IP-cím <IP address> nem hozható online állapotba, mert ismétlődő IP-címet észlelt a hálózaton. |Győződjön meg arról, hogy az ismétlődő IP-cím van-e távolítani a hálózatról, vagy konfigurálja újra a felület másik IP-címmel. |

### <a name="disaster-recovery-alerts"></a>Vész-helyreállítási riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A helyreállítási műveletek nem sikerült visszaállítani az összes beállítás ehhez a szolgáltatáshoz. Eszközkonfigurációs adatok inkonzisztens állapotúak néhány eszköz szerepel. |Adatok inkonzisztenciát észlelt katasztrófa utáni helyreállítás után. |A szolgáltatás a titkosított adatok nincs szinkronizálva, az eszközön. Engedélyezze az eszköz <*eszköznév*> a StorSimple-Eszközkezelő a szinkronizálási folyamat elindításához. A Windows PowerShell felületét a storsimple segítségével futtathatja a `Restore-HcsmEncryptedServiceData` eszközön <*eszköznév*> parancsmagot, a régi jelszó megadása a biztonsági profil visszaállításához a parancsmag bemeneteként. Ezután futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmag, amellyel frissíthetőek a szolgáltatásadat-titkosítási kulcs. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról. |

### <a name="hardware-alerts"></a>Hardver-riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Hardverösszetevő <*összetevő-azonosító*> állapotjelentése <*állapot*>. | |Néha átmeneti körülmények okozhatják ezek a riasztások. Ha igen, ez a riasztás automatikusan törlődik egy kis idő múlva. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. |
| Passzív vezérlő üzemzavara. |A (másodlagos) a passzív vezérlő nem működik. |Az eszköz működőképes, de az egyik vezérlő működése hibás. Próbálja meg újraindítani a vezérlőt. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support. |

### <a name="job-failure-alerts"></a>Feladat miatti riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A biztonsági mentési <*kötet csoport forrásazonosító*> nem sikerült. |Biztonsági mentési feladat sikertelen volt. |Kapcsolódási problémák akadályozzák meg a biztonságimásolat-készítési művelet sikeres befejeződését. Ha nincsenek kapcsolódási problémák, lehet, hogy a biztonsági másolatok maximális számát elérte. Törölje minden olyan biztonsági mentéseket, hogy már nincs rá szükség, és próbálja megismételni a műveletet. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról. |
| Klónozás forrása: <*adatforrás biztonsági mentési elem azonosítók*>, <*cél kötet sorozatszámok*> nem sikerült. |A Klónozási nem sikerült. |Annak ellenőrzéséhez, hogy a biztonsági másolat még érvényes a biztonsági mentési lista frissítéséhez. Ha a biztonsági másolat érvényes, akkor lehet, hogy felhőbeli kapcsolódási problémák akadályozzák a Klónozási művelet sikeres befejeződését. Ha nincsenek kapcsolódási problémák, lehet, hogy a tárolási korlátot elérte. Törölje minden olyan biztonsági mentéseket, hogy már nincs rá szükség, és próbálja megismételni a műveletet. A probléma megoldásához a megfelelő művelet elvégzése után törölje ezt a riasztást a riasztások oldaláról. |
| A visszaállítás <*adatforrás biztonsági mentési elem azonosítók*> nem sikerült. |A visszaállítási feladat nem sikerült. |Annak ellenőrzéséhez, hogy a biztonsági másolat még érvényes a biztonsági mentési lista frissítéséhez. Ha a biztonsági másolat érvényes, akkor lehet, hogy felhőbeli kapcsolódási problémák akadályozzák a visszaállítási művelet sikeres befejeződését. Ha nincsenek kapcsolódási problémák, lehet, hogy a tárolási korlátot elérte. Törölje minden olyan biztonsági mentéseket, hogy már nincs rá szükség, és próbálja megismételni a műveletet. A probléma megoldásához a megfelelő művelet elvégzése után törölje ezt a riasztást a riasztások oldaláról. |

### <a name="locally-pinned-volume-alerts"></a>Helyileg rögzített kötet riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Helyi kötet létrehozása <*kötet neve*> nem sikerült. |A kötet-létrehozási feladat nem sikerült. <*Hibaüzenet jelenik meg a sikertelen hibakódnak megfelelő*>. |Kapcsolódási problémák akadályozzák meg a területlétrehozási művelet sikeres befejeződését. A gyors helyi kötetek nem dinamikus kiosztásúak, és a területlétrehozási folyamat magában foglalja a rétegzett kötetek a felhőbe való kiadását. Ha nincsenek kapcsolódási problémák, akkor előfordulhat, hogy kipróbálta a helyi területet az eszközön. Határozza meg a lemezterület van-e az eszközön, mielőtt megpróbálná megismételni a műveletet. |
| Helyi kötet bővítése <*kötet neve*> nem sikerült. |A kötet módosításának feladat miatt nem sikerült <*hibaüzenet jelenik meg a sikertelen hibakódnak megfelelő*>. |Kapcsolódási problémák akadályozzák meg a kötetkiterjesztési művelet sikeres befejeződését. A gyors helyi kötetek nem dinamikus kiosztásúak, és a meglévő terület kiterjesztésének folyamata magában foglalja a rétegzett kötetek a felhőbe való kiadását. Ha nincsenek kapcsolódási problémák, akkor előfordulhat, hogy kipróbálta a helyi területet az eszközön. Határozza meg a lemezterület van-e az eszközön, mielőtt megpróbálná megismételni a műveletet. |
| Kötet konvertálása <*kötet neve*> nem sikerült. |A kötettípus rétegzettről helyi átalakítása feladat rögzített rétegzett sikertelen. |Átalakítás típusa a helyileg rögzített kötet a rétegzett nem lehet befejezni. Győződjön meg arról, hogy nincsenek kapcsolódási problémák miatt a művelet sikeres befejeződését. Kapcsolat hibaelhárítási problémák Ugrás [hibaelhárítás a Test-HcsmConnection parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Az eredeti helyileg rögzített kötet már van megjelölve a rétegzett kötetek óta a helyileg rögzített kötet származó adatok némelyike rendelkezik kiömlött a felhőbe az átalakítás során. Az eredő rétegzett kötet még mindig van kötnek helyi terület az eszközön, amely nem nyerhető a későbbi helyi köteteken.<br>Hárítson el minden kapcsolódási problémát, törölje a riasztást, és a kötet konvertálása vissza az eredeti helyileg rögzített kötet típusa, ügyeljen rá, hogy az adatok elérhetővé helyileg újra. |
| Kötet konvertálása <*kötet neve*> nem sikerült. |Feladat a kötettípus rétegzettről konvertálása rétegzettről a helyileg rögzített nem sikerült. |Átalakítás típusa a kötet rétegzett a helyileg rögzített, nem fejeződött be. Győződjön meg arról, hogy nincsenek kapcsolódási problémák miatt a művelet sikeres befejeződését. Kapcsolat hibaelhárítási problémák Ugrás [hibaelhárítás a Test-HcsmConnection parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Az eredeti rétegzett kötet, az átalakítási folyamat része továbbra is fennáll, hogy az adatok a felhőben levő, amíg a thickly kiosztott terület az eszközön a kötet már nem igényelheti vissza jövőbeli helyi kötetek most megjelölve egy helyileg rögzített kötet.<br>Hárítson el minden kapcsolódási problémát, törölje a riasztást, és a kötet konvertálása vissza annak biztosítása érdekében a helyi terület az eszközön kiosztásúak igényelheti az eredeti rétegzett kötet típus. |
| Rendekezésre álló terület helyi pillanatkép-készítési <*kötet csoport neve*> |A biztonsági mentési szabályzat helyi pillanatképei előfordulhat, hogy hamarosan elfogy a hely és a gazdagép-írási hibák elkerülése érdekében érvényteleníti. |A gyakori helyi pillanatkép-készítés egy biztonsági mentési szabályzathoz társított kötetek adatváltozás okozó helyi terület gyorsan elfogy az eszközön. Törölje minden olyan helyi pillanatképet, amely már nincs rá szükség. Ezenkívül frissítse ritkábban forduljanak a helyi pillanatképeket, és győződjön meg arról, hogy felhőbeli pillanatképet készíteni az rendszeresen biztonsági mentési szabályzatot a helyi pillanatképek ütemezését. Ezeket a műveleteket a rendszer nem hajtja végre, ha ezek a pillanatképek helyi terület hamarosan kimerülhetnek, és a rendszer automatikusan törli őket, hogy gazdagép írási továbbra is sikeresen feldolgozta. |
| A helyi pillanatképek <*kötet csoportnév*> érvénytelenítve lett. |A helyi pillanatképei <*kötet csoportnév*> érvénytelenítve, és ezután törölni, mert ezek is meghaladja a helyi területet az eszközön. |Annak érdekében, hogy ez nem ismétlődnek a jövőben, tekintse át a biztonsági mentési szabályzatot a helyi pillanatképek ütemezését, és törölje az összes helyi pillanatképet, amely már nincs szükség. A gyakori helyi pillanatkép-készítés egy biztonsági mentési szabályzathoz társított kötetek adatváltozás okozhat a helyi terület gyorsan elfogy az eszközön. |
| A visszaállítás <*adatforrás biztonsági mentési elem azonosítók*> nem sikerült. |A visszaállítási feladat nem sikerült. |Ha helyileg rögzített vagy a biztonsági mentési házirend, a helyileg rögzített és a rétegzett kötetek kombinációját frissítse a biztonsági mentési listáját, ellenőrizheti, hogy a biztonsági mentés továbbra is érvényes lesz. Ha a biztonsági másolat érvényes, akkor lehet, hogy felhőbeli kapcsolódási problémák akadályozzák a visszaállítási művelet sikeres befejeződését. A helyreállított folyamatban van a pillanatkép-csoport részeként a gyors helyi kötetek nem rendelkeznek adatok letöltve az eszközre, és ha rétegzett és a helyileg rögzített kötetek kombinációját a pillanatkép-csoportban található, nem lesz szinkronban egymással. A visszaállítási művelet sikeres végrehajtásához a köteteket a csoport offline állapotba a gazdagépen, és próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a kötet adatait a visszaállítás során végzett módosításai elvesznek. |

### <a name="networking-alerts"></a>Hálózatkezelés riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Nem lehetett elindítani a StorSimple szolgáltatás(oka)t. |DataPath hiba |Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |
| Ismétlődő IP-cím észlelve a "Data0". | |A rendszer "10.0.0.1" IP-cím ütközést észlelt. Az eszköz a hálózati erőforrás "Data0" *<device1>* offline állapotban. Győződjön meg arról, hogy az IP-címet nem használja egyetlen más entitás sem ezen a hálózaton. Hálózati probléma elhárításához nyissa meg [hibaelhárítás a Get-NetAdapter parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). A probléma megoldásához forduljon a hálózati rendszergazdához. Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |
| IPv4-(vagy IPv6) 'Data0' címe offline állapotban. | |A hálózati erőforrás "Data0" IP-cím "10.0.0.1". előtag hossza "22" az eszközön, és *<device1>* offline állapotban. Győződjön meg arról, hogy a kapcsoló portjaihoz, amelyhez ez az interfész csatlakozik működési. Hálózati probléma elhárításához nyissa meg [hibaelhárítás a Get-NetAdapter parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nem sikerült csatlakozni a hitelesítési szolgáltatást. |DataPath hiba |A URLthat használatos a hitelesítés nem érhető el. Győződjön meg arról, hogy a tűzfalszabályokban szerepel-e a StorSimple eszközhöz megadott URL-mintával. További információ az Azure Portalon URL-minta, nyissa meg https://aka.ms/ss-8000-network-reqs. Azure Government Cloud használata esetén nyissa meg az URL-mintákra a https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Az alkalmazásteljesítmény miatti riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek | |
|:--- |:--- |:--- | --- |
| Az eszközterhelés túllépte <*küszöbérték*>. |Lassabb, mint a várható válaszidőt. |Az eszköz jelentések kihasználtsága nagy bemeneti/kimeneti terhelés alatt. Ez az eszköz nem működik jól, ahogyan kellene okozhat. Tekintse át a számítási feladatokat, hogy az eszköz van csatolva, és határozza meg, ha vannak ilyenek, amely egy másik eszközre sikerült áthelyezni, vagy amelyek már nem szükséges.|
| Nem lehetett elindítani a StorSimple szolgáltatás(oka)t. |DataPath hiba |Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A Microsoft Support munkamenete elkezdődött. |Külső támogatási munkamenet érhető el. |Erősítse meg, ez a hozzáférés engedélyezett. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról. |
| A jelszó <*elem*> le fog járni <*mennyi ideig*>. |Már majdnem elérte a jelszó lejárati idejét. |Változtassa meg jelszavát még a lejárta előtt. |
| A hiányzó biztonsági konfigurációs adatok <*Elemazonosító*>. | |Ehhez a kötettárolóhoz társított kötetek nem használható a StorSimple-konfiguráció replikálására. Győződjön meg arról, hogy az adatok biztonságosan tárolja, azt javasoljuk, hogy törölje a kötettárolót, és a kötettárolóhoz társított összes kötetet. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról. |
| <*szám*> sikertelen bejelentkezési kísérlet ehhez <*Elemazonosító*>. |Több sikertelen bejelentkezési kísérlet. |Lehet, hogy az eszközét támadás érte, vagy egy jogosulatlan felhasználó helytelen jelszóval próbál csatlakozni.<ul><li>Lépjen kapcsolatba a jogosultsággal rendelkező felhasználók, és győződjön meg arról, hogy voltak-e ezek a próbálkozások legitim forrásból. Ha továbbra is nagyszámú sikertelen bejelentkezési kísérletek megtekintéséhez, fontolja meg a Távfelügyelet letiltását, és vegye fel a kapcsolatot a hálózati rendszergazdához. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról.</li><li>Ellenőrizze, hogy a Snapshot Manager-példányok a megfelelő jelszóval vannak-e konfigurálva. Megfelelő művelet végrehajtása után törölje ezt a riasztást a riasztások oldaláról.</li></ul>További információért ugorjon [egy lejárt eszköz jelszavának módosítása](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Egy vagy több hiba történt a szolgáltatásadat-titkosítási kulcs módosítása közben. | |A szolgáltatásadat-titkosítási kulcs módosítása során hibák történtek. Rendelkezik, a hibaállapotok elhárítása után futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmagot a storsimple-höz készült Windows PowerShell interfészről az eszközön, a szolgáltatás frissítése. Ha nem szűnik meg a hiba, forduljon a Microsoft támogatási szolgálatához. Miután a probléma megoldásához, törölje a riasztást a riasztások oldaláról. |

### <a name="support-package-alerts"></a>Támogatási csomag riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Nem sikerült létrehozni a támogatási csomagot. |A StorSimple nem sikerült létrehozni a csomagot. |Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. A probléma megoldása után törölje ezt a riasztást a riasztások oldaláról. |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hibák a StorSimple eszköz üzembe helyezési hibák megállapítása és elhárítása](storsimple-8000-troubleshoot-deployment.md).

