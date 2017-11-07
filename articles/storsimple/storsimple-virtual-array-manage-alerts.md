---
title: "A Microsoft Azure StorSimple virtuális tömb riasztások megtekintése és kezelése |} Microsoft Docs"
description: "Ismerteti a StorSimple virtuális tömb riasztási feltételek és a súlyosság, és a StorSimple Manager szolgáltatás segítségével kezelheti a riasztásokat."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a96e52bad725acb70a429c8eaafd6942b13d9b8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Riasztások kezelése a StorSimple virtuális tömb StorSimple Eszközkezelő

## <a name="overview"></a>Áttekintés

A riasztások funkciók a StorSimple Device Manager-módot biztosít a áttekintheti, és törölje a StorSimple virtuális tömbök valós idejű alapon kapcsolatos riasztásokat. A riasztások használhatja a **szolgáltatási összegző** panelt, és központilag figyelheti a ügynökállapottal kapcsolatos hibákkal a StorSimple virtuális tömbök és a teljes Microsoft Azure StorSimple megoldáshoz.

Ez az oktatóanyag leírja a riasztási értesítéseket, a közös riasztási feltételek, a riasztások súlyossági szintjei konfigurálása és megtekintése, és nyomon követéséhez értesítések. Ezenkívül azt is riasztás rövid összefoglaló táblák, amelyekkel gyorsan keresse meg az adott riasztásra, és megfelelő választ.

