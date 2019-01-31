---
title: Virtuális gép állapotának figyelése és az Azure Monitor-beli virtuális gépek (előzetes verzió) |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan megismeri az állapotát a virtuális gép és az alapjául szolgáló operációs rendszer és az Azure Monitor-beli virtuális gépek.
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
ms.date: 01/30/2019
ms.author: magoedte
ms.openlocfilehash: 58da86140b97c5292d390b6f91502b7f0622986a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476842"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms-preview"></a>A virtuális gépek (előzetes verzió) és az Azure Monitor az Azure virtuális gépek állapotának ismertetése
Az Azure több szolgáltatást tartalmaz, amelyek külön-külön végrehajtani egy adott szerepkör vagy a feladatot a figyelés területen, de biztosít az Azure-beli virtuális gépeken futó operációs rendszer egy részletes állapota szempontjából nem volt elérhető.  A Log Analytics vagy az Azure Monitor használatával különböző feltételek esetén felügyelheti, amíg nem tervezték őket modellezheti, és az alapvető összetevők állapotát vagy a virtuális gép általános állapotát.  Az Azure virtuális gépek Állapotfigyelő szolgáltatás figyelője proaktív módon figyeli a rendelkezésre állás és a Windows vagy Linux-alapú vendég operációs rendszer, amelyek a legfontosabb összetevők és a kapcsolatok, feltételek, amely meghatározza, hogy miként állapotát, a modell teljesítményét összetevők, és riasztást küld, ha a nem kifogástalan állapot észlelésekor.  

Megtekintése az Azure virtuális gép összesített állapotát és operációs rendszer alapjául szolgáló virtuális gépek Health, közvetlenül a virtuális gépről vagy az Azure Monitor egy erőforráscsoportba tartozó minden virtuális gépen az Azure Monitor szolgáltatással két perspektíva adatai figyelhető meg.

Ez a cikk segítséget nyújt a megtudhatja, hogyan mérheti fel gyorsan, kivizsgálásán és elhárításán észlelt állapotbeli problémák.

A virtuális gépek az Azure Monitor konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Monitor-beli virtuális gépek](vminsights-onboard.md).

