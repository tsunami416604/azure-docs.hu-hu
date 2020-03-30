---
title: A StorSimple virtuális tömb riasztásainak megtekintése és kezelése
description: A StorSimple virtuális tömb riasztási feltételeit és súlyosságát ismerteti, valamint azt, hogy miként használhatja a StorSimple Manager szolgáltatást a riasztások kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267429"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>A StorSimple Eszközkezelő használata a StorSimple virtuális tömb riasztásainak kezeléséhez

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás riasztási szolgáltatása lehetővé teszi a StorSimple virtuális tömbökkel kapcsolatos riasztások valós idejű áttekintését és törlését. A **szolgáltatás összesítő** panelen található riasztások segítségével központilag figyelheti a StorSimple virtuális tömbök és a teljes Microsoft Azure StorSimple megoldás állapotproblémáit.

Ez az oktatóanyag ismerteti, hogyan konfigurálhatja a riasztási értesítéseket, a gyakori riasztási feltételeket, a riasztássúlyossági szinteket, és hogyan tekintheti meg és követheti nyomon a riasztásokat. Emellett riasztási gyorsreferencia-táblákat is tartalmaz, amelyek lehetővé teszik egy adott riasztás gyors megkeresését és megfelelő válaszadását.

![Riasztások lap](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretne-e értesítést e-mailben a storsimple virtuális tömbök riasztási feltételeiről. Ezenkívül azonosíthatja a többi riasztási értesítés címzettjét, ha pontosvesszővel elválasztva beírja az e-mail címüket a **További e-mail címzettek** mezőbe.

> [!NOTE]
> Virtuális tömbönként legfeljebb 20 e-mail címet adhat meg.

Miután engedélyezte az e-mail értesítést egy virtuális tömbhöz, az értesítési lista tagjai minden kritikus riasztás esetén e-mail üzenetet kapnak. Az üzeneteket a *rendszer a\@storsimple-alerts-noreply mail.windowsazure.com* küldi el, és leírja a riasztási feltételt. A címzettek a **Leiratkozás** gombra kattintva eltávolíthatják magukat az e-mail értesítési listáról.

#### <a name="to-enable-email-notification-for-alerts"></a>Az e-mailes értesítések engedélyezése a riasztásokhoz

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és a **Kezelés** szakaszban jelölje ki és kattintson az **Eszközök gombra.** A megjelenített eszközök listájában jelölje ki és kattintson az eszközre.
   
    ![riasztási beállítások](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Ezzel megnyitja a **Beállítások** panelt. Az **Eszközbeállítások** csoportban válassza az **Általános**lehetőséget. Ezzel megnyitja az **Általános beállítások** panelt.
   
    ![riasztások értesítésének konfigurációja](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Az **Általános beállítások** panelen nyissa meg a **Riasztási beállítások szakaszt,** és állítsa be a következőket:
   
   1. Az **E-mail értesítés engedélyezése** mezőben válassza az **IGEN**lehetőséget.
   2. Az **E-mail szolgáltatás rendszergazdái** mezőben válassza az **IGEN** lehetőséget, ha azt szeretné, hogy a szolgáltatás rendszergazdája és az összes társ-rendszergazda megkapja a riasztási értesítéseket.
   3. A **További e-mail címzettek** mezőbe írja be az összes többi címzett e-mail címét, akiknek meg kell kapniuk a riasztási értesítéseket. Írja be a neveket abban a *formátumban, amelyet somewhere.com.\@* Pontosvesszővel válassza el az e-mail címeket. Virtuális eszközönként legfeljebb 20 e-mail címet állíthat be.
      
       ![riasztások értesítésének konfigurációja](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Teszte-mail értesítés küldéséhez kattintson a **Teszte-mail küldése**gombra. A StorSimple Eszközkezelő szolgáltatás állapotüzeneteket jelenít meg a tesztértesítés továbbításakor.
      
       ![Riasztások tesztértesítési e-mailben](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Ha a tesztértesítési üzenet nem küldhető el, a StorSimple Eszközkezelő szolgáltatás megjeleníti a megfelelő üzenetet. Kattintson **az OK gombra,** várjon néhány percet, majd próbálja meg újra elküldeni a tesztértesítési üzenetet.
      >
      >
   5. A lap alján kattintson a **Mentés** gombra a konfiguráció mentéséhez. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.
      
      ![Riasztások tesztértesítési e-mailben](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple virtuális tömb riasztásokat hoz létre a különböző feltételekre adott válaszként. A riasztási feltételek leggyakoribb típusai a következők:

* **Kapcsolódási problémák** – Ezek a riasztások akkor fordulnak elő, ha nehézségekbe ütközik az adatátvitel. Kommunikációs problémák fordulhatnak elő az Azure storage-fiókba való adatátvitel során, illetve a virtuális eszközök és a StorSimple Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs kérdések néhány legnehezebb kijavítani, mert olyan sok pont a hiba. Először mindig ellenőrizze, hogy a hálózati kapcsolat és az internet-hozzáférés elérhető-e, mielőtt továbblépne a részletesebb hibaelhárításhoz. A portokkal és a tűzfalbeállításokkal kapcsolatos további tudnivalókért látogasson el a [StorSimple virtual array rendszerkövetelményei](storsimple-ova-system-requirements.md)re. A hibaelhárítással kapcsolatos segítségért keresse fel [a Hibaelhárítás a Test-Connection parancsmaggal kapcsolatban című témakört.](storsimple-troubleshoot-deployment.md)
* **Teljesítményproblémák** – Ezek a riasztások akkor keletkeznek, ha a rendszer nem teljesít optimálisan, például ha nagy terhelés alatt van.

Emellett biztonsági, frissítési vagy feladathibákkal kapcsolatos riasztások is megjelenhetnek.

## <a name="alert-severity-levels"></a>Riasztássúlyossági szintek

Riasztások eltérő súlyossági szint, attól függően, hogy a riasztási helyzet lesz, és a riasztásra adott válasz szükségességét. A súlyossági szintek a következőek:

* **Kritikus –** Ez a riasztás válaszegy olyan állapot, amely befolyásolja a rendszer sikeres teljesítményét. A StorSimple szolgáltatás megszakításának biztosításához műveletre van szükség.
* **Figyelmeztetés** – Ez az állapot kritikussá válhat, ha nem oldódik meg. Vizsgálja meg a helyzetet, és tegyen meg minden szükséges intézkedést a probléma megoldásához.
* **Információ** – Ez a riasztás olyan információkat tartalmaz, amelyek hasznosak lehetnek a rendszer nyomon követésében és kezelésében.

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple Eszközkezelő szolgáltatás összefoglaló panel segítségével gyorsan áttekintheti a virtuális eszközökön lévő riasztások számát, súlyossági szint szerint rendezve.

![Riasztások irányítópultja](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kattintson a súlyossági szintre a **Riasztások** panel megnyitásához. Az eredmények csak az adott súlyossági szintnek megfelelő riasztásokat tartalmazzák.

![A riasztások jelentés hatóköre a riasztás típusához](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kattintson egy riasztásra a listában a riasztás további részleteinek betöltéséhez, beleértve a riasztás legutóbbi jelentésének idejét, a riasztás előfordulásainak számát az eszközön, valamint a riasztás feloldásához javasolt műveletet.

![Riasztások listája és részletei](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

A riasztás részleteit átmásolhatja egy szövegfájlba, ha el kell küldenie az adatokat a Microsoft támogatási szolgálatának. Miután követte az ajánlást, és megoldotta a riasztási feltétel helyszíni, törölje a riasztást a listából. Jelölje ki a riasztást a listából, majd kattintson a **Törlés**gombra. Több riasztás törléséhez jelölje ki az egyes riasztásokat, kattintson bármelyik oszlopra a **Riasztás** oszlop kivételével, majd kattintson a **Törlés** gombra, miután kiválasztotta az összes törlésre kerülő riasztást.

Ha a **Törlés**gombra kattint, lehetősége lesz megjegyzéseket fűzni a riasztáshoz és a probléma megoldásához szükséges lépésekhez.

![figyelmeztető megjegyzések](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

A rendszer bizonyos eseményeket töröl, ha egy másik esemény új információkkal történik.

## <a name="sort-and-review-alerts"></a>Riasztások rendezése és áttekintése

A **Riasztások** panel legfeljebb 250 riasztást jeleníthet meg. Ha túllépte a riasztások számát, nem minden riasztás jelenik meg az alapértelmezett nézetben. A következő mezők kombinálásával testreszabhatja a megjelenített riasztásokat:

* **Állapot** – **Megjelenítheti az aktív** vagy **a törlési** riasztásokat. Az aktív riasztások továbbra is aktiválódnak a rendszeren, míg a törölt riasztásokat a rendszergazda manuálisan törölte, vagy programozott módon törölte, mert a rendszer új információkkal frissítette a riasztási feltételt.
* **Súlyosság –** Megjelenítheti a riasztásokat az összes súlyossági szint (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos súlyossági, például csak a kritikus riasztások.
* **Forrás** – Megjelenítheti az összes forrásból érkező riasztásokat, vagy korlátozhatja a riasztásokat azokra, amelyek a szolgáltatásból vagy egy vagy az összes virtuális eszközből származnak.
* **Időtartomány** – A **Be-** és **Záró** dátumok és időbélyegek megadásával megnézheti az önt érdeklő időszak figyelmeztetéseit.

## <a name="alerts-quick-reference"></a>Riasztások – rövid útmutató

Az alábbi táblázatok néhány StorSimple riasztást sorolnak fel, amelyek előfordulhatnak, valamint további információkat és javaslatokat, ahol elérhető. A StorSimple virtuális tömb riasztásai a következő kategóriák valamelyikébe tartoznak:

* [Felhőkapcsolati riasztások](#cloud-connectivity-alerts)
* [Konfigurációs riasztások](#configuration-alerts)
* [Feladathiba-riasztások](#job-failure-alerts)
* [Teljesítményre vonatkozó riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőkapcsolati riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Az eszköz <*>* nem csatlakozik a felhőhöz. |Az elnevezett eszköz nem tud csatlakozni a felhőhöz. |Nem lehet csatlakozni a felhőhöz. Ezt a következők egyike okozhatja:<ul><li>Lehet, hogy probléma van az eszköz hálózati beállításaival.</li><li>Lehet, hogy probléma van a tárfiók hitelesítő adataival.</li></ul>A kapcsolódási problémák elhárításáról az eszköz [helyi webes felhasználói felületén](storsimple-ova-web-ui-admin.md) talál további információt. |

### <a name="configuration-alerts"></a>Konfigurációs riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| A helyszíni virtuális eszköz konfigurációja nem támogatott. |Lassú teljesítmény. |Az aktuális konfiguráció teljesítménycsökkenést eredményezhet. Győződjön meg arról, hogy a kiszolgáló megfelel a minimális konfigurációs követelményeknek. További információ: [StorSimple virtual array requirements](storsimple-ova-system-requirements.md). |
| Fogytán van a kiépített lemezterület <*eszköz nevén.*\> |Lemezterület figyelmeztetés. |Kevés a kiépített lemezterület. Ha helyet szeretne felszabadítani, fontolja meg a számítási feladatok áthelyezését egy másik kötetre, vagy adatok megosztását vagy törlését. |

### <a name="job-failure-alerts"></a>Feladathiba-riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Nem sikerült biztonsági másolatot készíteni <*eszköznevéről.* \> |Biztonsági mentési feladat hibája. |Nem lehetett biztonsági másolatot készíteni. Vegye figyelembe az alábbiak egyikét:<ul><li>A kapcsolódási problémák megakadályozhatják a biztonsági mentési művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek csatlakozási problémák. A kapcsolódási problémák elhárításáról a virtuális eszköz [helyi webes felhasználói felületén](storsimple-ova-web-ui-admin.md) talál további információt.</li><li>Elérte a rendelkezésre álló tárhelykorlátot. A hely felszabadításához fontolja meg a már nem szükséges biztonsági mentések törlését.</li></ul> Oldja meg a problémákat, törölje a riasztást, és próbálkozzon újra a művelettel. |
| Nem sikerült <*eszköznevének* \> klónozása. |Klónfeladat sikertelen. |Nem lehet klónt létrehozni. Vegye figyelembe az alábbiak egyikét:<ul><li>Lehet, hogy a biztonsági másolat listája érvénytelen. Frissítse a listát, és ellenőrizze, hogy még mindig érvényes-e.</li><li>A kapcsolódási problémák megakadályozhatják a klónozási művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek csatlakozási problémák.</li><li>Elérte a rendelkezésre álló tárhelykorlátot. A hely felszabadításához fontolja meg a már nem szükséges biztonsági mentések törlését.</li></ul>Oldja meg a problémákat, törölje a riasztást, és próbálkozzon újra a művelettel. |

### <a name="networking-alerts"></a>Hálózati riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Nem sikerült csatlakozni a hitelesítési szolgáltatáshoz. |Datapath hiba |A hitelesítéshez használt URL-cím nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazzák a StorSimple eszközhöz megadott URL-mintákat. Az Azure Portal URL-mintáiról a [StorSimple virtual array hálózati követelményei](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)című részben talál további információt.|

### <a name="performance-alerts"></a>Teljesítményre vonatkozó riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Váratlan késedelmet tapasztal az adatátvitelben. |Lassú adatátvitel. |Szabályozáshibák akkor fordulnak elő, ha túllépi a tárolási szolgáltatás méretezhetőségi céljait. A tárolási szolgáltatás ezt annak biztosítása érdekében, hogy egyetlen ügyfél vagy bérlő nem használhatja a szolgáltatást mások rovására. Az Azure-tárfiók hibaelhárításával kapcsolatos további tudnivalókért látogasson el a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása című témakörre.](../storage/common/storage-monitoring-diagnosing-troubleshooting.md) |
| Kevés a helyfoglalási lemezterület <*eszköz nevén.*\> |Lassú válaszidő. |<*eszköznév* \> teljes kiosztott méretének 10%-a a helyi eszközön van fenntartva, és most kevés a fenntartott terület. A számítási feladatok <*eszköz neve* \> generál nagyobb lemorzsolódás, vagy előfordulhat, hogy a közelmúltban nagy mennyiségű adatot telepített át. Ez a teljesítmény csökkenését eredményezheti. A probléma megoldásához vegye figyelembe az alábbi műveletek egyikét:<ul><li>Növelje az eszköz felhőbeli sávszélességét.</li><li>Csökkentheti vagy áthelyezi a munkaterheléseket egy másik kötetre vagy megosztásra.</li></ul> |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információk / ajánlott intézkedések |
|:--- |:--- |:--- |
| Az *<eszköznevének* \> jelszava <*napokban* \> lejár. |Jelszó figyelmeztetés. |A jelszó <*napon* \> belül lejár. Fontolja meg a jelszó módosítását. További információ: [A StorSimple Virtual Array eszköz rendszergazdai jelszavának módosítása.](storsimple-virtual-array-change-device-admin-password.md) |

## <a name="next-steps"></a>További lépések

* [További információ a StorSimple virtuális tömbről.](storsimple-ova-overview.md)
