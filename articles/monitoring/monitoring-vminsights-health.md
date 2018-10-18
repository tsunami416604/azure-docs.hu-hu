---
title: Virtuális gép állapotának figyelése és az Azure Monitor-beli virtuális gépek |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan megismeri az állapotát a virtuális gép és az alapjául szolgáló operációs rendszer és az Azure Monitor-beli virtuális gépek.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2018
ms.author: magoedte
ms.openlocfilehash: 84314f64d8a96e65f63cb5c6051f7f5e902cd682
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387821"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms"></a>A virtuális gépek az Azure Monitor szolgáltatással az Azure virtuális gépek állapotának ismertetése
Az Azure több szolgáltatást tartalmaz, amelyek külön-külön végrehajtani egy adott szerepkör vagy a feladatot a figyelés területen, de biztosít az Azure-beli virtuális gépeken futó operációs rendszer egy részletes állapota szempontjából nem volt elérhető.  A Log Analytics vagy az Azure Monitor használatával különböző feltételek esetén felügyelheti, amíg nem tervezték őket modellezheti, és az alapvető összetevők állapotát vagy a virtuális gép általános állapotát.  Az Azure virtuális gépek Állapotfigyelő szolgáltatás figyelője proaktív módon figyeli a rendelkezésre állás és a Windows vagy Linux-alapú vendég operációs rendszer, amelyek a legfontosabb összetevők és a kapcsolatok, feltételek, amely meghatározza, hogy miként állapotát, a modell teljesítményét összetevők, és riasztást küld, ha a nem kifogástalan állapot észlelésekor.  

Megtekintése az Azure virtuális gép összesített állapotát és operációs rendszer alapjául szolgáló virtuális gépek Health, közvetlenül a virtuális gépről vagy az Azure Monitor egy erőforráscsoportba tartozó minden virtuális gépen az Azure Monitor szolgáltatással két perspektíva adatai figyelhető meg.

Ez a cikk segítséget nyújt a megtudhatja, hogyan mérheti fel gyorsan, kivizsgálásán és elhárításán észlelt állapotbeli problémák.

A virtuális gépek az Azure Monitor konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Monitor-beli virtuális gépek](monitoring-vminsights-onboard.md).

## <a name="monitoring-configuration-details"></a>Figyelési konfiguráció részletei
Ez a rész bemutatja az Azure Windows és Linux rendszerű virtuális gépek figyelése meghatározott alapértelmezett állapotára vonatkozó feltételek. Az összes állapotára vonatkozó feltételek riasztás előre konfigurálva, a nem megfelelő állapotú feltétel teljesülése esetén. 

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
- Fizikai lemez százalékos üresjárati idő
- DHCP-ügyfél szolgáltatásának állapota
- DNS-ügyfél szolgáltatásának állapota
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
- Hálózati Adapter állapota
- Processzor százalékban kifejezett DPC idő
- Processzor – processzoridő százalékos aránya
- Teljes processzoridő százalékos aránya
- Százalékban kifejezett teljes DPC idő
- Operációs rendszer memória rendelkezésre álló MB

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Egészségügyi élmény bemutatása
Mielőtt belevágna az állapotfigyelő szolgáltatás használata egyetlen virtuális gépet vagy virtuális gépek csoportján történő, fontos, röviden bemutatja, hogyan jelennek meg az adatokat, és a Vizualizációk képviselik tisztában kínálunk.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Közvetlenül a virtuális gép állapotának megtekintése 
Egy Azure virtuális gép állapotának megtekintéséhez válassza **Insights (előzetes verzió)** a bal oldali panelen, a virtuális gép. A virtuális gép insights oldalon **egészségügyi** alapértelmezés szerint meg nyitva, és a virtuális gép állapot nézetét mutatja be.  