>[!NOTE]
>Kezdési 2019. február 15., megkezdjük, az aktuális állapotközpontú modellről az Azure monitorban a virtuális gépek állapotfigyelő szolgáltatást, amely akkor látható, ha használja-e az állapot diagnosztikai élmény még ma, egy új verziójára a állapotmodell-ről. A frissítés javítja a állapotának összesítése feldolgozása, és a egy diagnosztikai állapot nézetében megjelenő finomított állapotmodell tartalmazza. 
>
>Az új modell állapotának összesítését, szülőentitás/szintű állapotára vonatkozó feltételek, alárendelt állapotára vonatkozó feltételek lesz gyorsabban, és ennek eredményeképpen a kívánt vagy célzott állapot kisebb késést biztosítanak a szülő-frissítések állapotát. A állapotára vonatkozó feltételek alapján is szűrheti a **teljesítmény** és **rendelkezésre állási** ellentétben az előző lapon-alapú metódus jelölje ki a nézetben vagy kategória kategóriák.
>
>Az új állapotfigyelő diagnosztikai élményt további részletekért tekintse meg a Health diagnosztikai [szakasz](#health-diagnostics) ebben a cikkben. 
>
>Ez a frissítés javulni fog a következőket: 
>
>- Állapotának összesítése az alacsonyabb késésű feldolgozása  
>- Állapotfigyelők állapotváltozásai gyorsabb riasztásai 
>- Az összes virtuális gép összesített virtuális gép nézetben állapot gyorsabban frissítése 
>
>Nincs olyan funkciókat, ma kiadásaiban az állapotfigyelő szolgáltatás Azure monitor-beli virtuális gépek nem regressziós.

>Ebből a változásból lesz egy rövid idő alatt a szolgáltatás és az ügyfélállapot-előzmények az egyes megszakítása. A két megközelítés az egészségügyi diagnostics érintett - állapot módosítási előzményeinek alaphelyzetbe állnak, és korábbi állapotára vonatkozó feltételek állapotváltozásait nem lesz elérhető, tekintse át a Állapotváltozási oszlopban található a Health diagnosztika lapot. Ha érdekli a korábbi adatok bármelyik üzleti szempontból alapvető kritikus fontosságú virtuális Gépet, akkor is igénybe vehet a feltételek-állapotadatok és a megfelelő állapotváltozások képernyőkép referenciaként. 

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

![A virtuális gépek állapotának áttekintése a kiválasztott Azure virtuális gép az Azure Monitor](./media/vminsights-health/vminsights-directvm-health.png)

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

![VM-elemzés, monitorozás az Azure Monitor megtekintése](./media/vminsights-health/vminsights-aggregate-health.png)

Az a **előfizetés** és **erőforráscsoport** legördülő listák, válassza ki a megfelelő erőforráscsoportot a virtuális gépeket a csoporthoz kapcsolódó, annak érdekében, hogy a jelentett állapot megtekintéséhez.  A kijelölés csak az állapotfigyelő szolgáltatás vonatkozik, és nem vállalunk pénzügyi teljesítmény vagy a térképen.

Az a **egészségügyi** lapon is tudja ismerje meg a következőket:

* Hány virtuális gépet a kritikus vagy nem megfelelő állapotú, és hány olyan (néven állapota ismeretlen) megfelelő vagy nem küldő adatok vannak?
* Operációs rendszer (OS) virtuális gépek mely jelent a nem kifogástalan állapot, és hány?
* Hány virtuális gépet a processzor, a lemez, a memória vagy a hálózati adaptert az állapot alapján kategorizáljuk, az észlelt probléma miatt sérült állapotban?  
* Hány virtuális gépet egy core operációs rendszer szolgáltatás állapota alapján kategorizáljuk által észlelt probléma miatt sérült állapotban?

Itt gyorsan azonosíthatja a proaktív figyelése a virtuális gép állapotára vonatkozó feltételek által észlelt leggyakoribb kritikus hibák, és tekintse át a virtuális gép állapota riasztás részleteinek és a kapcsolódó ismeretcikk célja, hogy segítsenek a diagnosztikai és a szervizelés a probléma.  Válassza ki valamelyik megnyitásához a súlyossági szinten pedig a [minden riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) oldalon a súlyosság szerint szűrve.

A **operációs rendszer által a virtuális gép terjesztési** lista mutatja azokat a Windows-kiadás vagy a Linux-disztribúció, és azok verziószáma szereplő virtuális gépek. Az egyes operációs rendszert, a virtuális gépek bontásban további a virtuális gép alapján. 

![Virtuális gép Insights virtuális gép terjesztési perspektíva](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

A megadott virtuális gép állapota állapotok a következők: 

* **Kifogástalan állapotú** – nem problémát észlelt a virtuális gép, és szükség szerint működik-e.  
* **Kritikus fontosságú** – egy vagy több kritikus problémák észlelhetők, amely kell oldani annak érdekében, hogy a normál működés visszaállítása a várt módon. 
* **Figyelmeztetés** – egy vagy több problémák észlelhetők, amelyeket meg kell oldani, vagy az egészségügyi feltétel kritikus válhat.  
* **Ismeretlen** – Ha a szolgáltatás nem tudott egy kapcsolatot a virtuális géppel, az állapot módosul, állapota ismeretlen.  

Kattintson bármely oszlop elem – a **virtuális gépek száma**, **kritikus**, **figyelmeztetés**, **kifogástalan** vagy **ismeretlen** , részletesebb a **virtuális gépek** lapon kiválasztott oszlop megfelelő szűrt eredmények listájának megtekintéséhez. Például, ha azt át szeretné tekinteni a futó összes virtuális gép **Red Hat Enterprise Linux kiadásban 7.5**, kattintson a a **virtuális gépek száma** értéke, hogy az operációs rendszer, és megnyílik a következő oldalon a megfelelő virtuális gépek listája a szűrő és azok ismert állapotát.  

![Példa összesítésre, Red Hat Linux rendszerű virtuális gépek](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Az a **virtuális gépek** lapon, ha a virtuális gépek az az oszlop nevére **virtuális gép neve**, átirányítjuk a Virtuálisgép-példány lapjához a riasztás további részleteit tartalmazó és a feltételek állapotproblémák azonosított, amelyek vannak Ez hatással lenne a kiválasztott virtuális gép.  Itt szűrhet az egészségügyi állapotának részletei kattintva **állapot** ikonra a bal felső sarkában az oldalon tekintheti meg, mely összetevők sérült állapotban, vagy a virtuális gép állapota riasztásait váltotta ki egy nem megfelelő állapotú összetevőre tekintheti meg riasztás súlyossága szerint osztályozva.    

A virtuális gépek listája nézet a virtuális gép nevére kattintva megnyílik a **egészségügyi** oldal, amely a kiválasztott virtuális gép, hasonlóképpen, ha a kiválasztott **Insights (előzetes verzió)** közvetlenül a virtuális gépről.

![A kiválasztott Azure virtuális gép virtuális gép insights](./media/vminsights-health/vminsights-directvm-health.png)

Itt jelenik meg egy összegző **állapot** a virtuális gép és **riasztások**, kategorizált által súlyosságának, amelyek tartalmazzák a virtuális gép állapota riasztás jelenik meg, ha az állapot a állapota nem kifogástalan változik egy állapotára vonatkozó feltételek.  Kiválasztásával **kritikus állapotban lévő virtuális gépek** megnyit egy oldalt egy vagy több kritikus állapotban lévő virtuális gépek listáját.  Az állapot ellenőrzése a jelennek meg a virtuális gépek, a lista egyik kattint a **egészségügyi diagnosztikai** nézet a virtuális gép.  Itt találja meg, melyik állapotára vonatkozó feltételek állapot állapotprobléma lépett van tükrözve. Ha a **egészségügyi diagnosztikai** oldal megjelenik, azt mutatja, hogy a virtuális gép és a társított állapotára vonatkozó feltételek aktuális állapotát az összes összetevője.  Tekintse meg a [egészségügyi diagnosztikai](#health-diagnostics) további részleteket a következő szakaszban.  

Kiválasztásával **megtekintheti az összes állapotára vonatkozó feltételek** minden elérhető ez a szolgáltatás állapotának feltétel listáját tartalmazó oldal megnyitása.  Az adatokat tovább szűrhetők alapján a következő beállításokat:

* **Típus** – feltételek típusa feltételek felmérése, és a figyelt virtuális gép általános állapotának összesítő állapot három típusú van.  
    a. **Egység** – a virtuális gép bizonyos elemeit méri. Ez a feltételtípus egészségügyi előfordulhat, hogy lehet egyik teljesítményszámláló ellenőrzése az összetevő teljesítményének meghatározására szintetikus tranzakciók végrehajtására, vagy tekintse meg a hibát jelző esemény parancsprogram futtatása.  Alapértelmezés szerint a szűrő értéke egység.  
    b. **Függőségi** – különböző entitások állapotait. Ez állapotára vonatkozó feltételek lehetővé teszi, hogy a függő entitás, amely a sikeres működés támaszkodik más típusú állapotát egy entitás állapotát.  
    c. **Összesített** -biztosít hasonló állapotára vonatkozó feltételek kombinált állapotát. Egység- és függőségfigyelők egészségügyi feltétel mellett egy összesített állapotát feltétel általában lesz konfigurálva. Jobb általános szervezésén egy entitás megcélzó számos különböző egészségügyi kritériumok, valamint összesített állapotát feltételt biztosít egyedi állapotokat különböző entitáskategóriákról.

* **Kategória** -állapotára vonatkozó feltételek hasonló típusú csoport feltételek számára a jelentéskészítéshez használt típusát.  Vagy **rendelkezésre állási** vagy **teljesítmény**.

Áthatoló lejjebb melyik példányai nem megfelelő állapotú elemre kattintva megtekintheti a **nem megfelelő állapotú összetevőre** oszlop.  A lapon egy táblázat az összetevők, amelyek kritikus állapotban van.    

## <a name="health-diagnostics"></a>Diagnosztikai állapot
Thge **egészségügyi diagnosztikai** lap lehetővé teszi, hogy ábrázolhatja a virtuális gépek, listázás, a virtuális gép összes összetevőjét az Állapotközpontú modellről kapcsolódó állapotára vonatkozó feltételek, állapotváltozások, és más jelentős problémák által azonosított figyelt kapcsolódó összetevők a virtuális géphez.

![Példa egy virtuális gép állapotának diagnosztikája lap](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnosztikai állapot a következő módon indíthatja el.

* Összesítő állapotok szerint az összes virtuális gép az Azure monitorban az összesített virtuális gép szempontjából.  Az a **egészségügyi** lap, kattintson az ikonra az **kritikus**, **figyelmeztetés**, **kifogástalan**, vagy **ismeretlen** a szakasz alatt állapota **Vendég virtuális gép állapota** és indítson részletes elemzést az oldal, amely felsorolja az összes virtuális gép megfelelő szűrt kategóriát.  Kattintson az értéket a a **állapot** oszlop hatókörét arra, hogy adott virtuális gép állapota diagnosztikai nyílik meg.      

* Operációs rendszer az Azure monitorban az összesített virtuális gép szempontjából. Alatt **VM terjesztési**, az oszlop értékei közül bármelyik kiválasztásával megnyílik az **virtuális gépek** lapon és a tábla a szűrt kategória megfelelő listáját adja vissza.  Kattintson a érték a **állapot** oszlop egészségügyi diagnosztikai megnyílik a kiválasztott virtuális géphez.    
 
* A Vendég virtuális Gépen a virtuális gépek az Azure Monitor **egészségügyi** fülre kattintva **egészségügyi diagnosztika megtekintése** 

Diagnosztikai állapot az állapotinformációkat a következő kategóriákba sorolhatók: 

* Rendelkezésre állás
* Teljesítmény
 
Például a logikai lemez, a Processzor és a egy adott összetevőre definiált állapotára vonatkozó feltételek stb. Kategóriát adta meg a figyelő a mellette látható továbbá a **állapotára vonatkozó feltételek** oszlop.  

Egy állapotára vonatkozó feltételek állapotát határozza meg a négy állapota – *kritikus*, *figyelmeztetés*, *kifogástalan*, és *ismeretlen*. Az első három is konfigurálható, ami azt jelenti, módosíthatja a küszöbértékeket a figyelő használatával a [számítási feladatok a figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Ismeretlen* még nem konfigurálható, és a fenntartott, bizonyos forgatókönyvek esetén az alábbi táblázatban leírtak szerint.  

Az alábbi táblázat részletesen az állapotokat az egészségügyi Diagnostics jelöli.

|Ikon |Állapot |Jelentés |
|-----|-------------|------------|
| |Kifogástalan |Egészségügyi állapota megfelelő, ha meghatározott egészségügyi feltételek, a virtuális gép azt észlelte, hogy szükség szerint működik-e problémákat nem jelző belül. Egy szülő összesítő figyelő esetén a health tekercsben felfelé és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Kritikus |Állapot, kritikus fontosságú, ha nem található a meghatározott egészségügyi feltételt, amely azt jelzi, hogy legalább egy kritikus fontosságú problémák, amelyek kell oldani annak érdekében, hogy a normál működés visszaállítása. Egy szülő összesítő figyelő esetén a health tekercsben felfelé és a legkedvezőbb vagy legrosszabb állapota a gyermek tükrözi.|
| |Figyelmeztetés |Állapota figyelmet igényel, hogy meghatározott egészségügyi feltétel, ahol egy azt jelzi, hogy két küszöbérték közötti egy *figyelmeztetés* állapot, a másik pedig azt jelzi, egy *kritikus* (három ellenőrzött felhasználói állapot a következő állapota lehetséges), vagy ha egy nem kritikus problémát észlel, amelyek kritikus fontosságú problémákat okozhat, ha nem szűnik meg. Esetén a fölérendelt összegző figyelő esetén, ha egy vagy több gyermek figyelmeztetési állapotba kerül, akkor jelenik meg a szülő *figyelmeztetés* állapota. Ha van, amely a gyermek egy *kritikus* és a egy másik alárendelt egy *figyelmeztetés* állapot, a fölérendelt összegző megjelenik egy állapotát *kritikus*.|
| |Ismeretlen |Állapot szerepel egy *ismeretlen* állapot, amikor az objektum állapota nem számítható ki több okból, például nem tud adatokat gyűjteni, a szolgáltatás nem inicializált stb. Ez nem egy felhasználó által szabályozott állapotban.| 

Diagnosztikai állapotlap három fő részből áll:

* Összetevőmodell 
* Állapotra vonatkozó feltételek
* Állapotváltozások 

![Szakaszait állapotának diagnosztikája lap](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Összetevő-modell
Az egészségügyi diagnosztika oldal bal szélső oszlopában az összetevő modellje. Összes összetevő, amely a virtuális gép kapcsolódik, és azok aktuális állapotának ebben az oszlopban jelennek meg. 

A következő példában a felderített összetevők olyan lemez, logikai lemez, processzor, memória és operációs rendszer. Ezek az összetevők több példánya felderítésének és ebben az oszlopban jelenik meg. Ha például az alábbi képen láthatók a virtuális gép logikai lemez – C: és D:, amely kifogástalan állapotban vannak, két példánnyal rendelkezik.  

![Az egészségügyi diagnosztikai megjelenített példa összetevőmodellből](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Állapotára vonatkozó feltételek
A Health diagnosztika lapot center oszlopa a **állapotára vonatkozó feltételek** oszlop. Az a virtuális gép meghatározott állapotmodell egy hierarchikus fa jelenik meg. A virtuális gép állapotmodell egység és összesített állapotára vonatkozó feltételek áll.  

![Az egészségügyi diagnosztikai megjelenített példa állapotára vonatkozó feltételek](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Egészségügyi kritériumnak bizonyos feltételek, amely lehet egy küszöbértéket állapota egy entitás, és így tovább a figyelt példány állapotát méri. Egészségügyi kritériumnak konfigurálható egészségügyi állapota két vagy három küszöbértékek rendelkezik, a fentebb leírt módon. Csak egy lehetséges állapottal lekérdezhet, az egészségügyi feltétel is lehet. 

A cél általános állapotát annak állapotára vonatkozó feltételek az állapotközpontú modellről meghatározott állapotát határozza meg. Ez állapotára vonatkozó feltételek a cél közvetlenül megcélzó megcélzó a cél egy összesített állapotát feltétel keresztül működés közbeni összetevők állapotára vonatkozó feltételek kombinációja lesz. Ezt a hierarchiát mutatja be a **állapotára vonatkozó feltételek** állapotának diagnosztikája lap részében. A állapotán alapuló szabály konfigurációját az összesített állapotára vonatkozó feltételek része (alapértelmezett értéke *legrosszabb,*). Alapértelmezés szerint ez a funkció a szakaszában részeként futó állapotára vonatkozó feltételek listáját találja [figyelési konfiguráció részletei](#monitoring-configuration-details).  

**Egység** egészségügyi feltételtípust rendelkezhet az sokkal jobb és a kiválasztja a három pontra hivatkozásra kattintva módosítani a konfigurációt **részletek megjelenítése** a konfigurációs panel megnyitásához. 

![A health feltételek példa konfigurálása](./media/vminsights-health/health-diagnostics-vm-example-02.png)

A konfiguráció panelen a kiválasztott egészségügyi feltételeinek, példa használatával **átlagos másodpercek száma az írási**, a küszöbérték egy másik numerikus érték konfigurálható. Kétállapotú figyelő, ami azt jelenti, csak történt változások kifogástalanról figyelmeztetési. Előfordulhat, hogy más állapotfigyelő feltétel három állapot, ahol konfigurálhatja a figyelmeztetési és a kritikus állapot állapotküszöb értéke.  

>[!NOTE]
>Egészségügyi feltételek konfigurációs módosítások alkalmazása az egyik példányhoz alkalmazza a rendszer az összes figyelt példányokat.  Például, ha kiválasztja **fizikailemez -1 D:** és módosíthatja a **átlagos másodpercek száma az írási** küszöbértéket, csak annak a példánynak nem vonatkozik, de az összes többi lemez példány felderített, és a figyelt a virtuális Gépet.
>

![. Egy egység figyelő például egy állapotára vonatkozó feltételek konfigurálása](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Ha azt szeretné, további információ az állapotjelző ismeretcikkeket is segít azonosítani a problémákat, okok és megoldások. Kattintson a **információk megtekintéséhez** a lapon és a egy új lapon nyílik meg a böngészőben, az adott ismeretcikk megjelenítése. Bármikor, áttekintheti az összes virtuális gépek Állapotfigyelő szolgáltatás részét képező Azure Monitor állapota feltétel ismeretcikkek [Itt](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Állapotváltozások
A jobb szélső oszlop az egészségügyi Diagnostics lapon **állapotváltozások**. A kijelölt állapotára vonatkozó feltételek társított összes állapotváltozások listáját a **állapotára vonatkozó feltételek** szakasz vagy a virtuális gép, ha a kiválasztott virtuális gép az állapotváltozás körülményeiről a **Összetevőmodellből** vagy **Állapotára vonatkozó feltételek** táblázat. 

![Az egészségügyi diagnosztikai megjelenített példa állapotváltozások](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Ez a szakasz feltételek állapota és a kapcsolódó idő felül a legfrissebb állapot szerint rendezett áll.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Összetevő-modell, és állapotára vonatkozó feltételek és állapot-oszlopok módosítása 
A össze az a három oszlop van kapcsolva egymással egymással. Amikor kiválaszt egy felderített példány a **Összetevőmodellből** szakaszban a **állapotára vonatkozó feltételek** összetevő a nézet a szűrt szakaszban, és ennek megfelelően a **állapotváltozás**szakasz frissült a kiválasztott állapotára vonatkozó feltételek alapján. 

![Felügyelt példány és az eredmények kiválasztása – példa](./media/vminsights-health/health-diagnostics-vm-example-01.png)

A fenti példában, kiválasztásakor **fizikailemez - 1 D:**, a állapotára vonatkozó feltételek fa a szűrt **fizikailemez - 1 D:**. A **Állapotváltozási** az oszlopban látható az állapotváltozás körülményeiről rendelkezésre állásának alapján **fizikailemez - 1 D:**. 

Egy frissített állapot megtekintéséhez kattintson is frissítheti a Health diagnosztika lapot a **frissítése** hivatkozásra.  Alapján előre definiált lekérdezési időközét az egészségügyi feltétel állapot frissítése esetén ez a feladat lehetővé teszi a várakozás elkerülése érdekében, és tükrözi a legfrissebb állapot.  A **feltételek állapota** szűrő engedélyezi-e a kiválasztott állapota – a eredményeket hatókörét *kifogástalan*, *figyelmeztetés*, *kritikus*, *Ismeretlen*, és *összes*.  A **utolsó frissített** jobb felső sarokban az idő az utolsó időpont, amikor a rendszerállapot-diagnosztika lapot frissítésének jelöli.  

## <a name="alerts"></a>Riasztások
Virtuális gépek Állapotfigyelő szolgáltatás az Azure Monitor integrálható [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) és riasztást jelenít meg, ha az előre meghatározott állapotára vonatkozó feltételek módosítása a kifogástalan állapotú sérült állapotba kerül a feltétel észlelése esetén. Riasztások súlyosság – Sev 0-tól 4, a Sev 0 jelölő a legmagasabb súlyossági szint szerint kategorizálhatók.  

Virtuális gép állapota riasztások súlyosság szerint osztályozva száma érhető el a **egészségügyi** irányítópult a szakaszában **riasztások**. A riasztások teljes számát, vagy egy súlyossági szintet megfelelő számot kiválasztásakor a **riasztások** lap nyílik meg, és felsorolja a egyeztetésére vonatkozó beállítás az összes riasztás.  Például, ha a sor megfelelő a kiválasztott **Sev 1. szintű**, majd az alábbi nézet megjelenítéséhez:

![Az összes súlyossági szint 1 riasztás – példa](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Az a **riasztások** lap, akkor csak hatóköre nem a kijelölt megfelelő riasztások megjelenítése, de az is szűrve **erőforrástípus** csak az állapotriasztások váltotta ki a virtuális gép típusú erőforrást jeleníti meg.  Ez megjelenik a riasztások listája, az oszlop alatt **célerőforrás**, ahol az Azure virtuális Gépen, a riasztás kiadását okozó számára az adott feltételeknek nem megfelelő állapotú feltétel teljesülésekor jeleníti meg.  

Más típusú erőforrások vagy szolgáltatások riasztásai nem célja, hogy ez a nézet szerepelnek, például a riasztások a lekérdezések alapján a Log Analytics vagy a mérőszám, amely riasztást küld, általában az alapértelmezett Azure Monitor lenne megtekintése [minden riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) lapot. 

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

A **riasztás részletes** ki kell választania egy riasztást a riasztás részleteinek megadása és az állapot módosítását lehetővé tevő lap jelenik meg. Riasztások kezelésével kapcsolatos további tudnivalókért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>Ezzel időt állapotára vonatkozó feltételek alapján új riasztásokat hoznak létre, vagy módosítsa a meglévő health portálról az Azure Monitor riasztási szabályok nem támogatott.  
>

![Riasztás részleteinek ablaktáblája tartalmazza a kijelölt riasztás](./media/vminsights-health/alert-details-pane-01.png)

Riasztás állapota is módosítható egy vagy több riasztást a jelölje ki őket, és kiválasztja az **Állapotváltozáshoz** a a **minden riasztás** oldalán a bal felső sarkában található. Az a **riasztás állapotának módosítása** ablaktáblán válassza ki az állapotok valamelyikével adjon meg egy leírást a változás a a **Megjegyzés** mezőben, majd kattintson a **Ok** a módosítások véglegesítéséhez. Bár az adatokat a rendszer ellenőrzi, és a módosítások érvénybe lépnek, nyomon követheti a folyamat állapotát **értesítések** a menüből.  

## <a name="next-steps"></a>További lépések
Azonosíthatja a szűk keresztmetszeteket és a virtuális gépek teljesítményét a teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 
