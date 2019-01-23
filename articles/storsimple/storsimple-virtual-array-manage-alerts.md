---
title: A Microsoft Azure StorSimple Virtual Array riasztások megtekintése és kezelése |} A Microsoft Docs
description: Ismerteti a StorSimple Virtual Array riasztási feltételeket és a súlyosság és a StorSimple Manager szolgáltatás használata kezelheti a riasztásokat.
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
ms.openlocfilehash: 0cd3f506205a3ee4f4435b16fd2185eeb20aef3c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447224"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Riasztások kezelése a StorSimple Virtual Array az a StorSimple Eszközkezelő

## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatásban a riasztások a szolgáltatás oly módon, hogy tekintse át, és törölje a valós idejű alapon StorSimple Virtual Arrayt kapcsolatos riasztásokat biztosít. A riasztások használhatja a **szolgáltatás összefoglalás** központilag figyelheti a StorSimple Virtual Arrayt és a teljes Microsoft Azure StorSimple megoldás az állapotbeli problémák panel.

Ez az oktatóanyag leírja a riasztási értesítések, a gyakori riasztási feltételek, a riasztás súlyossági szintek konfigurálása és megtekintése és nyomon követheti a riasztások. Továbbá a riasztás rövid összefoglaló táblát is, amelyeket gyorsan keresse meg az adott riasztásra, és megfelelő választ tartalmazza.