![A virtuális gépek állapotának áttekintése a kiválasztott Azure virtuális gép az Azure Monitor](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Az a **egészségügyi** lapon, a szakasz a **Vendég virtuális gép állapota**, a táblázat bemutatja a virtuális gép aktuális állapotát, és a egy nem megfelelő állapotú összetevőre váltotta ki a virtuális gép állapota riasztások számát. Tekintse meg [riasztások kezelése és a egy riasztáskezelés](#alerting-and-alert-management) további részletekért.  

A megadott virtuális gép állapota állapotok a következők: 

* **Kifogástalan állapotú** – nem problémát észlelt a virtuális gép, és szükség szerint működik-e.  
* **Kritikus fontosságú** – egy vagy több kritikus problémák észlelhetők, amely kell oldani annak érdekében, hogy a normál működés visszaállítása a várt módon. 
* **Figyelmeztetés** – egy vagy több problémák észlelhetők, amelyeket meg kell oldani, vagy az egészségügyi feltétel kritikus válhat.  
* **Ismeretlen** – Ha a szolgáltatás nem tudott egy kapcsolatot a virtuális géppel, az állapot módosul, állapota ismeretlen.  

Kiválasztásával **diagnosztikai állapot megtekintése** kattint, megnyílik egy oldal, a virtuális gép, kapcsolódó állapotára vonatkozó feltételek, állapotváltozások és más jelentős problémák, a virtuális géphez kapcsolódó összetevők-figyelési szolgáltatás által észlelt összes összetevőjét bemutató. További információkért lásd: [egészségügyi diagnosztikai](#health-diagnostics). 

Alatt a **összetevő állapota** szakaszban, a táblázat egy összesítő állapot kifejezetten az ilyen területeken állapotára vonatkozó feltételek által figyelt elsődleges teljesítmény kategória **CPU**,  **Memória**, **lemez**, és **hálózati**.  Az összetevők közül bármelyik kiválasztásával tartalmazza az összes az adott összetevő és a megfelelő állapot egyes aspektusait figyelési egyes állapotfigyelő feltétel oldal megnyitása.  

Health a Windows operációs rendszert futtató Azure virtuális gépből elérésekor a 5 leggyakoribb alapvető Windows-szolgáltatások állapotát a szakaszában látható **Core állapotfigyelő szolgáltatások**.  A szolgáltatások bármelyike kiválasztása az adott összetevő és az állapot figyelése állapotára vonatkozó feltételek ajánlati oldal megnyitása.  A tulajdonság panelen a állapotára vonatkozó feltételek nevére kattintva megnyílik, és itt tekintse át a konfigurációs részleteket, beleértve, ha a állapotára vonatkozó feltételek rendelkezik egy megfelelő Azure Monitor alert definiálva. További tudnivalókért lásd: [egészségügyi diagnosztikai és állapotára vonatkozó feltételek használatának](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Összesített virtuálisgép-perspektíva
Állapotadat-gyűjtés az összes, a virtuális gépek megtekintése egy erőforráscsoportot a portálon, a navigációs listából válassza ki a **Azure Monitor** majd **(előzetes verzió) virtuális gépek**.  

![VM-elemzés, monitorozás az Azure Monitor megtekintése](./media/monitoring-vminsights-health/vminsights-aggregate-health.png)

Az a **előfizetés** és **erőforráscsoport** legördülő listák, válassza ki a megfelelő erőforráscsoportot a virtuális gépeket a csoporthoz kapcsolódó, annak érdekében, hogy a jelentett állapot megtekintéséhez.  A kijelölés csak az állapotfigyelő szolgáltatás vonatkozik, és nem vállalunk pénzügyi teljesítmény vagy a térképen.

Az a **egészségügyi** lapon is tudja ismerje meg a következőket:

* Hány virtuális gépet a kritikus vagy nem megfelelő állapotú, és hány olyan (néven állapota ismeretlen) megfelelő vagy nem küldő adatok vannak?
* Operációs rendszer (OS) virtuális gépek mely jelent a nem kifogástalan állapot, és hány?
* Hány virtuális gépet a processzor, a lemez, a memória vagy a hálózati adaptert az állapot alapján kategorizáljuk, az észlelt probléma miatt sérült állapotban?  
* Hány virtuális gépet egy core operációs rendszer szolgáltatás állapota alapján kategorizáljuk által észlelt probléma miatt sérült állapotban?

Itt gyorsan azonosíthatja a proaktív figyelése a virtuális gép állapotára vonatkozó feltételek által észlelt leggyakoribb kritikus hibák, és tekintse át a virtuális gép állapota riasztás részleteinek és a kapcsolódó ismeretcikk célja, hogy segítsenek a diagnosztikai és a szervizelés a probléma.  Válassza ki valamelyik megnyitásához a súlyossági szinten pedig a [minden riasztás](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md#all-alerts-page) oldalon a súlyosság szerint szűrve.

A **operációs rendszer által a virtuális gép terjesztési** lista mutatja azokat a Windows-kiadás vagy a Linux-disztribúció, és azok verziószáma szereplő virtuális gépek. Az egyes operációs rendszert, a virtuális gépek bontásban további a virtuális gép alapján. 

![Virtuális gép Insights virtuális gép terjesztési perspektíva](./media/monitoring-vminsights-health/vminsights-vmdistribution-by-os.png)

A megadott virtuális gép állapota állapotok a következők: 

* **Kifogástalan állapotú** – nem problémát észlelt a virtuális gép, és szükség szerint működik-e.  
* **Kritikus fontosságú** – egy vagy több kritikus problémák észlelhetők, amely kell oldani annak érdekében, hogy a normál működés visszaállítása a várt módon. 
* **Figyelmeztetés** – egy vagy több problémák észlelhetők, amelyeket meg kell oldani, vagy az egészségügyi feltétel kritikus válhat.  
* **Ismeretlen** – Ha a szolgáltatás nem tudott egy kapcsolatot a virtuális géppel, az állapot módosul, állapota ismeretlen.  

Kattintson bármely oszlop elem – a **virtuális gépek száma**, **kritikus**, **figyelmeztetés**, **kifogástalan** vagy **ismeretlen** , részletesebb a **virtuális gépek** lapon kiválasztott oszlop megfelelő szűrt eredmények listájának megtekintéséhez. Például, ha azt át szeretné tekinteni a futó összes virtuális gép **Red Hat Enterprise Linux kiadásban 7.5**, kattintson a a **virtuális gépek száma** értéke, hogy az operációs rendszer, és megnyílik a következő oldalon a megfelelő virtuális gépek listája a szűrő és azok ismert állapotát.  

![Példa összesítésre, Red Hat Linux rendszerű virtuális gépek](./media/monitoring-vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Az a **virtuális gépek** lapon, ha a virtuális gépek az az oszlop nevére **virtuális gép neve**, átirányítjuk a Virtuálisgép-példány lapjához a riasztás további részleteit tartalmazó és a feltételek állapotproblémák azonosított, amelyek vannak Ez hatással lenne a kiválasztott virtuális gép.  Itt szűrhet az egészségügyi állapotának részletei kattintva **állapot** ikonra a bal felső sarkában az oldalon tekintheti meg, mely összetevők sérült állapotban, vagy a virtuális gép állapota riasztásait váltotta ki egy nem megfelelő állapotú összetevőre tekintheti meg riasztás súlyossága szerint osztályozva.    

A virtuális gépek listája nézet a virtuális gép nevére kattintva megnyílik a **egészségügyi** oldal, amely a kiválasztott virtuális gép, hasonlóképpen, ha a kiválasztott **Insights (előzetes verzió)** közvetlenül a virtuális gépről.

![A kiválasztott Azure virtuális gép virtuális gép insights](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Itt jelenik meg egy összegző **állapot** a virtuális gép és **riasztások**, kategorizált által súlyosságának, amelyek tartalmazzák a virtuális gép állapota riasztás jelenik meg, ha az állapot a állapota nem kifogástalan változik egy állapotára vonatkozó feltételek.  Kiválasztásával **kritikus állapotban lévő virtuális gépek** megnyit egy oldalt egy vagy több kritikus állapotban lévő virtuális gépek listáját.  Az állapot ellenőrzése a jelennek meg a virtuális gépek, a lista egyik kattint a **egészségügyi diagnosztikai** nézet a virtuális gép.  Itt találja meg, melyik állapotára vonatkozó feltételek állapot állapotprobléma lépett van tükrözve. Ha a **egészségügyi diagnosztikai** oldal megjelenik, azt mutatja, hogy a virtuális gép és a társított állapotára vonatkozó feltételek aktuális állapotát az összes összetevője.  Tekintse meg a [egészségügyi diagnosztikai](#health-diagnostics) további részleteket a következő szakaszban.  

Kiválasztásával **megtekintheti az összes állapotára vonatkozó feltételek** minden elérhető ez a szolgáltatás állapotának feltétel listáját tartalmazó oldal megnyitása.  Az adatokat tovább szűrhetők alapján a következő beállításokat:

* **Típus** – feltételek típusa feltételek felmérése, és a figyelt virtuális gép általános állapotának összesítő állapot három típusú van.  
    a. **Egység** – a virtuális gép bizonyos elemeit méri. Ez a feltételtípus egészségügyi előfordulhat, hogy lehet egyik teljesítményszámláló ellenőrzése az összetevő teljesítményének meghatározására szintetikus tranzakciók végrehajtására, vagy tekintse meg a hibát jelző esemény parancsprogram futtatása.  Alapértelmezés szerint a szűrő értéke egység.  
    b. **Függőségi** – különböző entitások állapotait. Ez állapotára vonatkozó feltételek lehetővé teszi, hogy a függő entitás, amely a sikeres működés támaszkodik más típusú állapotát egy entitás állapotát.  
    c. **Összesített** -biztosít hasonló állapotára vonatkozó feltételek kombinált állapotát. Egység- és függőségfigyelők egészségügyi feltétel mellett egy összesített állapotát feltétel általában lesz konfigurálva. Jobb általános szervezésén egy entitás megcélzó számos különböző egészségügyi kritériumok, valamint összesített állapotát feltételt biztosít egyedi állapotokat különböző entitáskategóriákról.

* **Kategória** -állapotára vonatkozó feltételek hasonló típusú csoport feltételek számára a jelentéskészítéshez használt típusát.  Vagy **rendelkezésre állási** vagy **teljesítmény**.

Áthatoló lejjebb melyik példányai nem megfelelő állapotú elemre kattintva megtekintheti a **nem megfelelő állapotú összetevőre** oszlop.  A lapon egy táblázat az összetevők, amelyek kritikus állapotban van.    

## <a name="health-diagnostics"></a>Diagnosztikai állapot
**Diagnosztikai állapot** lap lehetővé teszi a virtuális gép, kapcsolódó állapotára vonatkozó feltételek, az állapotváltozásokat, az összetevők megtekintésére és a virtuális géphez kapcsolódó objektumainak figyelése nyomán észlelt más jelentős problémák. 

![Példa egy virtuális gép állapotának diagnosztikája lap](./media/monitoring-vminsights-health/health-diagnostics-page-01.png)

Diagnosztikai állapot a következő módon indíthatja el.

* Összesítő állapotok szerint az összes virtuális gép az Azure monitorban az összesített virtuális gép szempontjából.  Az a **egészségügyi** lap, kattintson az ikonra az **kritikus**, **figyelmeztetés**, **kifogástalan**, vagy **ismeretlen** a szakasz alatt állapota **Vendég virtuális gép állapota** és indítson részletes elemzést az oldal, amely felsorolja az összes virtuális gép megfelelő szűrt kategóriát.  Kattintson az értéket a a **állapot** oszlop hatókörét arra, hogy adott virtuális gép állapota diagnosztikai nyílik meg.      

* Operációs rendszer az Azure monitorban az összesített virtuális gép szempontjából. Alatt **VM terjesztési**, az oszlop értékei közül bármelyik kiválasztásával megnyílik az **virtuális gépek** lapon és a tábla a szűrt kategória megfelelő listáját adja vissza.  Kattintson a érték a **állapot** oszlop egészségügyi diagnosztikai megnyílik a kiválasztott virtuális géphez.    
 
* A Vendég virtuális Gépen a virtuális gépek az Azure Monitor **egészségügyi** fülre kattintva **egészségügyi diagnosztika megtekintése** 

Diagnosztikai állapot az állapotinformációkat a következő kategóriákba sorolhatók: 

* Rendelkezésre állás
* Teljesítmény
 
Az összes állapotára vonatkozó feltételek definiált egy kiválasztott cél jeleníti meg a megfelelő kategóriában. 

Állapotára vonatkozó feltételek állapota határozza meg a három állapot – egyik *kritikus*, *figyelmeztetés*, és *kifogástalan*. Van egy másik állapothoz *ismeretlen*, amely nem kapcsolódik állapotát, de a szolgáltatás által ismert figyelési állapotát jelöli.  

Az alábbi táblázat részletesen az állapotokat az egészségügyi diagnostics jelöli.

|Ikon |Állapot |Jelentés |
|-----|-------------|------------|
| |Kifogástalan |Az állapot állapota kifogástalan, ha meghatározott egészségügyi feltételek belül. Egy szülő összesítő figyelő esetén a health tekercsben felfelé és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Kritikus |Az állapot, kritikus fontosságú, ha nem található a meghatározott egészségügyi feltétel. Egy szülő összesítő figyelő esetén a health tekercsben felfelé és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Figyelmeztetés |A állapota figyelmet igényel, hogy meghatározott egészségügyi feltétel, ahol egy azt jelzi, hogy két küszöbérték közötti egy *figyelmeztetés* állapota és a egy másik jelzi egy *kritikus* állapota. Esetén a fölérendelt összegző figyelő esetén, ha egy vagy több gyermek figyelmeztetési állapotba kerül, akkor jelenik meg a szülő *figyelmeztetés* állapota. Ha van, amely a gyermek egy *kritikus* és a egy másik alárendelt egy *figyelmeztetés* állapot, a fölérendelt összegző megjelenik egy állapotát *kritikus*.|
| |Ismeretlen |Az állapot szerepel egy *ismeretlen* állapot, amikor az objektum állapota nem számítható ki több okból, például nem tud adatokat gyűjteni, a szolgáltatás nem inicializált stb.| 

Diagnosztikai állapotlap három fő részből áll:

* Összetevőmodell 
* Állapotra vonatkozó feltételek
* Állapotváltozások 

![Szakaszait állapotának diagnosztikája lap](./media/monitoring-vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Összetevő-modell
A Health-diagnosztika oldal bal szélső oszlopában az összetevő modellje. Összes összetevőt és annak a felderített példányait, amelyeket a virtuális gép társítva, ebben az oszlopban jelennek meg. 

A következő példában a felderített összetevők olyan lemez, logikai lemez, processzor, memória és operációs rendszer. Ezek az összetevők több példánya felderítésének és jelenik meg ebben az oszlopban, a logikai lemez két példányaival **/**, **/rendszerindító**, és   **/mnt/erőforrás**, hálózati adapter egy példánya **eth0**, lemez két példánya **sda** és **sdb**, két processzor, példányra **0 és 1**, és a egy **Red Hat Enterprise Linux Server kiadás 7.4 (Maipo) (operációs rendszer)**. 

![Az egészségügyi diagnosztikai megjelenített példa összetevőmodellből](./media/monitoring-vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Állapotára vonatkozó feltételek
A középső oszlopban, az állapot diagnosztikai lapon van a **állapotára vonatkozó feltételek** oszlop. Az a virtuális gép meghatározott állapotmodell egy hierarchikus fa jelenik meg. A virtuális gép állapotmodell egység, függőség és összesített állapotára vonatkozó feltételek áll.  

![Az egészségügyi diagnosztikai megjelenített példa állapotára vonatkozó feltételek](./media/monitoring-vminsights-health/health-diagnostics-page-healthcriteria.png)

Egészségügyi kritériumnak bizonyos feltételeknek, egy küszöbértéket és a egy entitás, és így tovább állapotának bekéréséhez. a felügyelt példány állapotát méri. Egészségügyi kritériumnak van két vagy három állapotokat, a fenti szakaszban leírtak szerint. Csak egy lehetséges állapottal lekérdezhet, az egészségügyi feltétel is lehet. 

A cél általános állapotát annak állapotára vonatkozó feltételek az állapotközpontú modellről meghatározott állapotát határozzák meg. Ez állapotára vonatkozó feltételek a cél közvetlenül megcélzó megcélzó a cél keresztül függőségi egészségügyi feltételt működés közbeni összetevők állapotára vonatkozó feltételek kombinációja lesz. Ezt a hierarchiát mutatja be a **állapotára vonatkozó feltételek** állapotának diagnosztikája lap részében. A állapotán alapuló szabály az összesítő és a függőségfigyelőkre állapotára vonatkozó feltételek konfigurációjának részét képezi. Alapértelmezés szerint ez a funkció a szakaszában részeként futó állapotára vonatkozó feltételek listáját találja [figyelési konfiguráció részletei](#monitoring-configuration-details).  

A következő példában az összesített állapotát feltétel **központi Windows-szolgáltatások összesítése** egy Windows-alapú virtuális gép felméri a kritikus fontosságú egyedi szolgáltatás állapotára vonatkozó feltételek alapján Windows-szolgáltatások állapotát. Például a DNS, DHCP stb., minden egyes szolgáltatás állapotának kiértékelése és az egészségügyi van összesítve a megfelelő kumulatív egészségügyi feltétel (ahogy az alább látható).  

![Állapot összegzése példa](./media/monitoring-vminsights-health/health-diagnostics-windows-svc-rollup.png)

Az állapotát a **központi Windows-szolgáltatások összesítése** összesíti az állapotát a **operációs rendszer rendelkezésre állását**, amely végül összesíti a **rendelkezésre állási** a virtuális gép. 

Állapotára vonatkozó feltételek **egység** típusa lehet módosítani az sokkal jobb és a kiválasztja a három pontra hivatkozásra kattintva konfigurációjuk **részletek megjelenítése** a konfigurációs panel megnyitásához. 

![A health feltételek példa konfigurálása](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-03.png)

A konfiguráció panelen a kiválasztott egészségügyi feltételeinek, ebben a példában a **logikai lemez szabad terület** kétállapotú figyelő, ami azt jelenti, csak az változik, mert a küszöbértéket, más numerikus érték konfigurálható Kifogástalan állapot kritikusra vált.  Előfordulhat, hogy más állapotfigyelő feltétel három állapot, ahol konfigurálhatja a egy értéke a figyelmeztetési és a kritikus állapot állapotküszöb.  

>[!NOTE]
>Egészségügyi feltételek konfigurációs módosítás alkalmazása az egyik példányhoz alkalmazza a rendszer az összes figyelt példányokat.  Például, ha kiválasztja **/mnt/erőforrás** és módosítása a **logikai lemez szabad terület** küszöbérték nem vonatkozik a csak az adott példány, de más logikai lemez példányait felderített és figyelni a A VIRTUÁLIS GÉP.
>

![. Egy egység figyelő például egy állapotára vonatkozó feltételek konfigurálása](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-04.png)

Ha azt szeretné, ha többet szeretne megtudni az állapotjelző ismeretcikkek szerepelnek segítségével azonosíthatja a problémákat, okok vagy megoldását.  Kattintson a **információk megtekintéséhez** a lapon és a egy új lapon nyílik meg a böngészőben, az adott ismeretcikk megjelenítése.  Bármikor, áttekintheti az összes virtuális gépek Állapotfigyelő szolgáltatás részét képező Azure Monitor állapota feltétel ismeretcikkek [Itt](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Állapotváltozások
Az állapot diagnosztikai lapon a jobb szélső oszlop **állapotváltozások**. A kijelölt állapotára vonatkozó feltételek társított összes állapotváltozások listáját a **állapotára vonatkozó feltételek** szakasz vagy a virtuális gép, ha a kiválasztott virtuális gép az állapotváltozás körülményeiről a **Összetevőmodellből** vagy **Állapotára vonatkozó feltételek** táblázat. 

![Az egészségügyi diagnosztikai megjelenített példa állapotváltozások](./media/monitoring-vminsights-health/health-diagnostics-page-statechanges.png)

Ez a szakasz feltételek állapota és a kapcsolódó idő felül a legfrissebb állapot szerint rendezett áll.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Összetevő-modell, és állapotára vonatkozó feltételek és állapot-oszlopok módosítása 
A össze az a három oszlop van kapcsolva egymással egymással. Amikor egy felhasználó kijelöl egy felderített példányt az Összetevőmodellből a **állapotára vonatkozó feltételek** szakaszban vannak leszűrve adott összetevő nézetre, és ennek megfelelően a **állapotváltozás** frissül a kiválasztott alapján állapotára vonatkozó feltételek. 

![Felügyelt példány és az eredmények kiválasztása – példa](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-02.png)

A fenti példában, amikor kiválaszt egy **/mnt (logikai lemez)**, a állapotára vonatkozó feltételek fa a szűrt **/mnt (logikai lemez)**. A **rendelkezésre állási** és **teljesítmény** lapok szűr a rendszer ennek megfelelően túl. A **Állapotváltozási** az oszlopban látható az állapotváltozás körülményeiről rendelkezésre állásának alapján **/mnt (logikai lemez)**. 

Frissített állapot megtekintéséhez kattintson is frissítheti az diagnosztikai oldalt a **frissítése** hivatkozásra.  Alapján előre definiált lekérdezési időközét az egészségügyi feltétel állapot frissítése esetén ez a feladat lehetővé teszi a várakozás elkerülése érdekében, és tükrözi a legfrissebb állapot.  A **feltételek állapota** szűrő engedélyezi-e a kiválasztott állapota – a eredményeket hatókörét kifogástalan állapotú, figyelmeztetés, kritikus, ismeretlen és az összes.  A **utolsó frissített** jobb felső sarokban az idő az utolsó időpont, amikor lett frissítve az diagnosztikai oldalt jelöli.  

## <a name="alerting-and-alert-management"></a>Riasztások és a riasztások kezelése 
Virtuális gépek Állapotfigyelő szolgáltatás az Azure Monitor integrálható [Azure Alerts](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) és riasztást jelenít meg, ha az előre meghatározott állapotára vonatkozó feltételek módosítása a kifogástalan állapotú sérült állapotba kerül a feltétel észlelése esetén. Riasztások súlyosság – Sev 0-tól 4, a Sev 0 jelölő a legmagasabb súlyossági szint szerint kategorizálhatók.  

Virtuális gép állapota riasztások súlyosság szerint osztályozva száma érhető el a **egészségügyi** irányítópult a szakaszában **riasztások**. A riasztások teljes számát, vagy egy súlyossági szintet megfelelő számot kiválasztásakor a **riasztások** lap nyílik meg, és felsorolja a egyeztetésére vonatkozó beállítás az összes riasztás.  Például, ha a sor megfelelő a kiválasztott **Sev 1. szintű**, majd az alábbi nézet megjelenítéséhez:

![Az összes súlyossági szint 1 riasztás – példa](./media/monitoring-vminsights-health/vminsights-sev1-alerts-01.png)

Az a **riasztások** lap, akkor csak hatóköre nem a kijelölt megfelelő riasztások megjelenítése, de az is szűrve **erőforrástípus** csak az állapotriasztások váltotta ki a virtuális gép típusú erőforrást jeleníti meg.  Ez megjelenik a riasztások listája, az oszlop alatt **célerőforrás**, ahol az Azure virtuális Gépen, a riasztás kiadását okozó számára az adott feltételeknek nem megfelelő állapotú feltétel teljesülésekor jeleníti meg.  

Más típusú erőforrások vagy szolgáltatások riasztásai nem célja, hogy ez a nézet szerepelnek, például a riasztások a lekérdezések alapján a Log Analytics vagy a mérőszám, amely riasztást küld, általában az alapértelmezett Azure Monitor lenne megtekintése [minden riasztás](../monitoring-and-diagnostics/monitoring-overview-alerts.md#all-alerts-page) lapot. 

Ez a nézet legördülő listák az oldal tetején lévő értékek kijelölésével szűrheti.

|Oszlop |Leírás | 
|-------|------------| 
|Előfizetés |Válasszon ki egy Azure-előfizetést. Csak a kijelölt előfizetésben riasztások nézetében megtalálhatók. | 
|Erőforráscsoport |Válasszon egy erőforráscsoportot. Csak azon riasztások t a kiválasztott erőforráscsoportban megtalálhatók a nézetet. | 
|Erőforrás típusa |Válassza ki egy vagy több erőforrás-típus. Alapesetben csak a cél riasztások **virtuális gépek** van jelölve, és ez a nézet tartalmazza. Ez az oszlop csak akkor használható, miután lett megadva egy erőforráscsoportot. | 
|Erőforrás |Válasszon ki egy erőforrást. Csak azon riasztások t célként adott erőforrásra a nézet szerepelnek. Ez az oszlop csak akkor használható, miután egy erőforrás-típus lett megadva. | 
|Severity |egy riasztás súlyossága használatba vétele, vagy válasszon *minden* minden súlyossági szint esetében riasztásokat tartalmazza. | 
|A figyelőre érvényes feltétel |Válassza ki a figyelési feltétel szűrése figyelmezteti, ha azok *Fired* a rendszer vagy *Megoldva* a rendszer, ha a feltétel már nem aktív. Válassza ki vagy *összes* tartalmazza az összes feltétel riasztásokat. | 
|Riasztás állapota |Válasszon egy riasztás állapotának *új*, *Acknowledge*, *lezárva*, vagy válasszon ki *összes* államok riasztásokat tartalmazza. | 
|Szolgáltatás monitorozása |Válasszon ki egy szolgáltatást, vagy válasszon *összes* tartalmazza az összes szolgáltatás. Csak a riasztások *VM Insights* Ez a funkció támogatja.| 
|Időtartomány| Csak az adott időtartamon belül aktivált riasztások nézetében megtalálhatók. Támogatott értékei a következők: az elmúlt egy órában, az elmúlt 24 órában, az elmúlt 7 napban és az elmúlt 30 napban. | 

A **riasztás részletes** ki kell választania egy riasztást a riasztás részleteinek megadása és az állapot módosítását lehetővé tevő lap jelenik meg. Riasztások kezelésével kapcsolatos további tudnivalókért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).  

>[!NOTE]
>Jelenleg nem támogatott állapotára vonatkozó feltételek új riasztásokat hozhat létre, vagy módosíthatja a meglévő erőforrásállapot-riasztási szabályok az Azure monitorban a portálról.  
>

![Riasztás részleteinek ablaktáblája tartalmazza a kijelölt riasztás](./media/monitoring-vminsights-health/alert-details-pane-01.png)

Riasztás állapota is módosítható egy vagy több riasztást a jelölje ki őket, és kiválasztja az **Állapotváltozáshoz** a a **minden riasztás** oldalán a bal felső sarkában található. Az a **riasztás állapotának módosítása** ablaktáblán válassza ki az állapotok valamelyikével adjon meg egy leírást a változás a a **Megjegyzés** mezőben, majd kattintson a **Ok** a módosítások véglegesítéséhez. Bár az adatokat a rendszer ellenőrzi, és a módosítások érvénybe lépnek, nyomon követheti a folyamat állapotát **értesítések** a menüből.  

## <a name="next-steps"></a>További lépések
Azonosíthatja a szűk keresztmetszeteket és a virtuális gépek teljesítményét a teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](monitoring-vminsights-performance.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](monitoring-vminsights-maps.md). 