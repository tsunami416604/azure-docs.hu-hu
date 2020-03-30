---
title: A StorSimple 8000 sorozatú eszközök riasztásainak megtekintése és kezelése
description: A StorSimple riasztási feltételeit és súlyosságát, a riasztási értesítések konfigurálásának módját, valamint a StorSimple Eszközkezelő szolgáltatás használatával a riasztások kezeléséhez használható.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267819"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple-riasztások megtekintéséhez és kezeléséhez

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás **Riasztások** panelje lehetővé teszi a StorSimple-eszközökkel kapcsolatos riasztások valós idejű áttekintését és törlését. Ebből a panelből központilag figyelheti a StorSimple-eszközök és a teljes Microsoft Azure StorSimple megoldás állapotproblémáit.

Ez az oktatóanyag ismerteti a gyakori riasztási feltételeket, a riasztás súlyossági szintjét és a riasztási értesítések konfigurálását. Emellett riasztási gyorsreferencia-táblákat is tartalmaz, amelyek lehetővé teszik egy adott riasztás gyors megkeresését és megfelelő válaszadását.

![Riasztások lap](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple-eszköz riasztásokat hoz létre a különböző körülményekre adott válaszként. A riasztási feltételek leggyakoribb típusai a következők:

* **Hardverproblémák** – Ezek a riasztások a hardver állapotáról szólnak. Tudatják Önnel, hogy szükség van-e belső vezérlőprogram-frissítésre, ha a hálózati adapterproblémákkal rendelkezik, vagy ha probléma van az egyik adatmeghajtóval.
* **Kapcsolódási problémák** – Ezek a riasztások akkor fordulnak elő, ha nehézségekbe ütközik az adatátvitel. Kommunikációs problémák fordulhatnak elő az Azure storage-fiókba való adatátvitel során, illetve az eszközök és a StorSimple Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs kérdések néhány legnehezebb kijavítani, mert olyan sok pont a hiba. Először mindig ellenőrizze, hogy a hálózati kapcsolat és az internet-hozzáférés elérhető-e, mielőtt továbblépne a részletesebb hibaelhárításhoz. A hibaelhárítással kapcsolatos segítségért keresse fel [a Hibaelhárítás a Test-Connection parancsmaggal kapcsolatban című témakört.](storsimple-8000-troubleshoot-deployment.md)
* **Teljesítményproblémák** – Ezek a riasztások akkor keletkeznek, ha a rendszer nem teljesít optimálisan, például ha nagy terhelés alatt van.

Emellett biztonsági, frissítési vagy feladathibákkal kapcsolatos riasztások is megjelenhetnek.

## <a name="alert-severity-levels"></a>Riasztássúlyossági szintek

Riasztások eltérő súlyossági szint, attól függően, hogy a riasztási helyzet lesz, és a riasztásra adott válasz szükségességét. A súlyossági szintek a következőek:

* **Kritikus –** Ez a riasztás válaszegy olyan állapot, amely befolyásolja a rendszer sikeres teljesítményét. A StorSimple szolgáltatás megszakításának biztosításához műveletre van szükség.
* **Figyelmeztetés** – Ez az állapot kritikussá válhat, ha nem oldódik meg. Vizsgálja meg a helyzetet, és tegyen meg minden szükséges intézkedést a probléma megoldásához.
* **Információ** – Ez a riasztás olyan információkat tartalmaz, amelyek hasznosak lehetnek a rendszer nyomon követésében és kezelésében.

## <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretne-e értesítést e-mailben a storSimple-eszközök riasztási feltételeiről. Ezenkívül azonosíthatja a többi értesítési címzettet, ha pontosvesszővel elválasztva beírja az e-mail címüket az **Egyéb e-mail címzettek** mezőbe.

> [!NOTE]
> Eszközönként legfeljebb 20 e-mail címet adhat meg.

Miután engedélyezte az e-mail es értesítést egy eszközhöz, az értesítési lista tagjai minden kritikus riasztás esetén e-mail üzenetet kapnak. Az üzeneteket a *rendszer a\@storsimple-alerts-noreply mail.windowsazure.com* küldi el, és leírja a riasztási feltételt. A címzettek a **Leiratkozás** gombra kattintva eltávolíthatják magukat az e-mail értesítési listáról.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Az eszköz értesítésének e-mailes értesítésének engedélyezése
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájában jelölje ki és kattintson a konfigurálni kívánt eszközre.
2. Nyissa meg az eszköz**Általános** **beállításai** > lapot.

   ![Riasztások panel](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Az **Általános beállítások** panelen nyissa meg a **Riasztás beállításait,** és állítsa be a következőket:
   
   1. Az **E-mail értesítés küldése** mezőben válassza az **IGEN**lehetőséget.
   2. Az **E-mail szolgáltatás rendszergazdái** mezőben válassza az **IGEN** lehetőséget, ha azt szeretné, hogy a szolgáltatás rendszergazdája és az összes társ-rendszergazda megkapja a riasztási értesítéseket.
   3. Az **Egyéb e-mail címzettek** mezőbe írja be az összes többi címzett e-mail címét, akiknek meg kell kapniuk a riasztási értesítéseket. Írja be a neveket abban a *formátumban, amelyet somewhere.com.\@* Pontosvesszővel válassza el az e-mail címeket. Eszközönként legfeljebb 20 e-mail címet állíthat be. 
      
3. Teszte-mail értesítés küldéséhez kattintson a **Teszte-mail küldése**gombra. A StorSimple Eszközkezelő szolgáltatás állapotüzeneteket jelenít meg a tesztértesítés továbbításakor.

    ![Riasztási beállítások](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. A teszte-mail elküldésekor értesítést kap. 
   
    ![Riasztások tesztértesítési e-mailben](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Ha a tesztértesítési üzenet nem küldhető el, a StorSimple Eszközkezelő szolgáltatás megjeleníti a megfelelő hibaüzenetet. Várjon néhány percet, majd próbálja meg újra elküldeni a tesztértesítési üzenetet. 

5. Miután befejezte a konfigurációt, kattintson a **Mentés gombra.** Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Riasztások tesztértesítési e-mailben](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple Eszközkezelő szolgáltatás összefoglaló panel segítségével gyorsan áttekintheti az eszközökön található riasztások számát, súlyossági szint szerint rendezve.

![Riasztások irányítópultja](./media/storsimple-8000-manage-alerts/device-summary4.png)

A súlyossági szintre kattintva megnyílik a **Riasztások** panel. Az eredmények csak az adott súlyossági szintnek megfelelő riasztásokat tartalmazzák.

A listában egy riasztásra kattintva további részleteket talál a riasztásról, beleértve a riasztás legutóbbi jelentésének idejét, az eszközön a riasztás előfordulásainak számát és a riasztás feloldásához javasolt műveletet. Ha hardverriasztásról van szó, akkor a hardverösszetevőt is azonosítja.

![Példa hardverriasztásra](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

A riasztás részleteit átmásolhatja egy szövegfájlba, ha el kell küldenie az adatokat a Microsoft támogatási szolgálatának. Miután követte az ajánlást, és megoldotta a riasztási feltételt a helyszínen, törölje a riasztást az eszközről, ha kiválasztja a **riasztást** a Riasztások panelen, és kattintson a **Törlés**gombra. Több riasztás törléséhez jelölje ki az egyes riasztásokat, kattintson bármelyik oszlopra a **Riasztás** oszlop kivételével, majd kattintson a **Törlés** gombra, miután kiválasztotta az összes törlésre kerülő riasztást. Vegye figyelembe, hogy egyes riasztások automatikusan törlődnek, ha a probléma megoldódott, vagy amikor a rendszer új információkkal frissíti a riasztást.

Ha a **Törlés**gombra kattint, lehetősége lesz megjegyzéseket fűzni a riasztáshoz és a probléma megoldásához szükséges lépésekhez. A rendszer bizonyos eseményeket töröl, ha egy másik esemény új információkkal történik. Ebben az esetben a következő üzenet jelenik meg.

![Figyelmeztető üzenet törlése](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Riasztások rendezése és áttekintése

Hatékonyabbnak találhatja a riasztásokról szóló jelentések futtatását, hogy csoportokban ellenőrizhesse és törölhesse őket. Emellett a **Riasztások** panel legfeljebb 250 riasztást jeleníthet meg. Ha túllépte a riasztások számát, nem minden riasztás jelenik meg az alapértelmezett nézetben. A következő mezők kombinálásával testreszabhatja a megjelenített riasztásokat:

* **Állapot** – **Megjelenítheti az aktív** vagy **a törlési** riasztásokat. Az aktív riasztások továbbra is aktiválódnak a rendszeren, míg a törölt riasztásokat a rendszergazda manuálisan törölte, vagy programozott módon törölte, mert a rendszer új információkkal frissítette a riasztási feltételt.
* **Súlyosság –** Megjelenítheti a riasztásokat az összes súlyossági szint (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos súlyossági, például csak a kritikus riasztások.
* **Forrás** – Megjelenítheti az összes forrásból érkező riasztásokat, vagy korlátozhatja a riasztásokat azokra, amelyek a szolgáltatásból vagy az egyik vagy az összes eszközből származnak.
* **Időtartomány** – A **Be-** és **Záró** dátumok és időbélyegek megadásával megnézheti az önt érdeklő időszak figyelmeztetéseit.

![Riasztások listája](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Riasztások – rövid útmutató

Az alábbi táblázatok a Microsoft Azure StorSimple által esetleg előforduló riasztásokat, valamint további információkat és javaslatokat sorolnak fel, ahol elérhetők. A StorSimple eszközriasztások a következő kategóriák valamelyikébe tartoznak:

* [Felhőkapcsolati riasztások](#cloud-connectivity-alerts)
* [Fürtriasztások](#cluster-alerts)
* [Vész-helyreállítási riasztások](#disaster-recovery-alerts)
* [Hardverriasztások](#hardware-alerts)
* [Feladathiba-riasztások](#job-failure-alerts)
* [Helyileg rögzített kötetriasztások](#locally-pinned-volume-alerts)
* [Hálózati riasztások](#networking-alerts)
* [Teljesítményre vonatkozó riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)
* [Támogatási csomagriasztás](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőkapcsolati riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Nem hozható létre <*felhőhitelesítő adatok nevéhez*> kapcsolat. |Nem lehet csatlakozni a tárfiókhoz. |Úgy tűnik, hogy kapcsolódási probléma van az eszközzel. Futtassa `Test-HcsmConnection` a parancsmast a Windows PowerShell Interface for StorSimple az eszközön a probléma azonosításához és megoldásához. Ha a beállítások helyesek, a probléma lehet a hitelesítő adatait a tárfiók, amelyre a riasztás t. Ebben az esetben `Test-HcsStorageAccountCredential` a parancsmag segítségével határozza meg, hogy vannak-e megoldható problémák.<ul><li>Ellenőrizze a hálózati beállításokat.</li><li>Ellenőrizze a tárfiók hitelesítő adatait.</li></ul> |
| Az elmúlt <*szám*> percben nem kaptunk szívverést az Ön készülékétől. |Nem lehet csatlakozni az eszközhöz. |Úgy tűnik, csatlakozási probléma van az eszközzel. Kérjük, `Test-HcsmConnection` használja a parancsmag a Windows PowerShell interface for StorSimple az eszközön, hogy azonosítsa és javítsa ki a problémát, vagy forduljon a hálózati rendszergazdához. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple viselkedés, ha a felhőkapcsolat meghibásodik

Mi történik, ha a felhőbeli kapcsolat meghibásodik az éles környezetben futó StorSimple-eszközhöz?

Ha a felhőbeli kapcsolat nem működik a StorSimple éles eszközön, akkor az eszköz állapotától függően a következők fordulhatnak elő:

* **Az eszközön lévő helyi adatok**hoz: Egy ideig nem lesz fennakadás, és az olvasások továbbra is kiszolgálásra kerülnek. Azonban a függőben lévő IOs-ek száma növekszik, és meghaladja a korlátot, az olvasások elkezdődhetnek sikertelenül.

    Az eszközön lévő adatok mennyiségétől függően az írási műveletek a felhőbeli kapcsolat megszakadását követő első néhány órában is folytatódni fognak. Az írási műveletek majd lelassul, és végül elindul, ha a felhőkapcsolat megszakad több órán keresztül. (Az eszközön ideiglenes tároló található a felhőbe lelökendő adatok számára. Ez a terület az adatok elküldésekor kiürítésre kerül. Ha a kapcsolat meghibásodik, a tárolóterület adatai nem lesznek leküldéses a felhőbe, és az IO sikertelen lesz.)
* **A felhőben lévő adatok esetében:** A legtöbb felhőbeli kapcsolódási hiba esetén a rendszer hibát ad vissza. A kapcsolat helyreállítása után az IOs anélkül folytatódik, hogy a felhasználónak online állapotba kellene hoznia a kötetet. Ritka esetekben felhasználói beavatkozásra lehet szükség, hogy a kötet online az Azure Portalon.
* **A folyamatban lévő felhőbeli pillanatképek**: A művelet et 4-5 órán belül néhányszor újra próbálkozik, és ha a kapcsolat nem áll helyre, a felhőbeli pillanatképek sikertelenek lesznek.

### <a name="cluster-alerts"></a>Fürtriasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Az eszköz> *<.* |Az eszköz karbantartási üzemmódban van. |Az eszköz a karbantartási módba való belépés vagy kilépés miatt nem felelt meg. Ez normális, és nincs szükség beavatkozásra. Miután elismerte ezt a riasztást, törölje azt a riasztások oldalról. |
| Az eszköz> *<.* |Az eszköz belső vezérlőprogramja vagy szoftvere csak frissítve lett. |Fürt feladatátvételt kapott egy frissítés miatt. Ez normális, és nincs szükség beavatkozásra. Miután elismerte ezt a riasztást, törölje azt a riasztások oldalról. |
| Az eszköz> *<.* |A vezérlő leállt vagy újraindult. |Az eszköz feladatátvételt kapott, mert a rendszergazda leállította vagy újraindította az aktív vezérlőt. Nincs szükség beavatkozásra. Miután elismerte ezt a riasztást, törölje azt a riasztások oldalról. |
| Az eszköz> *<.* |Tervezett feladatátvétel. |Ellenőrizze, hogy ez egy tervezett feladatátvétel volt-e. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról. |
| Az eszköz> *<.* |Nem tervezett feladatátvétel. |A StorSimple úgy van kialakítva, hogy automatikusan helyreálljon a nem tervezett feladatátvételek miatt. Ha sok ilyen riasztást lát, forduljon a Microsoft támogatási szolgálatához. |
| Az eszköz> *<.* |Egyéb/ismeretlen ok. |Ha sok ilyen riasztást lát, forduljon a Microsoft támogatási szolgálatához. A probléma megoldása után törölje a riasztást a riasztások lapjáról. |
| A kritikus eszközszolgáltatás hibásként jelenti az állapotot. |Datapath szolgáltatás hibája. |Segítségért forduljon a Microsoft támogatási szolgálatához. |
| A *DATA #* <hálózati adapter virtuális IP-címe,> hibásként jelenti állapotát. |Egyéb/ismeretlen ok. |Néha ideiglenes körülmények okozhatnak ilyen riasztásokat. Ebben az esetben ez a riasztás egy idő után automatikusan törlődik. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| A *DATA #* <hálózati adapter virtuális IP-címe,> hibásként jelenti állapotát. |A kapcsolat neve: <`<IP address>` DATA *#*> IP-cím nem hozható online állapotba, mert a hálózat duplikált IP-címet észlelt. |Győződjön meg arról, hogy az ismétlődő IP-cím eltávolításra kerül a hálózatról, vagy konfigurálja újra az összeköttetést egy másik IP-címmel. |

### <a name="disaster-recovery-alerts"></a>Vész-helyreállítási riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A helyreállítási műveletek nem tudták visszaállítani a szolgáltatás összes beállítását. Az eszközkonfigurációs adatok egyes eszközök esetében inkonzisztens állapotban vannak. |A vészhelyreállítás után észlelt adatinkonzisztencia. |A szolgáltatás titkosított adatai nincsenek szinkronizálva az eszközön lévő adatokkal. Engedélyezze az eszköz <a StorSimple Eszközkezelőtől származó *> eszköznevét* a szinkronizálási folyamat elindításához. A Windows PowerShell-felület storSimple `Restore-HcsmEncryptedServiceData` futtatni az eszközön <*eszköz nevét*> parancsmag, amely a régi jelszót, mint egy bemeneti parancsmag a biztonsági profil visszaállításához. Ezután `Invoke-HcsmServiceDataEncryptionKeyChange` futtassa a parancsmaparancsot a szolgáltatás adattitkosítási kulcsának frissítéséhez. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról. |

### <a name="hardware-alerts"></a>Hardverriasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A hardverösszetevő <*összetevő-azonosítója*> <*állapot>.* | |Néha ideiglenes körülmények okozhatnak ilyen riasztásokat. Ha igen, a riasztás egy idő után automatikusan törlődik. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| A passzív vezérlő meghibásodott. |A passzív (másodlagos) vezérlő nem működik. |A készülék működik, de az egyik vezérlő hibásan működik. Próbálja meg újraindítani a vezérlőt. Ha a probléma nem oldódott meg, forduljon a Microsoft támogatási szolgálatához. |

### <a name="job-failure-alerts"></a>Feladathiba-riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Nem sikerült biztonsági másolatot készíteni <*forráskötetcsoport azonosító>járól.* |A biztonsági mentési feladat nem sikerült. |A kapcsolódási problémák megakadályozhatják a biztonsági mentési művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy elérte a biztonsági mentések maximális számát. Törölje a már nem szükséges biztonsági másolatokat, és próbálkozzon újra a művelettel. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról. |
| Nem sikerült <*forrásbiztonsági másolat azonosítóinak klónozása*> <*célkötet sorozat>számaihoz.* |A klónozási feladat nem sikerült. |Frissítse a biztonsági másolat listát, és ellenőrizze, hogy a biztonsági másolat továbbra is érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőbeli kapcsolódási problémák megakadályozzák a klónozási művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy elérte a tárolási korlátot. Törölje a már nem szükséges biztonsági másolatokat, és próbálkozzon újra a művelettel. Miután megtette a megfelelő lépéseket a probléma megoldásához, törölje a riasztást a riasztások oldalról. |
| A *<forrásbiztonsági másolat elemazonosítóinak visszaállítása*> sikertelen. |A visszaállítási feladat nem sikerült. |Frissítse a biztonsági másolat listát, és ellenőrizze, hogy a biztonsági másolat továbbra is érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőbeli kapcsolódási problémák megakadályozzák a visszaállítási művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy elérte a tárolási korlátot. Törölje a már nem szükséges biztonsági másolatokat, és próbálkozzon újra a művelettel. Miután megtette a megfelelő lépéseket a probléma megoldásához, törölje a riasztást a riasztások oldalról. |

### <a name="locally-pinned-volume-alerts"></a>Helyileg rögzített kötetriasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A helyi kötet <*kötetneve* nem sikerült>. |A kötet létrehozási feladata nem sikerült. <*A sikertelen hibakódnak megfelelő* hibaüzenet>. |A kapcsolódási problémák megakadályozhatják a helyteremtési művelet sikeres befejezését. A helyileg rögzített kötetek vastagon vannak kiépítve, és a terület létrehozásának folyamata magában foglalja a rétegzett kötetek kiömlését a felhőbe. Ha nincsenek csatlakozási problémák, előfordulhat, hogy kimerítette az eszközön lévő helyi területet. A művelet újbóli megkísérlése előtt állapítsa meg, hogy van-e hely az eszközön. |
| A helyi kötet <*kötet neve*> sikertelen. |A kötetmódosítási feladat <*hibakódnak megfelelő hibaüzenet*> miatt sikertelen. |A kapcsolódási problémák megakadályozhatják a kötetbővítési művelet sikeres befejezését. A helyileg rögzített kötetek vastagon vannak kiépítve, és a meglévő terület kiterjesztésének folyamata magában foglalja a rétegzett kötetek kiömlését a felhőbe. Ha nincsenek csatlakozási problémák, előfordulhat, hogy kimerítette az eszközön lévő helyi területet. A művelet újbóli megkísérlése előtt állapítsa meg, hogy van-e hely az eszközön. |
| A kötet <*kötetnevének* konvertálása sikertelen>. |A kötettípus helyileg rögzítettről rétegzettre történő konvertálására a kötetkonverziós feladat nem sikerült. |A kötet átalakítása a helyileg rögzített típusból rétegzettre nem hajtható végre. Győződjön meg arról, hogy nincsenek olyan csatlakozási problémák, amelyek megakadályozzák a művelet sikeres befejezését. A kapcsolódási problémák elhárításához látogasson el [a Hibaelhárítás a Test-HcsmConnection parancsmaggal című témakörben.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)<br>Az eredeti helyileg rögzített kötet most már rétegzett kötetként van megjelölve, mivel a helyileg rögzített kötet egyes adatai kiömltek a felhőbe az átalakítás során. Az eredő rétegzett kötet továbbra is helyi helyet foglal el az eszközön, amely nem nyerhető vissza a jövőbeli helyi köteteknél.<br>Hárítsa el a kapcsolódási problémákat, törölje a riasztást, és alakítsa vissza a kötetet az eredeti, helyileg rögzített kötettípusra, hogy az összes adat ismét helyileg elérhetőlegyen. |
| A kötet <*kötetnevének* konvertálása sikertelen>. |A kötettípus rétegzettről helyileg rögzítettre történő konvertálására irányuló kötetátalakítási feladat nem sikerült. |A kötet rétegzett típusból helyileg rögzítettre történő átalakítása nem hajtható végre. Győződjön meg arról, hogy nincsenek olyan csatlakozási problémák, amelyek megakadályozzák a művelet sikeres befejezését. A kapcsolódási problémák elhárításához látogasson el [a Hibaelhárítás a Test-HcsmConnection parancsmaggal című témakörben.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)<br>Az eredeti rétegzett kötet most helyileg rögzített kötetként van megjelölve az átalakítási folyamat részeként, amelytovábbra is rendelkezik a felhőben tárolt adatokkal, míg az eszköz vastagon kiépített területe a kötethez a jövőbeli helyi kötetek esetében már nem nyerhető vissza.<br>Hárítsa el a kapcsolódási problémákat, törölje a riasztást, és alakítsa vissza ezt a kötetet az eredeti rétegzett kötettípusra, hogy biztosítsa az eszközön vastagon kiépített helyi területet. |
| Közeledik a helyi területfelhasználáshoz <*kötetcsoport nevének* helyi pillanatképeihez> |A biztonsági mentési házirend helyi pillanatképei hamarosan elfogyhatnak a helyből, és érvényteleníthetők az állomás írási hibáinak elkerülése érdekében. |A biztonsági mentési házirend-csoporthoz társított kötetek gyakori helyi pillanatképei az eszköz helyi tárhelyének gyors felhasználását eredményezik. Törölje a helyi pillanatképeket, amelyek már nincs szükség. Emellett frissítse a biztonsági mentési szabályzat helyi pillanatkép-ütemezéseit, hogy kevésbé gyakori helyi pillanatképeket készítsen, és győződjön meg arról, hogy a felhőbeli pillanatképek rendszeresen készülnek. Ha ezek a műveletek nem történnek meg, a pillanatképek helyi területe hamarosan kimerülhet, és a rendszer automatikusan törli őket annak érdekében, hogy az állomásírások feldolgozása továbbra is sikeres legyen. |
| A *kötetcsoport nevének* <helyi pillanatképei> érvénytelenítve. |A *kötetcsoport nevének*> <helyi pillanatképei érvénytelenné váltak, majd törlődtek, mert túllépték az eszközön lévő helyi területet. |Annak érdekében, hogy ez a jövőben ne ismétlődjön meg, tekintse át a biztonsági mentési szabályzat helyi pillanatkép-ütemezéseit, és törölje a már nem szükséges helyi pillanatképeket. A biztonsági mentési házirend-csoporthoz társított kötetek gyakori helyi pillanatképei az eszköz helyi tárhelyének gyors felhasználását eredményezhetik. |
| A *<forrásbiztonsági másolat elemazonosítóinak visszaállítása*> sikertelen. |A visszaállítási feladat nem sikerült. |Ha helyileg rögzített, vagy a helyileg rögzített és rétegzett kötetek vegyesen ebben a biztonsági mentési házirendben, frissítse a biztonsági mentési listát, és ellenőrizze, hogy a biztonsági mentés továbbra is érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőbeli kapcsolódási problémák megakadályozzák a visszaállítási művelet sikeres befejezését. A pillanatkép-csoport részeként visszaállított helyileg rögzített kötetek nem rendelkeznek az összes adat letöltésével az eszközre, és ha rétegzett és helyileg rögzített kötetek vegyesen vannak ebben a pillanatkép-csoportban, azok nem lesznek szinkronban egymással. A visszaállítási művelet sikeres befejezéséhez tegye offline állapotba a csoport köteteit az állomáson, majd próbálkozzon újra a visszaállítási művelettel. Vegye figyelembe, hogy a visszaállítási folyamat során végrehajtott kötetadatok módosításai elvesznek. |

### <a name="networking-alerts"></a>Hálózati riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Nem indítható a StorSimple szolgáltatás(oka). |Datapath hiba |Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| A "Data0" azonosító IP-címe. | |A rendszer ütközést észlelt a "10.0.0.1" IP-címnél. Az eszköz * \<eszközén* "Data0" hálózati erőforrás1>offline állapotban van. Győződjön meg arról, hogy ezt az IP-címet a hálózat más entitása nem használja. A hálózati problémák elhárításához nyissa meg [a Hibaelhárítás a Get-NetAdapter parancsmaggal című témakört.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet) A probléma megoldásához forduljon a hálózati rendszergazdához. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| A "Data0" IPv4(vagy IPv6) címe offline állapotban van. | |A "Data0" hálózati erőforrás "10.0.0.1" IP-címmel. és a "22" előtag hossza az * \<eszközön1>* offline állapotban van. Győződjön meg arról, hogy azok a kapcsolóportok, amelyekhez ez az összeköttetés csatlakozik, működőképesek. A hálózati problémák elhárításához nyissa meg [a Hibaelhárítás a Get-NetAdapter parancsmaggal című témakört.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet) |
| Nem sikerült csatlakozni a hitelesítési szolgáltatáshoz. |Datapath hiba |A hitelesítéshez használt URL-cím nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazzák a StorSimple eszközhöz megadott URL-mintákat. Az Azure Portal URL-mintáiról a\/https: /aka.ms/ss-8000-network-reqs webhelyen talál további információt. Ha az Azure Government Cloud, nyissa meg\/az URL-minták https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Teljesítményre vonatkozó riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések | |
|:--- |:--- |:--- | --- |
| Az eszköz terhelése túllépte <*küszöbértéket*>. |A vártnál lassabb válaszidők. |A készülék nagy bemeneti/kimeneti terhelés mellett jelenti a kihasználtságot. Ez azt eredményezheti, hogy a készülék nem működik olyan jól, ahogy kellene. Tekintse át az eszközhöz csatlakoztatott munkaterheléseket, és állapítsa meg, hogy vannak-e olyan, amely áthelyezhető egy másik eszközre, vagy amelyek már nem szükségesek.|
| Nem indítható a StorSimple szolgáltatás(oka). |Datapath hiba |Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A Microsoft támogatási munkamenete megkezdődött. |Harmadik fél által elért támogatási munkamenet. |Ellenőrizze, hogy ez a hozzáférés engedélyezett-e. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról. |
| A> *elem* <jelszava <*>.* |A jelszó lejárata közeledik. |Változtassa meg a jelszavát, mielőtt lejár. |
| Hiányoznak <*elemazonosító*> biztonsági konfigurációs adatai. | |A kötettárolóhoz társított kötetek nem használhatók a StorSimple-konfiguráció replikálására. Annak érdekében, hogy az adatok biztonságosan tárolják, azt javasoljuk, hogy törölje a kötettárolóés a kötettárolóhoz társított köteteket. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról. |
| <*a* <*elemazonosító*>> bejelentkezési kísérlet. |Több sikertelen bejelentkezési kísérlet. |Lehet, hogy az eszköz támadás alatt áll, vagy egy jogosult felhasználó helytelen jelszóval próbál csatlakozni.<ul><li>Lépjen kapcsolatba a jogosult felhasználókkal, és ellenőrizze, hogy ezek a kísérletek jogszerű forrásból származnak-e. Ha továbbra is nagy számú sikertelen bejelentkezési kísérletet lát, fontolja meg a távkezelés letiltását és a hálózati rendszergazdához való kapcsolatfelvétel megbúvását. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról.</li><li>Ellenőrizze, hogy a Snapshot Manager-példányok a megfelelő jelszóval vannak-e konfigurálva. Miután megtette a megfelelő lépéseket, törölje ezt a riasztást a riasztások oldalról.</li></ul>További információ: [Lejárt eszközjelszó módosítása.](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password) |
| Egy vagy több hiba történt a szolgáltatás adattitkosítási kulcsának módosítása közben. | |Hibák merültek fel a szolgáltatás adattitkosítási kulcsának módosítása kor. Miután kioldotta a hibafeltételeket, futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmaletet a Windows PowerShell Interface for StorSimple az eszközön a szolgáltatás frissítéséhez. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. A probléma megoldása után törölje a riasztást a riasztások lapjáról. |

### <a name="support-package-alerts"></a>Támogatási csomagriasztás

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A támogatási csomag létrehozása nem sikerült. |A StorSimple nem tudta létrehozni a csomagot. |Próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. Miután megoldotta a problémát, törölje a riasztást a riasztások lapról. |

## <a name="next-steps"></a>További lépések

További információ a [StorSimple hibáiról és az eszközök telepítésével kapcsolatos problémák elhárításáról.](storsimple-8000-troubleshoot-deployment.md)

