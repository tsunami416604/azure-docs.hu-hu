---
title: Az Azure-beli virtuális gépek állapotának megértése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan megérteni a virtuális gépek és az alapjául szolgáló operációs rendszerek és az Azure Monitor-beli virtuális gépek.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340145"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Az Azure-beli virtuális gépek állapotának ismertetése

Az Azure szolgáltatások az egyes szerepkörök vagy feladatokat tartalmaz a figyelési területen, de azt nem biztosít részletes állapot perspektívák operációs rendszer (OSs) üzemeltetett Azure-beli virtuális gépek (VM). Az Azure Monitor is használható, a különböző feltételek esetén, azonban nem célja az, hogy modellezheti, és az alapvető összetevők állapotát, vagy a virtuális gépek általános állapotát.

Virtuális gépek állapotát az Azure Monitor használatával folyamatosan figyelheti a rendelkezésre állás és a egy Windows vagy Linux-alapú vendég operációs rendszer teljesítményét. Az állapotfigyelő szolgáltatás modellt használ, amely jelzi a legfontosabb összetevők és azok, szempontokat tartalmazza, amely meghatározza, hogy miként mérheti az összetevő állapotát, és riasztást küld, ha azt észleli, hogy a nem kifogástalan állapot.

Az Azure virtuális gép és az alapjául szolgáló operációs rendszer általános állapotának megtekintése figyelhető két perspektíva adatai: közvetlenül a virtuális Gépet, vagy minden virtuális gépen az Azure Monitor egy erőforráscsoportban.

Ez a cikk bemutatja, hogyan gyorsan felmérheti, kivizsgálásán és elhárításán állapotbeli problémák észlelése esetén az Azure Monitor, virtuális gépek Állapotfigyelő szolgáltatás által.