![Riasztások lap](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>A riasztási beállítások konfigurálása

Kiválaszthatja, hogy szeretné-e a StorSimple Virtual Arrayt minden riasztási feltétel e-mailben értesítést. Ezenkívül azonosítsa a más riasztási értesítés címzettjeinek írja be az e-mail címüket a **e-mail további címzettjei** mezőbe pontosvesszővel elválasztva.

> [!NOTE]
> Megadhat egy legfeljebb 20 e-mail-címek száma virtuális tömb.

Miután engedélyezte a virtuális tömb kapcsolódó e-mail értesítések, a notification lista tagjainak minden alkalommal, amikor egy kritikus riasztás esetén e-mail üzenetet fog kapni. Az üzenetek küldését *storsimple-alerts-noreply@mail.windowsazure.com* és leírja a riasztási feltételt. Rákattinthat a címzettek **Unsubscribe** eltávolítása magukat az e-mail értesítési listája.

#### <a name="to-enable-email-notification-for-alerts"></a>Riasztásokhoz kapcsolódó e-mail értesítések engedélyezése

1. Nyissa meg a StorSimple-Eszközkezelő szolgáltatás és a a **felügyeleti** szakaszt, válassza ki, és kattintson a **eszközök**. Az eszközök jelennek meg a listában válassza ki, és kattintson az eszköz.
   
    ![riasztási beállítások](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Megnyílik a **beállítások** panelen. Az a **eszközbeállítások** szakaszban jelölje be **általános**. Megnyílik a **általános beállítások** panelen.
   
    ![riasztások értesítési konfigurációja](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Az a **általános beállítások** panelen lépjen a **riasztási beállítások** szakaszt, és állítsa be a következőket:
   
   1. Az a **e-mail értesítő engedélyezése** mezőben válassza **Igen**.
   2. Az a **E-mail-szolgáltatás-rendszergazdák** mezőben válassza **Igen** Ha azt szeretné, hogy a szolgáltatás-rendszergazda, és az összes többi rendszergazda is megkapja a riasztási értesítéseket.
   3. Az a **e-mail további címzettjei** mezőbe írja be annak az összes többi címzett, akiknek meg kell kapniuk a riasztási értesítéseket. A következő formátumban adja meg a neveket *someone@somewhere.com*. Az e-mail címeket pontosvesszővel. Beállíthatja, hogy egy legfeljebb 20 e-mail-címek száma virtuális eszközt.
      
       ![riasztások értesítési konfigurációja](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Egy teszt e-mail értesítés küldése, kattintson a **tesztelő e-mail küldése**. A StorSimple-Eszközkezelő szolgáltatás állapotüzenetek jelenik meg, akkor továbbítja a tesztértesítés.
      
       ![Figyelmeztetéseket küldött értesítő e-mail tesztelése](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Nem lehet elküldeni az értesítési tesztüzenet, ha a StorSimple-Eszközkezelő szolgáltatás egy megfelelő üzenet jelenik meg. Kattintson a **OK**, várjon néhány percet, és próbálja meg újból elküldeni az értesítési tesztüzenet.
      >
      >
   5. Kattintson a lap alján **mentése** kattintva mentse a konfigurációt. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.
      
      ![Figyelmeztetéseket küldött értesítő e-mail tesztelése](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple Virtual Array feltételek különböző válaszul riasztásokat állít elő. Az alábbiakban a leggyakoribb riasztási feltételeket:

* **Kapcsolódási problémák** – ezek a riasztások fordulhat elő, ha nehézségekbe ütközik az adatok átvitele. Kommunikációs előfordulhatnak problémák az adatok átvitel során, és az Azure storage-fiókból vagy a virtuális eszközök és a StorSimple-Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. Kommunikációs problémák az egyes nagyon nehéz javításához, mert vannak olyan sok meghibásodási pontokat. Először mindig ellenőrizze, hogy hálózati kapcsolatot és az Internet-hozzáférés érhetők el speciális hibaelhárítás be a folytatás előtt. Portok és a tűzfal beállításaival kapcsolatos további információkért lépjen [StorSimple Virtual Array rendszerkövetelményei](storsimple-ova-system-requirements.md). Segítség a hibaelhárításban, [hibaelhárítás a Test-Connection parancsmaggal](storsimple-troubleshoot-deployment.md).
* **Teljesítménnyel kapcsolatos problémák** – ezek a riasztások okozott végrehajtásakor a rendszer nem optimális, például a nagy terhelés alatt van.

Ezenkívül láthatja a biztonság, a frissítések és a sikertelen feladatok kapcsolatos riasztások.

## <a name="alert-severity-levels"></a>Riasztások súlyossági szintjei

Riasztások különböző fontossági szintek, attól függően, a hatást, amelyet a riasztás helyzet fog rendelkezni, illetve a riasztásra adott válasz nincs szükség van. A súlyossági szintek a következők:

* **Kritikus fontosságú** – Ez a riasztás egy feltételt, amely befolyásolja a rendszer teljesítményét a sikeres válasz szerepel. TEENDŐ ahhoz, hogy a StorSimple szolgáltatás ne szakadjon meg.
* **Figyelmeztetés** – Ez az állapot kritikus fontosságú, ha nem szűnik meg válhat. Vizsgálja meg a helyzet kell, és semmit sem kell törölnie a problémát.
* **Információk** – Ez a riasztás tartalmaz információkat, amelyek hasznosak lehetnek a követés és a rendszer kezelése.

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén biztosít egy gyors áttekintés a virtuális eszközökön a súlyossági szint szerint rendezett riasztások számát.

![Riasztások irányítópult](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

A súlyossági szintet megnyitásához kattintson a **riasztások** panelen. Az eredmények tartalmazzák a csak azokat a riasztásokat, amelyek megfelelnek a súlyossági szintet.

![Riasztási jelentés riasztási típus hatóköre](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kattintson egy riasztásra, a listában, hogy több információt a riasztás, beleértve az utolsó időpont, a riasztás lett jelentve, az eszköz és a javasolt művelet a riasztás feloldásához jelenítse meg a riasztás előfordulásainak száma.

![Lista riasztások és részletek](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Egy szövegfájlba másolhatja a riasztás részleteit, ha elküldi a Microsoft Support kell. Miután a javaslatot követi és feloldva a riasztási feltételt a helyszíni, törölje a riasztást a listából. Válassza ki a riasztást a listából, és kattintson a **egyértelmű**. Több riasztás törléséhez jelölje ki minden riasztást, kattintson bármely másik oszlopot kivéve a **riasztás** oszlop, és kattintson **törölje a jelet** törölni kell minden riasztás kiválasztása után.

Amikor rákattint **egyértelmű**, meg a riasztás és a lépéseket, akkor a probléma megoldásához igénybe vett kapcsolatos megjegyzéseit lehetőséget kell.

![riasztási megjegyzések](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Néhány esemény törlődik a rendszer, ha egy másik eseményt akkor indítja el az új adatokkal.

## <a name="sort-and-review-alerts"></a>Rendezés, és tekintse át a riasztások

A **riasztások** panelen legfeljebb 250 riasztások tudja megjeleníteni. A riasztások száma túllépte az, ha nem az összes riasztás megjelenik az alapértelmezett nézet. Kombinálhatja a következő mezőket, testreszabása a riasztások jelennek meg:

* **Állapot** – megjelenítheti vagy **aktív** vagy **nincs bejelölve** riasztásokat. Az Active továbbra is folyamatban van a figyelmeztetéseket a rendszerén, amíg átesett riasztások vagy manuálisan törölték a rendszergazda által, vagy programozott módon törölve, mert a rendszer a riasztási feltétel frissülnek az új adatokkal.
* **Súlyosság** – megjelenítheti az összes súlyossági szint (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos fontosság, például csak a kritikus riasztások riasztásokat.
* **Forrás** – riasztások megjelenítésének kezdete forrásokból, vagy korlátozza a riasztásokat, amelyek a szolgáltatás vagy a egyikét vagy mindegyikét a virtuális eszközök származnak.
* **Időtartomány** – megadásával a **a** és **való** dátum- és időbélyegek, tekintse meg riasztások során az Önt érdeklő időszakot.

## <a name="alerts-quick-reference"></a>Riasztásainak rövid összefoglalója

Az alábbi táblázatok sorolják fel, amelyek akkor fordulhatnak, valamint a további információk és javaslatok, ha elérhetők a StorSimple-riasztásokat. A StorSimple Virtual Array riasztások az alábbi kategóriák valamelyikébe tartoznak:

* [Felhőbeli csatlakozási riasztások](#cloud-connectivity-alerts)
* [Riasztások konfigurálása](#configuration-alerts)
* [Feladat miatti riasztások](#job-failure-alerts)
* [Az alkalmazásteljesítmény miatti riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőbeli csatlakozási riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Eszköz <*eszköznév*> nem csatlakozik a felhőben. |Az elnevezett eszköz nem lehet csatlakozni a felhőben. |Nem létesíthető kapcsolat a felhővel. Ezt a következők egyike okozhatja:<ul><li>Előfordulhat, hogy probléma adódott a hálózati beállításokat az eszközön.</li><li>Előfordulhat, hogy a tárfiók hitelesítő adatait a probléma.</li></ul>Kapcsolódási problémák hibaelhárításával kapcsolatos további információkért látogasson el a [helyi webes felületén](storsimple-ova-web-ui-admin.md) az eszköz. |

### <a name="configuration-alerts"></a>Riasztások konfigurálása

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A helyszíni virtuális eszközök konfigurációját nem támogatott. |A teljesítmény lecsökkenését tapasztalhatja. |A jelenlegi konfiguráció teljesítményromlást eredményezhet. Győződjön meg arról, hogy a kiszolgáló megfelel-e a minimális konfigurációs követelményeinek. További információért ugorjon [StorSimple Virtual Array – követelmények](storsimple-ova-system-requirements.md). |
| Elfogy létesített lemezterület <*eszköznév*\>. |Figyelmeztetés. |Kevés a létesített lemezterület. Szabadítson fel lemezterületet, fontolja meg a számítási feladatok áthelyezése egy másik kötetet vagy megosztást vagy töröl adatokat. |

### <a name="job-failure-alerts"></a>Feladat miatti riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A biztonsági mentési <*eszköznév* \> nem sikerült végrehajtani. |Biztonsági mentési feladat sikertelen. |Nem sikerült létrehozni a biztonsági mentést. Vegye figyelembe az alábbi lehetőségek közül:<ul><li>Kapcsolódási problémák akadályozzák meg a biztonságimásolat-készítési művelet sikeres befejeződését. Győződjön meg arról, hogy nincsenek kapcsolódási problémák. Kapcsolódási problémák hibaelhárításával kapcsolatos további információkért látogasson el a [helyi webes felületén](storsimple-ova-web-ui-admin.md) a virtuális eszköz.</li><li>Elérte a rendelkezésre álló tár határértéket. Szabadítson fel lemezterületet, érdemes a törlése minden biztonsági már nem szükséges.</li></ul> A problémák megoldásához, törölje a riasztást, és próbálja megismételni a műveletet. |
| Klónozás forrása: <*eszköznév* \> nem sikerült végrehajtani. |Klónozza a feladat sikertelen. |Nem sikerült létrehozni a klónt. Vegye figyelembe az alábbi lehetőségek közül:<ul><li>A biztonsági mentési lista nem lehet érvényes. Ellenőrizze, hogy továbbra is érvényes a lista frissítéséhez.</li><li>Kapcsolódási problémák akadályozzák meg a Klónozási művelet sikeres befejeződését. Győződjön meg arról, hogy nincsenek kapcsolódási problémák.</li><li>Elérte a rendelkezésre álló tár határértéket. Szabadítson fel lemezterületet, érdemes a törlése minden biztonsági már nem szükséges.</li></ul>A problémák megoldásához, törölje a riasztást, és próbálja megismételni a műveletet. |

### <a name="networking-alerts"></a>Hálózatkezelés riasztások
| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Nem sikerült csatlakozni a hitelesítési szolgáltatást. |DataPath hiba |A hitelesítéshez használt URL-cím nem érhető el. Győződjön meg arról, hogy a tűzfalszabályokban szerepel-e a StorSimple eszközhöz megadott URL-mintával. További információ az Azure Portalon URL-minta, nyissa meg [StorSimple Virtual Array hálózati követelményei](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Az alkalmazásteljesítmény miatti riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| Az adatátvitel nem várt késedelmeket tapasztal. |Lassú az adatátvitelhez. |Szabályozási hibák fordulhatnak elő, ha túllépi a skálázhatósági célokat, a storage szolgáltatás. A storage szolgáltatás azért teszi ezt, győződjön meg arról, hogy nincs egyetlen ügyfél vagy a bérlő használhatják a mások rovására service. Hibaelhárítás az Azure storage-fiókot további információért látogasson el [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Kevés a szabad lemezterület helyi foglalás <*eszköznév*\>. |Lassú válasz ideje. |számára kiosztott teljes méret 10 %-át <*eszköznév* \> fenntartva a helyi eszközön, és most kevés a a foglalt területet. A számítási feladatok a <*eszköznév* \> generál egy magasabb aránya adatváltozású vagy, előfordulhat, hogy nemrégiben áttelepítette-e nagy mennyiségű adat. Ez kisebb teljesítményt eredményezhet. Fontolja meg a probléma megoldásához az alábbi műveletek egyikét:<ul><li>Növelje a felhő sávszélesség erre az eszközre.</li><li>Csökkentse vagy számítási feladatok áthelyezése egy másik kötetet vagy megosztást.</li></ul> |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információ / ajánlott műveletek |
|:--- |:--- |:--- |
| A jelszó <*eszköznév* \> le fog járni <*szám* \> nap. |Jelszó figyelmeztetés. |A jelszó lejár, a <*szám* \> nap. Fontolja meg a jelszó módosítását. További információért ugorjon [a StorSimple Virtual Array eszköz rendszergazdai jelszavának módosítása](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>További lépések

* [További információ a StorSimple Virtual Array](storsimple-ova-overview.md).