![Riasztások lap](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>A riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretné-e az egyes a StorSimple virtuális tömbök riasztási feltétel e-mailben értesítést kapni. Ezenkívül azonosítsa a más riasztási értesítés címzettjeinek írja be az e-mail címüket a **további e-mailek címzettjeinek** mezőbe pontosvesszővel elválasztva.

> [!NOTE]
> Legfeljebb 20 e-mail cím adható virtuális tömb adhat meg.


Egy virtuális tömb kapcsolódó e-mail értesítések engedélyezése után az értesítési listán tagjai minden alkalommal, amikor egy kritikus riasztás esetén e-mailt fog kapni. Az üzenetek küldését  *storsimple-alerts-noreply@mail.windowsazure.com*  és leírja a riasztást kiváltó körülmény. Címzettek kattintva **Unsubscribe** maguk eltávolítása az e-mail értesítési listán.

#### <a name="to-enable-email-notification-for-alerts"></a>Riasztásokhoz kapcsolódó e-mail értesítések engedélyezése

1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és a a **felügyeleti** szakaszt, válassza ki, és kattintson **eszközök**. Az eszközök jelenik meg a listában jelölje ki, és kattintson az eszköz.
   
    ![a riasztási beállítások](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Ezzel megnyílik a **beállítások** panelen. Az a **eszközbeállítások** szakaszban jelölje be **általános**. Ezzel megnyílik a **általános beállítások** panelen.
   
    ![riasztások értesítési konfiguráció](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Az a **általános beállítások** paneljén lépjen **riasztási beállítások** szakaszt, és állítsa be a következőket:
   
   1. Az a **e-mail értesítő engedélyezése** mezőben válassza **Igen**.
   2. Az a **szolgáltatás-rendszergazdák E-mail** mezőben válassza **Igen** Ha a szolgáltatás-rendszergazda kíván, és minden társrendszergazdák a riasztási értesítéseket.
   3. Az a **további e-mailek címzettjeinek** mezőbe írja be a többi a riasztási értesítéseket kapjanak címzett e-mail címét. Írja be nevét a formátumban  *someone@somewhere.com* . Az e-mail címek elválasztásához használjon pontosvesszőt. Legfeljebb 20 e-mail címeket a virtuális eszköz konfigurálása
      
       ![riasztások értesítési konfiguráció](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Egy teszt e-mail értesítést küldeni, kattintson a **teszt e-mail küldése**. A tesztértesítés továbbítja a StorSimple Device Manager szolgáltatás jelennek-állapotüzenetek.
      
       ![Riasztások által küldött értesítő e-mail tesztelése](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Ha a teszt értesítési üzenet nem küldhető el, a StorSimple Device Manager szolgáltatás egy megfelelő üzenetet jelenít meg. Kattintson a **OK**, várjon néhány percet, és próbálja meg újra elküldeni az értesítési tesztüzenet.
      > 
      > 
   5. Kattintson a lap alján **mentése** kattintva mentse a konfigurációt. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.
      
      ![Riasztások által küldött értesítő e-mail tesztelése](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási állapot

A StorSimple virtuális tömb feltételek különböző válaszul riasztásokat állít elő. A leggyakoribb riasztások feltételek a következők:

* **Kapcsolódási problémák** – ezek a riasztások fordulhat elő, ha az adatok átvitele nehézségekbe ütközik. Kommunikációs problémák adatátvitel során fordulhatnak elő, hogy és az Azure storage-fiók vagy a virtuális eszközök és a StorSimple Device Manager szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs problémák nagyon nehéz javítsa ki, mert nincs olyan sok ponton felmerülő hibákat. Meg kell először mindig ellenőrizze, hogy hálózati kapcsolatot és az Internet-hozzáférés elérhető speciális hibaelhárítás be a folytatás előtt. Portok és a tűzfal beállításaival kapcsolatos információkért látogasson el [StorSimple virtuális tömb rendszerkövetelmények](storsimple-ova-system-requirements.md). Segítség a hibaelhárításban, [kapcsolatos problémák elhárítása a Test-Connection parancsmaggal](storsimple-troubleshoot-deployment.md).
* **Teljesítményproblémák** – ezek a riasztások vannak okozza, ha a rendszer nem optimális, például a nagy terhelés alatt van.

Ezenkívül jelenhet meg biztonsági, a frissítések vagy a sikertelen feladatok kapcsolatos riasztásokat.

## <a name="alert-severity-levels"></a>Riasztások súlyossági szintjei

Riasztások különböző súlyossági szintek, attól függően, hogy a hatás, hogy a riasztás helyzet és a riasztás választ az rendelkezik. A súlyossági szintek a következők:

* **Kritikus** – Ez a riasztás olyan feltétel, amely érinti a sikeres, a rendszer teljesítménye válaszul van. TEENDŐ annak érdekében, hogy a StorSimple szolgáltatás nem szakad meg.
* **Figyelmeztetés** – Ez az állapot kritikus, ha nem sikerül válhat. Vizsgálja meg a helyzet kell, és teendője kell törölnie a problémát.
* **Információ** – Ez a riasztás információkat tartalmaznak, amelyek nyomon követése és a rendszer kezelése hasznos lehet.

## <a name="view-and-track-alerts"></a>Megtekintheti, és nyomon követéséhez értesítések

A StorSimple Device Manager szolgáltatás összefoglaló panel biztosít kiadványok súlyossági szint szerint, a virtuális eszközön értesítések száma.

![Riasztások irányítópult](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Milyen súlyossági szintről megnyitásához kattintson a **riasztások** panelen. Az eredmények csak az adott súlyossági szint megfelelő riasztások tartalmazzák.

![Riasztások jelentés riasztástípusnak hatóköre](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kattintson a listában, hogy több információt a riasztást, többek között a legutóbbi, a riasztás érkezett jelentés, az eszközön, és a riasztás feloldásához jelenítse meg a javasolt művelet a riasztás előfordulásainak száma a riasztást.

![Riasztások listája és részletek](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

A riasztás részleteit szövegfájlba másolhatja, ha elküldi az adatokat a Microsoft Support van szüksége. A javaslat követni és a riasztási feltétel helyszíni feloldva, után törölje a riasztást a listából. Válassza ki a riasztást a listából, és kattintson a **egyértelmű**. Több riasztás törléséhez minden riasztás választásához kattintson a bármely oszlop, kivéve a **riasztás** oszlop, és kattintson **törölje** törölni kell minden riasztás kiválasztása után.

Amikor rákattint **egyértelmű**, lesz lehetősége van arra, hogy a riasztás és a lépéseket, amelyek vett igénybe, ha a probléma megoldására vonatkozó megjegyzések. 

![riasztási megjegyzések](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Egyes események törlődik a rendszer, ha egy másik esemény akkor váltódik ki, új információkkal. 

## <a name="sort-and-review-alerts"></a>A rendezés és tekintse át a riasztások

A **riasztások** panel is legfeljebb 250 riasztások megjelenítése. Túllépte a riasztások számát is, ha nem az összes riasztás jelenik meg az alapértelmezett nézetben. Kombinálhatja testreszabása, mely riasztások jelennek meg a következő mezőket:

* **Állapot** – jelenítheti meg vagy **aktív** vagy **nincs bejelölve** riasztásokat. Aktív továbbra is folyamatban figyelmeztetéseket a rendszeren, amíg üres riasztások vagy manuálisan törölték egy rendszergazda, vagy programozott módon nincs bejelölve, mert a rendszer a riasztást kiváltó körülmény frissülnek az új adatokkal.
* **Súlyossági** – az összes súlyossági szint (kritikus, figyelmeztetés, tájékoztatás), vagy csak egy bizonyos súlyossági, például csak a kritikus riasztások riasztások jelenítheti meg.
* **Forrás** – forrásokból származó riasztások megjelenítése, vagy korlátozza azok, amelyeket a szolgáltatás vagy egyet vagy mind a virtuális eszközök a riasztásokat.
* **Időtartománynak** – megadásával a **a** és **való** dátumok és időbélyegeket, vessen egy pillantást riasztások érdekli időszakban.

## <a name="alerts-quick-reference"></a>Riasztások rövid összefoglalása

A következő táblázat a StorSimple-riasztásokat, amelyek akkor léphetnek fel, valamint további információkra és ajánlásokra ahol az rendelkezésre áll olyan. A StorSimple virtuális tömb riasztások egyet a következő kategóriákba sorolhatók:

* [Felhő csatlakozási riasztások](#cloud-connectivity-alerts)
* [Konfigurációs riasztásokat](#configuration-alerts)
* [Figyelmeztetések](#job-failure-alerts)
* [Teljesítményével kapcsolatos riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)
* [Riasztások frissítése](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhő csatlakozási riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Eszköz  *<device name>*  nem csatlakozik a felhőhöz. |Az elnevezett eszköz nem lehet kapcsolódni a felhőben. |Nem sikerült csatlakozni a felhőhöz. A hiba lehetséges oka a következők egyikét:<ul><li>Az eszközön a hálózati beállításokkal probléma lehet.</li><li>A tárfiók hitelesítő adatait a probléma lehet.</li></ul>Kapcsolódási problémák elhárításával kapcsolatos további információkért látogasson el a [helyi webes felhasználói felület](storsimple-ova-web-ui-admin.md) az eszköz. |

### <a name="configuration-alerts"></a>Konfigurációs riasztásokat

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| A helyszíni virtuális eszközök konfigurációját nem támogatott. |Lassú teljesítmény. |A jelenlegi konfiguráció teljesítménycsökkenést eredményezhet. Győződjön meg arról, hogy a kiszolgáló megfelel-e a minimális konfigurációs követelményeinek. További információkért látogasson el [a StorSimple virtuális tömb követelményei](storsimple-ova-system-requirements.md). |
| Futtatja kiosztott lemezterület <*eszköznév*>. |Figyelmeztetés. |Kevés a kiosztott lemezterület. Szabadítson fel lemezterületet, fontolja meg a munkaterhelések áthelyezése egy másik kötetre vagy megosztásra vagy adatok törlése. |

### <a name="job-failure-alerts"></a>Figyelmeztetések

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| A biztonsági mentési <*eszköznév*> nem sikerült végrehajtani. |Biztonsági mentési feladat sikertelen. |Nem sikerült létrehozni a biztonsági másolat. Vegye figyelembe a következők egyikét:<ul><li>Kapcsolódási problémák megakadályozzák, hogy a biztonsági mentési művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek-e kapcsolódási problémák. Kapcsolódási problémák elhárításával kapcsolatos további információkért látogasson el a [helyi webes felhasználói felület](storsimple-ova-web-ui-admin.md) a virtuális eszköz.</li><li>Elérte a rendelkezésre álló tár vonatkozó korlátozást. Szabadítson fel lemezterületet, fontolja meg a törlése minden biztonsági másolatának már nem szükséges.</li></ul> Javítsa ki a hibákat, törölheti a riasztást, és próbálja megismételni a műveletet. |
| A klón <*eszköznév*> nem sikerült végrehajtani. |Klónozza a feladat sikertelen. |Nem sikerült létrehozni a klónozott. Vegye figyelembe a következők egyikét:<ul><li>A biztonságimásolat-lista nem lehet érvényes. Frissítse a listát ellenőrizze az érvényességét.</li><li>Kapcsolódási problémák megakadályozzák, hogy a Klónozási művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek-e kapcsolódási problémák.</li><li>Elérte a rendelkezésre álló tár vonatkozó korlátozást. Szabadítson fel lemezterületet, fontolja meg a törlése minden biztonsági másolatának már nem szükséges.</li></ul>Javítsa ki a hibákat, törölheti a riasztást, és próbálja megismételni a műveletet. |

### <a name="networking-alerts"></a>Hálózatkezelés riasztások
| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült kapcsolódni a hitelesítési szolgáltatás. |DataPath hiba |A hitelesítéshez használt URL-cím nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazza-e a StorSimple eszközhöz megadott URL-mintával. Az Azure portál URL-mintával további információkért látogasson el [StorSimple virtuális tömb hálózati követelményei](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Teljesítményével kapcsolatos riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Adatátvitel váratlan késést tapasztal. |Lassú az adatátvitelt. |Sávszélesség-szabályozási hiba történik, amikor a túllépi a méretezhetőségi célok tároló szolgáltatás. A tároló szolgáltatás ezt a végzi annak érdekében, hogy nincs egyetlen ügyfél vagy a bérlői csökkenti a többi szolgáltatás használhatja. Az Azure storage-fiók elhárításával kapcsolatos további információkért látogasson el [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Kevés a szabad lemezterület helyi lefoglalás <*eszköznév*>. |Lassú válasz időpontja. |a kiépített mérete 10 %-át <*eszköznév*> le foglalva a helyi eszközön, és most kevés a foglalt területet. Terhelése <*eszköznév*> generál egy nagyobb mértékű forgalom, vagy előfordulhat, hogy nemrég áttelepített nagy mennyiségű adatot. Emiatt előfordulhat, a csökkentett teljesítményt. Fontolja meg a probléma megoldásához az alábbi műveletek egyikét:<ul><li>Növelje meg a felhő sávszélesség erre az eszközre.</li><li>Csökkentse vagy munkaterhelések áthelyezése egy másik kötetet vagy megosztást.</li></ul> |

### <a name="security-alerts"></a>Biztonsági riasztások

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| A jelszó <*eszköznév*> múlva le fog járni <*szám*> nap. |Jelszó figyelmeztetés. |A jelszó lejár < szám < nap. Fontolja meg a jelszót. További információkért látogasson el [megváltoztatása a StorSimple virtuális tömb eszköz rendszergazdai jelszava](storsimple-virtual-array-change-device-admin-password.md). |

### <a name="update-alerts"></a>Riasztások frissítése

| Figyelmeztető szöveg | Esemény | További információ / javasolt műveletek |
|:--- |:--- |:--- |
| Új frissítések érhetők el az eszközhöz. |A StorSimple virtuális tömbhöz frissítések érhetők el. |Új frissítések telepítése a **karbantartási** lap. |
| Sikerült nem új frissítések keresése a <*eszköznév*>. |A frissítés sikertelen. |Hiba történt az új frissítések telepítése közben. Manuálisan telepítheti a frissítéseket. Ha a probléma továbbra is fennáll, forduljon a [Microsoft Support](storsimple-contact-microsoft-support.md). |

## <a name="next-steps"></a>Következő lépések

* [További tudnivalók a StorSimple virtuális tömb](storsimple-ova-overview.md).