A virtuális gépek az Azure Monitor konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Monitor-beli virtuális gépek](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Figyelési konfiguráció részletei

Ez a szakasz ismerteti az alapértelmezett állapotára vonatkozó feltételek figyelése az Azure Windows és Linux rendszerű virtuális gépek. Az összes állapotára vonatkozó feltételek olyan előre konfigurált riasztást küldjön, ha azt észleli, hogy a nem kifogástalan állapot.

### <a name="windows-vms"></a>Windows rendszerű virtuális gépek

- A memória érhető el (MB)
- (A logikai lemez) Írásonkénti átlagos másodperc
- (A lemez) Írásonkénti átlagos másodperc
- Olvasásonkénti átlagos logikai lemez másodperc
- Átvitelenkénti átlagos logikai lemez másodperc
- Olvasásonkénti átlagos másodperc
- Átvitelenkénti átlagos másodperc
- Lemezvárólista jelenlegi hossza (a logikai lemez)
- Lemezvárólista jelenlegi hossza (a lemez)
- Lemez százalékos üresjárati idő
- Fájlrendszer hibája vagy -sérülés
- Logikai lemez – szabad terület (%) Alacsony
- Kevés a logikai lemez szabad terület (MB)
- Logikai lemez százalékos üresjárati idő
- Memórialapok másodpercenként
- Százalékos sávszélesség használt olvasása
- Százalékos sávszélesség használt összesen
- Százalékos sávszélesség használt írási
- Használt előjegyzett memória százalékos aránya
- Lemez százalékos üresjárati idő
- DHCP-ügyfél szolgáltatásának állapota
- DNS Client Service Health
- RPC szolgáltatás állapota
- Kiszolgálói szolgáltatás állapota
- Teljes CPU-kihasználtság százaléka
- Windows Eseménynapló szolgáltatás állapota
- Windows tűzfal szolgáltatásának állapota
- Windows távoli felügyeleti szolgáltatásának állapota

### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

- Lemez átlagos Lemez mp/átvitel
- Lemez átlagos Lemez mp/Olvasás
- Lemez átlagos Lemez mp/írás
- Lemez állapota
- Logikai lemez – szabad terület
- Logikai lemez – szabad terület %
- Logikai lemez szabad Inode-OK
- Network Adapter Health
- Teljes processzoridő százalékos aránya
- Operációs rendszer memória rendelkezésre álló MB

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be, lépjen a [az Azure portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Virtuális gépek állapotát az Azure Monitor bemutatása

Az állapotfigyelő szolgáltatás egyetlen virtuális Gépet vagy virtuális gépek csoportján használja, mielőtt fontos tudni, hogyan jelennek meg az adatokat, és a Vizualizációk jelölik.

### <a name="view-health-directly-from-a-vm"></a>Közvetlenül a virtuális gép állapotának megtekintése

Egy Azure virtuális gép állapotának megtekintéséhez válassza **Insights (előzetes verzió)** a bal oldali panelen, a virtuális gép. A virtuális gép insights oldalon a **egészségügyi** lapon alapértelmezés szerint meg nyitva, és a virtuális gép állapot nézetét mutatja be.

![A virtuális gépek állapotának áttekintése a kiválasztott Azure virtuális gép az Azure Monitor](./media/vminsights-health/vminsights-directvm-health.png)

Az a **egészségügyi** lap **Vendég virtuális gép állapota**, a táblázat bemutatja a virtuális gép állapotát, és a egy nem megfelelő állapotú összetevőre váltotta ki a virtuális gép állapotriasztások teljes száma.

További információkért lásd: [riasztások](#alerts).

A virtuális gép meghatározott állapotokat az alábbi táblázat ismerteti:

|Ikon |Állapot |Jelentés |
|-----|-------------|---------------|
| |Kifogástalan |A virtuális gép meghatározott egészségügyi feltételek belül van. Ez az állapot azt jelzi, hogy nincs nincsenek problémák észlelése és a virtuális gép megfelelően működik-e. Egy szülő összesítő figyelő esetén, az állapotát összesíti, és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Kritikus |Az állapot nem része a meghatározott állapotfigyelő feltétel, amely azt jelzi, hogy egy vagy több kritikus problémák. Ezek a problémák normál működés visszaállítása kell címezni. Egy szülő összesítő figyelő esetén az állapot összesíti és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Figyelmeztetés |Meghatározott egészségügyi feltétel, ahol egy, a másik pedig a figyelmeztetési állapot azt jelzi, a kritikus állapot (három állapot állapot küszöbértékek konfigurálható), vagy ha egy nem kritikus probléma kritikus fontosságú problémákat okozhat két küszöbérték közötti állapota feloldatlan. Egy szülő összesítő figyelő esetén, az egy vagy több gyermek van figyelmeztetési állapotba kerül, a szülő fogja tartalmazni a figyelmeztetési állapot. Ha egy gyermek kritikus állapotba, és a egy másik alárendelt figyelmeztetési állapotban van, a fölérendelt összegző jelennek meg, ha kritikus állapot.|
| |Ismeretlen |Az állapot nem számítható ki több okból. A következő szakaszban a további részletek és a lehetséges megoldásokat biztosít. |

Egy ismeretlen állapot az alábbi problémákat okozhatja:

- Az ügynök újra lett konfigurálni, és már nem a munkaterület jelentések megadott, ha a virtuális gépek az Azure Monitor engedélyezve lett. Konfigurálja az ügynököt, hogy a munkaterület lásd a [hozzáadása vagy eltávolítása a munkaterület](../platform/agent-manage.md#adding-or-removing-a-workspace).
- A virtuális gép törölve lett.
- Az Azure Monitor szolgáltatással a virtuális gépekhez tartozó munkaterület törlése. A munkaterület helyreállíthatja, ha rendelkezik Premier szintű támogatási kedvezményekre. Lépjen a [Premier](https://premier.microsoft.com/) , és nyisson egy támogatási kérést.
- A megoldás függőségek törölve lett. A Log Analytics-munkaterület ServiceMap és InfrastructureInsights-megoldások engedélyezéséhez telepítse újra ezeket a megoldásokat használatával a [Azure Resource Manager-sablon](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Vagy használja az első lépések lapján található munkaterület konfigurálása lehetőséget.
- A virtuális gép le lett állítva.
- Az Azure-beli Virtuálisgép-szolgáltatás nem érhető el, vagy karbantartás végrehajtása folyamatban van.
- A munkaterület [napi adatok és adatmegőrzés korlát](../platform/manage-cost-storage.md) lett telepítve.

Válassza ki **diagnosztikai állapot megtekintése** egy virtuális gép összes összetevő megjelenítő lap megnyitásához kapcsolódó állapotára vonatkozó feltételek, állapotváltozások és más figyelési a virtuális géphez kapcsolódó összetevők által észlelt problémák.

További információkért lásd: [egészségügyi diagnosztikai](#health-diagnostics).

Az a **egészségügyi** a szakaszban egy táblázat a teljesítmény összetevők állapotára vonatkozó feltételek által figyelt állapotának összesítése. Ezen összetevők közé tartoznak **CPU**, **memória**, **lemez**, és **hálózati**. Egy összetevő kiválasztásával megnyílik egy oldal, amely felsorolja a figyelési feltétel, és az összetevő állapotát.

Egészségügyi használatakor, amely futtatja a Windows Azure virtuális gépből alatt látható-e a Windows az öt legaktívabb alapszolgáltatások állapotát **Core állapotfigyelő szolgáltatások**. Szolgáltatások kiválasztásával megnyílik egy oldal, amely felsorolja az adott összetevő együtt állapotába állapotára vonatkozó feltételek.

A tulajdonság panelen a állapotára vonatkozó feltételek nevét kiválasztva megnyílik. Ezen az ablaktáblán tekintse át a konfigurációs részleteket, többek között, ha a állapotára vonatkozó feltételek rendelkezik egy megfelelő Azure Monitor alert.

További információkért lásd: [egészségügyi diagnosztikai és állapotára vonatkozó feltételek használatának](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Virtuális gép összesített perspektíva

A health-gyűjtemény összes virtuális gépet egy erőforráscsoportban megtekintéséhez jelölje ki **Azure Monitor** a portálon, és válassza ki a navigációs listában **virtuális gépek (előzetes verzió)** .

![VM-elemzés, monitorozás az Azure Monitor megtekintése](./media/vminsights-health/vminsights-aggregate-health.png)

Az a **előfizetés** és **erőforráscsoport** legördülő listák, a csoportnak, azok jelentett állapot megtekintéséhez válassza ki a megfelelő erőforráscsoportot, amely tartalmazza a virtuális gépek kapcsolatos. A kijelölés csak az állapotfigyelő szolgáltatás vonatkozik, és nem jelenik meg **teljesítmény** vagy **térkép** lapokon.

A **egészségügyi** lapon a következő információkat tartalmazza:

* Hány virtuális gépet a kritikus vagy nem megfelelő állapotú, és hány kifogástalan állapotban, vagy nem küld adatokat (néven ismeretlen állapotba).
* Hány és milyen operációs rendszer által a virtuális gépek jelent a nem kifogástalan állapot.
* Hány virtuális gépet a processzor, a lemez, a memória vagy a hálózati adaptert az állapot alapján kategorizáljuk, az észlelt probléma miatt sérült állapotban.
* Hány virtuális gépet egy core operációs rendszer szolgáltatás állapota alapján kategorizáljuk által észlelt probléma miatt sérült állapotban.

Az a **egészségügyi** lapon, a kritikus problémák észlelhetők a virtuális gép, és tekintse át a riasztás részleteinek és kapcsolódó ismeretcikkeket állapotára vonatkozó feltételek alapján azonosíthatja. Ezek a cikkek végrehajtásában segít a diagnosztikai és, hibák elhárítása. Válassza ki valamelyik megnyitásához a súlyossági szinten pedig a [minden riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) oldalon a súlyosság szerint szűrve.

A **operációs rendszer által a virtuális gép terjesztési** lista mutatja azokat a Windows-kiadás vagy a Linux-disztribúció, és azok verziószáma szereplő virtuális gépek. Az egyes operációs rendszer, a virtuális gépek bontásban további a virtuális gép alapján.

![Virtuális gép Insights virtuális gép terjesztési perspektíva](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Válassza ki bármelyik oszlop, például **virtuális gépek száma**, **kritikus**, **figyelmeztetés**, **kifogástalan**, vagy **ismeretlen**. A szűrt eredmények megtekintése a **virtuális gépek** oldal, amely egyezik meg az oszlop ki van jelölve.

Például, tekintse át az összes Red Hat Enterprise Linux kiadásban 7.5 futtató virtuális gépeket, jelölje be a **virtuális gépek száma** értékét, hogy az operációs rendszer, és felsorolja a virtuális gépek megfelelő szűrőnek, és azok aktuális állapotát.

![Példa összesítésre, Red Hat Linux rendszerű virtuális gépek](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Az a **virtuális gépek** lapon, ha a virtuális gépek az az oszlop nevére **virtuális gép neve**, van irányítva az **Virtuálisgép-példány** lap. Ez az oldal nyújt további részleteket a riasztások és állapotbeli feltételek problémák, amelyek hatással vannak a kiválasztott virtuális gép. Az állapotadatok állapot szűrése kiválasztásával **állapot** ikonra a bal felső sarkában az oldalon tekintheti meg, mely összetevők sérült állapotban. Virtuális gép állapotriasztások váltotta ki olyan kategorizálta a riasztás súlyossága nem megfelelő állapotú összetevőre is megtekintheti.

Az a **Virtuálisgép-lista** megtekintéséhez nyissa meg a virtuális gép neve válassza ki a **egészségügyi** lapján a virtuális Gépeket, hasonlóképpen, ha a kiválasztott **Insights (előzetes verzió)** közvetlenül a virtuális gépről.

![A kiválasztott Azure virtuális gép virtuális gép insights](./media/vminsights-health/vminsights-directvm-health.png)

A **Insights (előzetes verzió)** lapon látható egy összesítő állapot a virtuális gép és a riasztások. Ez az állapot súlyossági, virtuális gép állapotriasztások következik be, amikor az objektum állapota megváltozott kifogástalan nem megfelelő állapotú, feltétel alapján jelölő kategorizálta. Kiválasztásával **kritikus állapotban lévő virtuális gépek** kritikus állapotban egy vagy több virtuális gépek listáját tartalmazó oldal megnyitása.

Az állapot-ellenőrzése egy, a virtuális gépek azt mutatja be, válassza a **egészségügyi diagnosztikai** nézet a virtuális gép. Ebben a nézetben megadhatja, hogy melyik állapotára vonatkozó feltételek egy állapot probléma van tükrözve. Ha a **egészségügyi diagnosztikai** oldal megjelenik, a Virtuálisgép-összetevők és azok kapcsolódó állapotára vonatkozó feltételek aktuális állapotával együtt jelenik meg.

További információkért lásd: [egészségügyi diagnosztikai](#health-diagnostics).

Kiválasztásával **megtekintheti az összes állapotára vonatkozó feltételek** minden elérhető ez a szolgáltatás állapotának feltétel listáját tartalmazó oldal megnyitása. Az adatokat tovább szűrhetők alapján a következő beállításokat:

* **típus**. Feltételek és és az általános rendszerállapotot a figyelt virtuális gép összesítő állapotára vonatkozó feltételek három típusa van:
    - **Egység**. A virtuális gépek bizonyos elemeit méri. Ez a feltételtípus egészségügyi előfordulhat, hogy lehet egyik teljesítményszámláló ellenőrzése az összetevő teljesítményének meghatározására szintetikus tranzakciók végrehajtásához parancsprogram futtatása vagy hibát jelző esemény az figyelése. Alapértelmezés szerint a szűrő egységhez van beállítva.
    - **Függőségi**. Itt egy másik entitás közötti állapotának összesítése. Ez állapotára vonatkozó feltételek lehetővé teszi, hogy a függő entitás, amely a sikeres működés támaszkodik más típusú állapotát egy entitás állapotát.
    - **Összesített**. Itt egy hasonló állapotára vonatkozó feltételek kombinált állapotát. Egység- és függőségfigyelők egészségügyi feltétel mellett egy összesített állapotát feltétel általában úgy vannak konfigurálva. Jobb általános szervezésén egy entitás megcélzó számos különböző egészségügyi kritériumok, valamint összesített állapotát feltételt biztosít egyedi állapotokat különböző entitáskategóriákról.

* **Kategória**. A jelentéskészítési célokból hasonló feltételek csoportosítására használhatók, állapotára vonatkozó feltételek típusa. Ezek a kategóriák **rendelkezésre állási** és **teljesítmény**.

Melyik példányai nem megfelelő állapotú megtekintéséhez válasszon egy értéket a a **nem megfelelő állapotú összetevőre** oszlop. Ezen a lapon egy táblázat az összetevőket, amelyek kritikus állapotban van.

## <a name="health-diagnostics"></a>Diagnosztikai állapot

A **egészségügyi diagnosztikai** lap lehetővé teszi az állapotközpontú modellről, a virtuális gépek megjelenítése. Ez az oldal összes Virtuálisgép-összetevők, kapcsolódó állapotára vonatkozó feltételek, állapotváltozások és más jelentős problémák, a virtuális géphez kapcsolódó figyelt összetevők által azonosított sorolja fel.

![Példa egy virtuális gép állapotának diagnosztikája lap](./media/vminsights-health/health-diagnostics-page-01.png)

Indítsa el a health diagnosztika a következő módszerekkel:

* Összesítő állapotok szerint az összes virtuális gép az Azure monitorban az összesített virtuális gép szempontjából:

    1. Az a **egészségügyi** lapra, jelölje be a ikonjára **kritikus**, **figyelmeztetés**, **kifogástalan**, vagy **ismeretlen** a szakasz alatt állapota **Vendég virtuális gép állapota**.
    2. Nyissa meg az oldal, amely felsorolja a megfelelő szűrt kategória összes virtuális gép.
    3. Válassza ki az értéket a **állapot** oszlop a hatóköre a virtuális gép állapota diagnosztikai megnyitásához.

* Által az Azure monitorban az összesített virtuális gép szempontjából. Alatt **VM terjesztési**, az oszlop értékei közül bármelyik kiválasztásával megnyílik az **virtuális gépek** lapon és a tábla a szűrt kategória megfelelő listáját adja vissza. Érték a kiválasztásával **állapot** oszlop egészségügyi diagnosztikai megnyílik a kiválasztott virtuális géphez.
 
* A Vendég virtuális Gépen a virtuális gépek az Azure Monitor **egészségügyi** fülre kattintva **diagnosztikai állapot megtekintése**.

Diagnosztikai állapot állapotinformációkat két kategóriába sorolhatók: rendelkezésre állását és teljesítményét.
 
A két kategória a szűrés nélküli összetevő, például a logikai lemez, a Processzor és így tovább, meghatározott állapotára vonatkozó feltételek tekinthet meg. Ezek a nézetek a feltételek vagy az eredmények szűréséhez vagy kategória szerint, amikor kiválaszt egy teljes nézetben lehet **rendelkezésre állási** vagy **teljesítmény**.

Emellett a feltételek kategória mellett látható a **állapotára vonatkozó feltételek** oszlop. Ha a feltételek nem egyezik meg a kiválasztott kategória a következő üzenetet **nem érhető el a kiválasztott kategória állapotára vonatkozó feltételek** jelenik meg a **állapotára vonatkozó feltételek** oszlop.

Egy állapotára vonatkozó feltételek állapotát egy másik négy különböző van definiálva: **Kritikus fontosságú**, **figyelmeztetés**, **kifogástalan**, és **ismeretlen**. Az első három is konfigurálható, ami azt jelenti, hogy közvetlenül a figyelők küszöbértékek módosíthatja a **állapotára vonatkozó feltételek** konfigurációs panelen. Ez akkor is lehetséges az Azure Monitor REST API-val [a figyelő a frissítés](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Ismeretlen** nem konfigurálható, és meghatározott forgatókönyvek számára van fenntartva.

A **egészségügyi diagnosztikai** lapon a három fő szakaszra osztható:

* Összetevő-modell
* Állapotra vonatkozó feltételek
* Állapotváltozások

![Szakaszait állapotának diagnosztikája lap](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Összetevő-modell

A bal szélső oszlopban a **egészségügyi diagnosztikai** lap **Összetevőmodellből**. Minden összetevő, amely a virtuális gép kapcsolódik, és azok aktuális állapotának ebben az oszlopban jelennek meg.

A következő példában a felderített összetevők a következők **lemez**, **logikai lemez**, **processzor**, **memória**, és  **Operációs rendszer**. Ezek az összetevők több példánya felderítésének és ebben az oszlopban jelenik meg.

Például a következő ábrán látható, hogy a virtuális gép rendelkezik-e a logikai lemezek két példánya **C:** és **D:** , amely egy kifogástalan állapotban vannak:

![Az egészségügyi diagnosztikai megjelenített példa összetevőmodellből](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Állapotára vonatkozó feltételek

A Health diagnosztika lapot center oszlopa **állapotára vonatkozó feltételek**. Az a virtuális gép meghatározott állapotmodell egy hierarchikus fa jelenik meg. A virtuális gép állapotmodell egység és összesített állapotára vonatkozó feltételek áll.

![Az egészségügyi diagnosztikai megjelenített példa állapotára vonatkozó feltételek](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Egészségügyi feltételt egy figyelt példányt, amely egy küszöbértéket, az entitásokhoz, és így tovább állapota lehet állapotát méri. Egészségügyi kritériumnak két vagy három konfigurálható egészségügyi állapot küszöbértékeket, rendelkezik, a fentebb leírt módon. Bármikor az egészségügyi feltétel csak egy lehetséges állapottal is lehet.

Az állapotközpontú modellről az átfogó cél állapotát és a cél összetevői meghatározó feltételeket határozza meg. A hierarchia feltételek látható a **állapotára vonatkozó feltételek** szakaszában a **egészségügyi diagnosztikai** lap.

Az állapot-összesítési házirend összesített állapotára vonatkozó feltételek konfigurációjának részét képezi (az alapértelmezett értékre van állítva **legrosszabb,** ). Ennek a funkciónak a részeként futó állapotára vonatkozó feltételek alapértelmezett készletét annak a [figyelési konfiguráció részletei](#monitoring-configuration-details) című szakaszát.

Is használhatja az Azure Monitor REST API [figyelő példányok listáját úgy erőforrás](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) az összes állapotára vonatkozó feltételek listáját. Ennek a feltételnek futtat az Azure VM-erőforrás konfigurációs adatait tartalmazza.

A **egység** egészségügyi feltételtípust rendelkezhet a három pontra a jobb oldalon a hivatkozásra kattintva módosíthatja annak konfigurációját. Válassza ki **részletek megjelenítése** a konfigurációs panel megnyitásához.

![A health feltételek példa konfigurálása](./media/vminsights-health/health-diagnostics-vm-example-02.png)

A konfiguráció panelen a kiválasztott állapotára vonatkozó feltételek, ha a példában a **átlagos másodpercek száma az írási**, a küszöbérték egy másik numerikus érték konfigurálható. Kétállapotú figyelő, ami azt jelenti, azt csak a módosíthatja **kifogástalan** való **figyelmeztetés**.

Más állapotára vonatkozó feltételek néha Háromállapotú, ahol konfigurálhatja a figyelmeztetési és a kritikus állapot küszöbértékei értékét használja. Az Azure Monitor REST API használatával is módosíthatja egy küszöbértéket [figyelési konfiguráció](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Egészségügyi feltételek konfigurációs módosítások alkalmazása az egyik példányhoz alkalmazza őket az összes figyelt példányokat. Például, ha kiválasztja **lemez-1 D:** majd módosítsa a **átlagos másodpercek száma az írási** küszöbértéket, a módosítás vonatkozik az összes példányra felderítésének és figyelésének a virtuális gépen.


![. Egy egység figyelő például egy állapotára vonatkozó feltételek konfigurálása](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Ha szeretne további tudnivalók a állapotára vonatkozó feltételek, segít azonosítani a problémákat, okok és megoldásaik ismeretcikkek bővítettük. Válassza ki **információk megtekintéséhez** weblapon, tekintse meg a kapcsolódó Tudásbázis-cikket.

A virtuális gépek állapotának és az Azure Monitor tartalmaz minden ismeretcikk áttekintéséhez lásd: [Azure Monitor állapota Tudásbázis dokumentáció](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Állapotváltozások

A jobb szélső oszlopban található a **egészségügyi diagnosztikai** lap **állapotváltozások**. Ez az oszlop megjeleníti a kiválasztott állapotára vonatkozó feltételek társított összes állapotváltozások a **állapotára vonatkozó feltételek** szakasz vagy a virtuális gép, ha a kiválasztott virtuális gép az állapotváltozás körülményeiről a **Összetevőmodellből** vagy  **Állapotára vonatkozó feltételek** táblázat.

![Az egészségügyi diagnosztikai megjelenített példa állapotváltozások](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A következő szakasz bemutatja az egészségügyi-feltételek állapota és a kapcsolódó idő. Ez az információ az oszlop tetején lévő legfrissebb állapotát mutatja.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Az Összetevőmodellből állapotára vonatkozó feltételek és állapotváltozások oszlopok társítása

A össze az a három oszlop van kapcsolva egymással egymással. Amikor kiválaszt egy példányt a **Összetevőmodellből** oszlop, a **állapotára vonatkozó feltételek** oszlopot adott összetevő nézetre van szűrve. Ennek megfelelően a **állapotváltozások** oszlop frissül a kiválasztott állapotára vonatkozó feltételek alapján.

![Felügyelt példány és az eredmények kiválasztása – példa](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Például, ha kiválasztja *lemez – 1 D:* alatti listából **Összetevőmodellből**, **állapotára vonatkozó feltételek** szűri a *lemez – 1 D:* , és  **Módosítások állapot** az állapotváltozás körülményeiről alapján rendelkezésre állását jeleníti meg *lemez – 1 D:* .

Egy frissített állapot megtekintéséhez kiválasztásával is frissítheti a Health diagnosztika lapot a **frissítése** hivatkozásra. Alapján előre definiált lekérdezési időközét az egészségügyi feltétel állapot frissítése esetén ez a feladat lehetővé teszi a várakozás elkerülése érdekében, és tükrözi a legfrissebb állapot. A **feltételek állapota** egy szűrő, amely lehetővé teszi, hogy azt a terjedelmet az eredmények a kiválasztott állapota alapján: Kifogástalan állapotú, figyelmeztetés, kritikus fontosságú, ismeretlen, és minden. A **utolsó frissített** idő a jobb felső sarokban a állapotának diagnosztikája lap utolsó frissítésekor jelöli.

## <a name="alerts"></a>Riasztások

A virtuális gépek állapotát az Azure Monitor integrálható [Azure Alerts](../../azure-monitor/platform/alerts-overview.md). A riasztást, ha előre meghatározott feltételek észlel, amikor változnak kifogástalanról sérült állapotba kerül. Riasztások, Sev 0, "a"-4-Sev 0, a legmagasabb szintű súlyosság szerint kategorizálhatók.

Riasztások nem műveletcsoport arra az esetre, ha a riasztás aktiválódott-e társítva. Az előfizetés tulajdonosa értesítések kell konfigurálnia a lépéseket követve a [riasztások konfigurálása](#configure-alerts) szakaszban.

Virtuális gép állapota riasztások súlyosság szerint osztályozva száma érhető el a **állapotfigyelő** irányítópult alatt a **riasztások** szakasz. A riasztások teljes számát, vagy egy súlyossági szintet megfelelő számot kiválasztásakor a **riasztások** lap nyílik meg, és felsorolja a egyeztetésére vonatkozó beállítás az összes riasztás.

Például, ha kiválasztja a megfelelő sor **Sev 1. szintű**, látni fogja az alábbi nézetben jelenítheti meg:

![Az összes súlyossági szint 1 riasztás – példa](./media/vminsights-health/vminsights-sev1-alerts-01.png)

A **minden riasztás** lap hatóköre nem csak a kijelölt megfelelő riasztások megjelenítése. Emellett szerint van szűrve **erőforrástípus** csak egy VM-erőforrás által kiváltott állapotriasztások megjelenítéséhez. Riasztási listán, az az oszlop is megjelenik ez a formátum **célerőforrás**, ahol láthatja az Azure virtuális Gépen a megnövelt riasztás egy nem megfelelő állapotú feltétel teljesülésekor.

Ebben a nézetben szereplő riasztások más típusú erőforrások vagy szolgáltatások nem tartozhat. Ezek a riasztások tartalmazzák a naplólekérdezések, vagy megtekintheti az alapértelmezett Azure Monitor lenne általában metrikákkal kapcsolatos riasztások alapján naplóriasztások [minden riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) lapot.

Ez a nézet a legördülő menükben az oldal tetején lévő értékek kijelölésével szűrheti.

|Oszlop |Leírás |
|-------|------------|
|Előfizetés |Válasszon ki egy Azure-előfizetést. Csak a kijelölt előfizetésben riasztások nézetében megtalálhatók. |
|Erőforráscsoport |Válasszon egy erőforráscsoportot. Csak azon riasztások t a kiválasztott erőforráscsoportban megtalálhatók a nézetet. |
|Erőforrás típusa |Válassza ki egy vagy több erőforrás-típus. Alapesetben csak a cél riasztások **virtuális gépek** van jelölve, és ez a nézet tartalmazza. Ez az oszlop csak akkor használható, miután lett megadva egy erőforráscsoportot. |
|Erőforrás |Válasszon ki egy erőforrást. Csak azon riasztások t célként adott erőforrásra a nézet szerepelnek. Ez az oszlop csak egy erőforrás-típus lett megadva akkor érhető el. |
|Severity |Jelöljön ki egy riasztás súlyossága vagy **összes** minden súlyossági szint esetében riasztásokat tartalmazza. |
|Figyelési feltétel |Jelölje be a szűrő riasztásokra figyelőre érvényes feltételt, ha aktivált vagy a rendszer által megoldott, ha a feltétel már nem aktív. Vagy válassza **összes** tartalmazza az összes feltétel riasztásokat. |
|Riasztás állapota |Válasszon egy riasztás állapotának **új**, **Acknowledge**, **lezárva**, vagy **összes** államok riasztásokat tartalmazza. |
|Szolgáltatás monitorozása |Jelöljön ki egy szolgáltatás vagy **összes** tartalmazza az összes szolgáltatás. Ez a funkció csak a virtuális gép insights riasztások támogatottak.|
|Időtartomány| Csak az adott időtartamon belül aktivált riasztások nézetében megtalálhatók. Támogatott értékei a következők: az elmúlt egy órában, az elmúlt 24 órában, az elmúlt 7 napban és az elmúlt 30 napban. |

Amikor kiválaszt egy riasztást a **riasztás részletes** lap jelenik meg. Ezen a lapon biztosít a riasztás részleteit, és módosíthatja annak állapota.

Riasztások kezelésével kapcsolatos további tudnivalókért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Új riasztások alapján állapotára vonatkozó feltételek vagy módosítása meglévő erőforrásállapot-riasztási szabályok az Azure monitorban a portálról létrehozása jelenleg nem támogatott.


![Riasztás részleteinek ablaktáblája tartalmazza a kijelölt riasztás](./media/vminsights-health/alert-details-pane-01.png)

Egy vagy több riasztást egy riasztási állapota módosíthatja, ha kiválasztja őket, és válassza **Állapotváltozáshoz** származó a **minden riasztás** lap bal felső sarokban. Válassza ki az egyik az államok a a **riasztás állapotának módosítása** panelen adjon meg egy leírást a változás a a **Megjegyzés** mezőben, majd válassza ki **Ok** a módosítások véglegesítéséhez. Ha az adatokat a rendszer ellenőrzi, és a módosítások érvénybe lépnek, nyomon követheti a folyamat állapotát **értesítések** menüjében.

### <a name="configure-alerts"></a>Riasztások konfigurálása
Nem kezelheti a meghatározott riasztás-felügyeleti feladatokat az Azure Portalról. Ezek a feladatok használatával kell végrehajtani a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Konkrétan:

- Engedélyezés vagy letiltás riasztást állapotára vonatkozó feltételek
- Feltételek állapotriasztások értesítéseinek beállítása

Valamennyi példa [ARMClient](https://github.com/projectkudu/armclient) a Windows-gépen. Ha nem ismeri ezt a módszert, lásd: [használatával ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Engedélyezheti vagy tilthatja le a riasztási szabály

Engedélyezheti vagy tilthatja le a konkrét állapotára vonatkozó feltételek, a tulajdonság egy riasztás **alertGeneration** módosítani kell, vagy értékkel **letiltott** vagy **engedélyezve**.

Azonosíthatja a *monitorid attribútumként* meghatározott állapottal feltételeinek, az alábbi példa bemutatja, hogyan lekérdezni a ezt az értéket a feltétel számára **teljesítményszámlálójának korlátját lemez másodperc / átviteli**:

1. Egy terminálablakban, írja be a **armclient.exe bejelentkezési**. Ezt kéri, hogy jelentkezzen be az Azure-bA.

2. Adja meg a következő parancsot az összes aktív az adott virtuális gép állapota feltételt és értékének azonosítása *monitorid attribútumként* tulajdonság:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Az alábbi példa bemutatja a kimenetét a *armclient GET* parancsot. Jegyezze fel az értékét *monitorid attribútumként*. Ez az érték megadása kötelező a következő lépéshez nevezhetjük kell a állapotára vonatkozó feltételek Azonosítóját adja meg, és hozzon létre egy riasztást a vonatkozó tulajdonság módosításával.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Adja meg a következő paranccsal módosíthatja a *alertGeneration* tulajdonság:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Adja meg a GET parancs, ellenőrizze, hogy a tulajdonság értéke 2. lépésben használt **letiltott**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Műveletcsoport társítása állapotára vonatkozó feltételek

A virtuális gépek állapotát az Azure Monitor SMS- és e-mail értesítéseket támogatja, amikor riasztások jönnek létre a nem megfelelő állapotú állapotára vonatkozó feltételek. Értesítések konfigurálása, vegye figyelembe az SMS-EK vagy e-mail értesítések küldéséhez konfigurált műveleti csoport neve.

>[!NOTE]
>Ez a művelet minden figyelt virtuális Gépre, amelyet szeretne értesítést kapni ellen kell elvégezni. Azt egy erőforráscsoportba tartozó összes virtuális gépekre nem vonatkozik.

1. Adja meg egy terminálablakot, *armclient.exe bejelentkezési*. Ezt kéri, hogy jelentkezzen be az Azure-bA.

2. Adja meg a műveletcsoport társítandó riasztási szabályok az alábbi parancsot:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Ellenőrizze, hogy a tulajdonság értékének **actionGroupResourceIds** sikeresen frissítve, adja meg a következő parancsot:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    A kimenet a következő feltételeknek kell hasonlítania:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>További lépések

- Korlátozások és általános virtuális gép teljesítmény-azonosításához tekintse meg a [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md).
- Felderített alkalmazások függőségeinek kapcsolatos további információkért lásd: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
