---
title: Az Azure-beli virtuális gépek állapotának megismerése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan értelmezhető a virtuális gépek és a mögöttes operációs rendszerek állapota a Azure Monitor for VMs használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3cecb04a4f299051860c45425f0fc4e13c3722ea
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656296"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Az Azure-beli virtuális gépek állapotának megismerése

Az Azure a figyelési területen meghatározott szerepkörökhöz vagy feladatokhoz nyújt szolgáltatásokat, de nem tartalmazza az Azure Virtual Machines (VM) szolgáltatásban üzemeltetett operációs rendszerek (OSs) részletes állapotának perspektíváit. Habár a Azure Monitor különböző feltételekhez is használható, nem az alapvető összetevők, illetve a virtuális gépek általános állapotának modellezésére és megjelenítésére szolgál.

A Azure Monitor for VMs Health használatával aktívan nyomon követheti a Windows-vagy Linux-alapú vendég operációs rendszer rendelkezésre állását és teljesítményét. Az állapotfigyelő szolgáltatás a fő összetevőket és azok kapcsolatait jelképező modellt használ, amely meghatározza, hogyan kell mérni az összetevők állapotát, és riasztást küld, ha sérült állapotot észlel.

Egy Azure-beli virtuális gép és a mögöttes operációs rendszer általános állapotának megtekintése két perspektívából lehetséges: közvetlenül egy virtuális gépről vagy egy erőforráscsoport összes virtuális gépén Azure Monitorból.

Ez a cikk bemutatja, hogyan lehet gyorsan felmérni, megvizsgálni és elhárítani az állapottal kapcsolatos problémákat, amikor a Azure Monitor for VMs Health funkció észleli őket.

További információ a Azure Monitor for VMs konfigurálásáról: [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md).

>[!NOTE]
>A közelmúltban bejelentettük, hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján az állapotfigyelő funkció [bekövetkezett](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md). 

## <a name="monitoring-configuration-details"></a>Figyelési konfiguráció részletei

Ez a szakasz az Azure-beli Windows-és Linux-alapú virtuális gépek figyelésére vonatkozó alapértelmezett állapot-feltételeket ismerteti. Az összes állapotra vonatkozó feltétel előre konfigurálva van, hogy riasztást küldjön, ha nem kifogástalan állapotot azonosítanak.

| Figyelő neve | Gyakoriság (perc) | Lookback időtartama (perc) | Művelet | Küszöbérték | Riasztás állapot szerint | Severity | Munkaterhelés kategóriája | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Online logikai lemez | 5 | 15 | <> | 1 (igaz) | Kritikus | Sev1 | Linux | 
| Logikai lemez – Szabad terület | 5 | 15 | < | 200 MB (figyelmeztetés)<br> 100 MB (kritikus) | Figyelmeztetés | Sev1<br> Sev2 | Linux | 
| Logikai lemez – Szabad inode-ok (%) | 5 | 15 | < | 5% | Kritikus | Sev1 | Linux | 
| Logikai lemez – Szabad terület (%) | 5 | 15 | < | 5% | Kritikus | Sev1 | Linux | 
| Hálózati adapter állapota | 5 | 15 | <> | 1 (igaz) | Figyelmeztetés | Sev2 | Linux | 
| Operációs rendszer rendelkezésre álló memóriája (MB) | 5 | 10 | < | 2,5 MB | Kritikus | Sev1 | Linux | 
| Lemez átlagos olvasási ideje (mp/olvasás) | 5 | 25 | > | 0,05 s | Kritikus | Sev1 | Linux | 
| Lemez átlagos műveleti idő (mp/átvitel) | 5 | 25 | > | 0,05 s | Kritikus | Sev1 | Linux | 
| Lemez átlagos írási ideje (mp/írás) | 5 | 25 | > | 0,05 s | Kritikus | Sev1 | Linux | 
| Lemez állapota | 5 | 25 | <> | 1 (igaz) | Kritikus | Sev1 | Linux | 
| Operációs rendszer teljes processzorideje (%) | 5 | 10 | >= | 95% | Kritikus | Sev1 | Linux | 
| Teljes CPU-kihasználtság százalékos aránya | 5 | 10 | >= | 95% | Kritikus | Sev1 | Windows | 
| Fájlrendszer hibája vagy sérülése | 60 | 60 | <> | 4 | Kritikus | Sev1 | Windows | 
| Átlagos logikai lemez másodpercenkénti száma olvasáskor | 1 | 15 | > | 0.04 s | Figyelmeztetés | Sev2 | Windows | 
| Átlagos logikai lemez másodpercenkénti száma | 1 | 15 | > | 0.04 s | Figyelmeztetés | Sev2 | Windows | 
| Logikai lemez átlagos másodpercenkénti száma (logikai lemez) | 1 | 15 | > | 0.04 s | Figyelmeztetés | Sev2 | Windows | 
| Lemez aktuális várólistájának hossza (logikai lemez) | 5 | 60 | >= | 32 | Figyelmeztetés | Sev2 | Windows | 
| Logikai lemez szabad területe (MB) | 15 | 60 | > | 500 MB figyelmeztetés<br> 300 MB kritikus | Kritikus | Sev1<br> Sev2 | Windows | 
| Logikai lemez szabad területe (%) | 15 | 60 | > | 10%-os figyelmeztetés<br> 5% kritikus | Kritikus | Sev1<br> Sev2 | Windows |
| Logikai lemez üresjárati ideje (%) | 15 | 360 | <= | 20 | Figyelmeztetés | Sev2 | Windows | 
| Használatban lévő sávszélesség százalékos aránya | 5 | 60 | >= | 60% | Figyelmeztetés | Sev2 | Windows | 
| Felhasznált sávszélesség százalékos aránya összesen | 5 | 60 | >= | 75% | Figyelmeztetés | Sev2 | Windows | 
| Felhasznált sávszélesség százalékos aránya (írás) | 5 | 60 | >= | 60% | Figyelmeztetés | Sev2 | Windows | 
| DHCP-ügyfél Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| DNS-ügyfél Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| Windows-Eseménynapló Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| Windows tűzfal Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| RPC Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| Kiszolgáló Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| Rendszerfelügyeleti webszolgáltatások Service Health | 5 | 12 | <> | 4 (futó) | Kritikus | Sev1 | Windows | 
| Rendelkezésre álló memória MB-ban | 5 | 10 | < | 100 MB | Kritikus | Sev1 | Windows | 
| Ingyenes Rendszerlapok táblázatos bejegyzései | 5 | 10 | <= | 5000 | Kritikus | Sev1 | Windows | 
| Memória lapja másodpercenként | 5 | 10 | >= | 5000/s | Figyelmeztetés | Sev1 | Windows | 
| A felhasznált előjegyzett memória százalékos aránya | 5 | 10 | > | 80% | Kritikus | Sev1 | Windows | 
| Átlagosan használt lemez másodpercenkénti száma | 1 | 15 | > | 0.04 s | Figyelmeztetés | Sev2 | Windows | 
| Írási idő átlagos másodpercenkénti száma | 1 | 15 | > | 0.04 s | Figyelmeztetés | Sev2 | Windows | 
| Lemez aktuális várólistájának hossza | 5 | 60 | >= | 32 | Figyelmeztetés | Sev2 | Windows | 
| Lemez üresjárati ideje (%) | 5 | 60 | >= | 20 | Figyelmeztetés | Sev2 | Windows | 

>[!NOTE]
>A Lookback időtartama azt jelzi, hogy a visszatekintő ablak milyen gyakran ellenőrzi a metrika értékeit, például az elmúlt öt percben.  

>[!NOTE]
>A gyakoriság azt jelzi, hogy a metrikai riasztás milyen gyakran ellenőrzi, hogy a feltételek teljesülnek-e, például percenként.  Ez az az időtartam, ameddig a rendszer végrehajtja az állapot feltételeit, és a lookback az az időtartam, ameddig a rendszer kiértékeli az állapotot. Például az állapotra vonatkozó feltétel akkor kerül kiértékelésre, ha a **processzor kihasználtsága** 95%-nál nagyobb 5 perces gyakorisággal, és 15 percnél nagyobb marad a 95%-nál (3 egymást követő kiértékelési ciklus), majd az állapotot a kritikus súlyosságra frissíti, ha még nem volt ilyen.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A bejelentkezéshez nyissa meg a [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>A Azure Monitor for VMs Health bemutatása

Mielőtt a Health szolgáltatást egyetlen virtuális gépre vagy virtuális gépek csoportjára használja, fontos tisztában lennie azzal, hogy az információk hogyan jelennek meg, és hogy a vizualizációk milyen formában jelennek meg.

### <a name="view-health-directly-from-a-vm"></a>Állapot megtekintése közvetlenül egy virtuális gépről

Egy Azure-beli virtuális gép állapotának megtekintéséhez válassza az eredmények **(előzetes verzió)** lehetőséget a virtuális gép bal oldali ablaktábláján. A virtuális gép elemzése lapon a **Health (állapot** ) lap alapértelmezés szerint meg van nyitva, és a virtuális gép állapot nézetét jeleníti meg.

![A kiválasztott Azure-beli virtuális gépek Azure Monitor for VMs állapotának áttekintése](./media/vminsights-health/vminsights-directvm-health-01.png)

A **vendég virtuális gép állapota** szakaszban a tábla megjeleníti a virtuális gép állapotára vonatkozó feltételek által figyelt teljesítmény-összetevők állapotát, valamint a nem megfelelő állapotú összetevők által kiváltott virtuálisgép-állapot-riasztások teljes számát. Ezek az összetevők a következők: **CPU**, **memória**, **lemez**és **hálózat**. A vendég virtuális gép állapota melletti Chevron kibontásával tekintheti meg az összetevők állapotát.

![Egy kiválasztott Azure-beli virtuális gép összetevő-állapotának Azure Monitor for VMs](./media/vminsights-health/vminsights-directvm-health-02.png)

Az összetevő melletti állapot kiválasztásakor a kiválasztott összetevő környezetében megnyílik az állapot-diagnosztika felülete. Megjeleníti az adott összetevő állapotának összeállítását, amely leírja, hogy milyen állapotot kell kiszámítani az állapotának meghatározásához. További információ: [Health Diagnostics és Health feltételek használata](#health-diagnostics). A riasztásokkal kapcsolatos további információkért lásd: [riasztások](#alerts).

A virtuális géphez definiált állapotokat az alábbi táblázat ismerteti:

|Ikon |Állapot |Jelentés |
|-----|-------------|---------------|
| |Kifogástalan |A virtuális gép a meghatározott állapoton belül van. Ez az állapot azt jelzi, hogy nincs észlelt probléma, és a virtuális gép rendesen működik. A szülő összesítő figyelővel az állapot összesíthető, és a gyermek legkedvezőbb vagy legrosszabb állapotát tükrözi.|
| |Kritikus |Az állapot nincs a definiált állapoton belül, ami azt jelzi, hogy a rendszer egy vagy több kritikus problémát észlelt. Ezeket a problémákat a normál működés visszaállításával kell megoldani. A szülő összesítő figyelővel az állapot összesítve jelenik meg, és tükrözi a gyermek legkedvezőbb vagy legrosszabb állapotú állapotát.|
| |Figyelmeztetés |Az állapot a definiált állapot két küszöbértéke között van, ahol az egyik figyelmeztetési állapotot jelez, a másik pedig kritikus állapotot jelez (három állapot küszöbértéke konfigurálható), vagy ha egy nem kritikus probléma kritikus problémát okozhat, ha megoldatlan. Ha szülő-összesítő figyelővel rendelkezik, ha egy vagy több gyermek figyelmeztetési állapotban van, akkor a szülő figyelmeztetési állapotot jelez. Ha egy gyermek kritikus állapotban van, egy másik pedig figyelmeztetési állapotban van, akkor a szülő összesítés az állapotot kritikusként jeleníti meg.|
| |Ismeretlen |Az állapot több okból nem számítható ki. A következő szakasz további részleteket és lehetséges megoldásokat tartalmaz. |

Ismeretlen állapotot okozhat a következő problémák:

- Az ügynököt újrakonfigurálták, és a Azure Monitor for VMs engedélyezésekor megadott munkaterületre már nem jelent jelentést. Ha úgy szeretné beállítani az ügynököt, hogy a munkaterületre jelentsen, tekintse át a [munkaterület hozzáadását vagy eltávolítását](../platform/agent-manage.md#adding-or-removing-a-workspace).
- A virtuális gép törölve lett.
- A Azure Monitor for VMshoz társított munkaterület törölve lett. A munkaterületet a Premier szintű támogatás előnyeivel állíthatja helyre. Lépjen a [premierre](https://premier.microsoft.com/) , és nyisson meg egy támogatási kérést.
- A megoldás függőségei törölve lettek. Ha újra engedélyezni szeretné a ServiceMap és a InfrastructureInsights-megoldásokat a Log Analytics munkaterületen, telepítse újra a ServiceMap-megoldást a [Azure Resource Manager sablon](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)használatával. A InfastructureInsights-megoldás újratelepítéséhez e-mail-vminsights@microsoft.com. 
- A virtuális gép le lett állítva.
- Az Azure-beli virtuális gép szolgáltatás nem érhető el, vagy folyamatban van a karbantartás.
- A munkaterület [napi adatok vagy a megőrzési korlát](../platform/manage-cost-storage.md) teljesült.

Válassza az **állapot-diagnosztika megtekintése** lehetőséget egy olyan oldal megnyitásához, amely megjeleníti a virtuális gép összes összetevőjét, a társított állapotra vonatkozó feltételeket, az állapot módosításait, valamint a virtuális géphez kapcsolódó összetevők figyelésével kapcsolatos egyéb problémákat.

További információ: [Health Diagnostics](#health-diagnostics).

A **Health (állapot** ) szakaszban egy táblázat mutatja be az állapotra vonatkozó feltételek által figyelt teljesítmény-összetevők összesítését. Ezek az összetevők a következők: **CPU**, **memória**, **lemez**és **hálózat**. Egy összetevő kiválasztásával megnyílik egy oldal, amely felsorolja az adott összetevő összes figyelési feltételét és állapotát.

Amikor egy Windows rendszerű Azure-beli virtuális gépről fér hozzá az állapothoz, az első öt legfontosabb Windows-szolgáltatás állapota az **alapszolgáltatások állapota**területen látható. Bármelyik szolgáltatás kiválasztásával megnyílik egy oldal, amely felsorolja az adott összetevőhöz tartozó állapot-figyelési állapotot az állapotával együtt.

Az állapotra vonatkozó feltételek nevének kiválasztásával megnyílik a Tulajdonságok ablaktábla. Ezen az ablaktáblán áttekintheti a konfigurációs adatokat, beleértve, hogy az állapotra vonatkozó feltételek megfelelő Azure Monitor riasztással rendelkeznek-e.

További információ: [Health Diagnostics és Health feltételek használata](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Aggregált VM-perspektíva

Ha meg szeretné tekinteni az erőforráscsoport összes virtuális gépe állapotát, válassza ki a **Azure monitor** elemet a portál navigációs listájában, majd válassza a **Virtual Machines (előzetes verzió)** lehetőséget.

![A VM-alapú adatvizsgálatok figyelési nézete Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Az **előfizetés** és az **erőforráscsoport** legördülő listában válassza ki a megfelelő erőforráscsoportot, amely tartalmazza a csoporttal kapcsolatos virtuális gépeket, hogy megtekinthesse a jelentett állapotot. A kijelölés csak az állapotfigyelő szolgáltatásra vonatkozik, és nem végez átadást a **teljesítmény** -vagy a **leképezési** lapokra.

Az **állapot** lap a következő információkat tartalmazza:

* Hány virtuális gép kritikus vagy nem kifogástalan állapotban van, illetve hogy hányan egészségesek vagy nem küldenek el semmilyen adat (más néven ismeretlen állapot).
* Azt, hogy az operációs rendszer hány virtuális gépet jelentett, nem kifogástalan állapotot jelent.
* A processzor, a lemez, a memória vagy a hálózati adapter által észlelt, állapot szerinti kategorizált probléma miatt hány virtuális gép sérült.
* Hány virtuális gép sérült, mert az alapvető operációsrendszer-szolgáltatással észlelt probléma állapota szerint kategorizálva van.

A **Health (állapot** ) lapon azonosíthatja a virtuális gépet figyelő egészségügyi feltételek által észlelt kritikus problémákat, valamint áttekintheti a riasztás részleteit és a kapcsolódó tudásbázisokat. Ezek a cikkek segíthetnek a problémák diagnosztizálásában és szervizelésében. Válassza ki bármelyik csomópontot a súlyosság szerint szűrt [összes riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) oldal megnyitásához.

A virtuálisgép- **elosztás az operációs rendszer** listában a Windows-kiadás vagy a Linux-disztribúció által felsorolt virtuális gépeket jeleníti meg, valamint azok verzióját. Az egyes operációsrendszer-kategóriákban a virtuális gépek a virtuális gép állapota alapján tovább vannak bontva.

![VM-alapú virtuális gépek eloszlási perspektívája](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Válassza ki bármelyik oszlopot, beleértve a **virtuális gépek darabszámát**, a **kritikus**, a **Figyelmeztetés**, a **kifogástalan**vagy az **ismeretlen**lehetőséget. Tekintse meg a szűrt eredmények listáját a **Virtual Machines** oldalon, amely megfelel a kijelölt oszlopnak.

Ha például át szeretné tekinteni az összes olyan virtuális gépet, amelyen a 7,5-es kiadás fut Red Hat Enterprise Linux, válassza ki a **virtuális gép száma** értéket az adott operációs rendszer számára, és felsorolja a szűrőnek megfelelő virtuális gépeket és a jelenlegi állapotukat.

![Példa a Red Hat Linux rendszerű virtuális gépek összesítésére](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Az **állapot megjelenítése** jelölőnégyzetre kattint, és a rendszer visszaadja az állapotot a táblázat szűrt eredményeihez.  

![A Red Hat Linux rendszerű virtuális gépek állapotának példája](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

A lista egyik eleméhez a megfelelő állapotra kattintva elindíthatja az állapot-diagnosztika eszközt, amely azt mutatja, hogy a rendszer hogyan értékeli ki az állapotot a kiválasztott virtuális géphez. 

Ha kijelöli a virtuális gép nevét a **virtuális gép neve**alatt, a **Virtual Machines** lapon a virtuálisgép- **példány** lapra irányítja a rendszer. Ez a lap további részleteket tartalmaz a riasztások és a kiválasztott virtuális gépet érintő állapottal kapcsolatos problémákról. A lap bal felső sarkában található **állapot** ikon kiválasztásával szűrheti az állapotadatok adatait, és megtekintheti, hogy mely összetevők nem kifogástalan állapotúak. Megtekintheti a riasztás súlyossága szerint kategorizált, nem kifogástalan állapotú összetevők által generált virtuálisgép-riasztásokat is.

A virtuálisgép- **lista** nézetben válassza ki egy virtuális gép nevét, hogy megnyissa az adott virtuális gép **állapot** lapját, hasonlóan, ha közvetlenül a virtuális gépről kiválasztotta az **Adatáttekintést (előzetes verzió)** .

![A kiválasztott Azure-beli virtuális gép VM-elemzése](./media/vminsights-health/vminsights-directvm-health.png)

Azure Monitor lapon a **Virtual Machines (előzetes verzió)** megjeleníti a virtuális gép és a riasztások összesítő állapotát. Ez az állapot a súlyosság szerint van kategorizálva, amely a virtuálisgép-állapotra vonatkozó riasztásokat jelöli, amikor az állapot állapota kifogástalanról sérültre változik, a feltételek alapján. A **kritikus állapotú virtuális gépek** kiválasztása egy olyan oldalt nyit meg, amelyben egy vagy több virtuális gép szerepel egy kritikus állapotban.

Az egyik virtuális gép állapotának kiválasztásakor a virtuális gép állapot- **diagnosztika** nézete látható. Ebben a nézetben meghatározhatja, hogy mely egészségügyi feltételek tükrözzék az állapottal kapcsolatos problémákat. Amikor megnyílik az **állapot-diagnosztika** lap, megjeleníti az összes virtuálisgép-összetevőt és a hozzájuk tartozó állapotot az aktuális állapottal együtt.

További információ: [Health Diagnostics](#health-diagnostics).

Az **összes állapot megtekintése** lehetőség kiválasztásával megnyílik egy oldal, amely a szolgáltatásban elérhető összes állapotra vonatkozó feltétel listáját tartalmazza. Az adatok az alábbi lehetőségek alapján tovább szűrhetők:

* **Írja be a következőt**:. A feltételek felmérése és a figyelt virtuális gépek általános állapotának összesítése három típusú állapotot határozhat meg:
    - **Egység**. Egy virtuális gép bizonyos aspektusait méri. Ez az állapot-feltétel az összetevő teljesítményének meghatározására, a szintetikus tranzakció elvégzésére szolgáló parancsfájl futtatására, illetve a hibát jelző esemény figyelésére is alkalmas lehet. Alapértelmezés szerint a szűrő egységre van beállítva.
    - **Függőség**. Állapot-összesítést biztosít a különböző entitások között. Ez az állapot lehetővé teszi, hogy egy entitás állapota egy másik típusú entitás állapotától függjön, amely a sikeres műveletre támaszkodik.
    - **Összesítés**. A hasonló állapotú feltételek együttes állapotát adja meg. Az egység és a függőség állapotára vonatkozó feltétel általában egy összesített állapoti feltétel alatt van konfigurálva. Az entitások számos különböző állapotára vonatkozó feltétel jobb általános megszervezése mellett az összesített állapotra vonatkozó feltétel egyedi állapotot biztosít az entitások különböző csoportjai számára.

* **Kategória**. A hasonló feltételek jelentéskészítési célokra való csoportosításához használt állapot-feltételek típusa. Ezek a kategóriák a **rendelkezésre állás** és a **teljesítmény**.

A nem kifogástalan állapotú példányok megtekintéséhez válasszon egy értéket a nem megfelelő **állapotú összetevő** oszlopban. Ezen az oldalon a táblázat felsorolja a kritikus állapotú összetevőket.

## <a name="health-diagnostics"></a>Health Diagnostics

Az **állapot-diagnosztika** oldalon megtekintheti egy virtuális gép állapotának modelljét. Ez a lap felsorolja az összes virtuálisgép-összetevőt, a kapcsolódó állapotra vonatkozó feltételeket, az állapot változásait és a virtuális géphez kapcsolódó figyelt összetevők által azonosított egyéb jelentős problémákat.

![Példa egy virtuális géphez tartozó Health Diagnostics-oldalra](./media/vminsights-health/health-diagnostics-page-01.png)

Indítsa el az Health diagnosticsot az alábbi módszerek használatával:

* A Azure Monitor összesített virtuálisgép-perspektívájában lévő összes virtuális gép összesítési állapotával:

    1. Az **állapot** lapon válassza a **kritikus**, **Figyelmeztetés**, **kifogástalan**vagy **ismeretlen** állapot ikont a **vendég virtuális gép állapota**szakasz alatt.
    2. Nyissa meg az oldalt, amely felsorolja az összes, a szűrt kategóriának megfelelő virtuális gépet.
    3. Válassza **ki az Állapot oszlopban az** értéket, hogy megnyissa az adott virtuális gépre vonatkozó állapot-diagnosztika hatókörét.

* Operációs rendszer alapján a Azure Monitor összesített virtuálisgép-szemszögéből. A **virtuális gép eloszlása**területen az oszlopok bármelyikének kiválasztásakor megnyílik a **Virtual Machines** lap, és egy listát ad vissza a szűrt kategóriának megfelelő táblázatban. Az **állapot állapota** oszlopban lévő érték kiválasztásával megnyílik a kiválasztott virtuális géphez tartozó Health Diagnostics.
 
* A Azure Monitor for VMs **állapota** lapon található vendég virtuális gépről válassza az állapot- **diagnosztika megtekintése**lehetőséget.

Az Health Diagnostics két kategóriába rendezi az egészségügyi adatokat: rendelkezésre állás és teljesítmény.
 
Egy összetevőhöz (például a logikai lemezhez, a PROCESSZORhoz stb.) definiált összes állapotra vonatkozó feltétel a két kategória szűrése nélkül is megtekinthető. Ezek a nézetek a feltételek teljes nézetében, illetve a **rendelkezésre állás** vagy a **teljesítmény**lehetőség kiválasztásával, vagy az eredmények szűrésével is elvégezhetők.

Emellett a feltételek kategória az **állapot feltételei** oszlop mellett is látható. Ha a feltételek nem egyeznek a kiválasztott kategóriával, akkor a **kijelölt kategóriára vonatkozóan nem érhetők el** az **állapotot** jelző üzenet, amely az állapotfigyelő feltételek oszlopban jelenik meg.

Az állapot feltételeit négy típus egyike határozza meg: **kritikus**, **Figyelmeztetés**, **kifogástalan**és **ismeretlen**. Az első három konfigurálható, ami azt jelenti, hogy a figyelők küszöbértékeit közvetlenül az **állapot feltételeinek** beállítása ablaktáblán módosíthatja. Ez az Azure Monitor REST API [Update Monitor művelettel](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)is lehetséges. Az **ismeretlen** nem konfigurálható, és meghatározott forgatókönyvek számára van fenntartva.

Az **állapot-diagnosztika** lapon három fő szakaszt talál:

* Összetevő-modell
* Állapotra vonatkozó feltételek
* Állapotváltozások

![Az Health Diagnostics-oldal részei](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Összetevő-modell

Az **Health Diagnostics** oldal bal szélső oszlopa az **összetevő modellje**. A virtuális géphez társított összes összetevő ebben az oszlopban az aktuális állapottal együtt jelenik meg.

A következő példában a felderített összetevők a **lemez**, a **logikai lemez**, a **processzor**, a **memória**és az **operációs rendszer**. Ezen az oszlopon több példány észlelhető és jelenik meg.

Az alábbi ábra például azt mutatja, hogy a virtuális gépen két logikai lemez ( **C:** és **D:** ) példánya van, amelyek kifogástalan állapotban vannak:

![Példa az Health Diagnostics szolgáltatásban bemutatott összetevő-modellre](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Állapotra vonatkozó feltételek

Az állapot-diagnosztika oldal középső oszlopa az **állapot feltételei**. A virtuális géphez definiált állapot modell egy hierarchikus fán jelenik meg. A virtuális gép állapot-modellje egység és összesített állapotú feltételekből áll.

![Az Health Diagnostics szolgáltatásban bemutatott állapotra vonatkozó feltételek – példa](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Az állapotra vonatkozó feltétel egy figyelt példány állapotát méri, amely lehet egy küszöbérték, egy entitás állapota stb. Az állapotra vonatkozó feltétel a korábban leírtaknak megfelelően két vagy három konfigurálható állapot-küszöbértéket tartalmaz. Az állapotra vonatkozó feltétel minden esetben csak az egyik lehetséges állapotában lehet.

Az állapot modell olyan feltételeket határoz meg, amelyek meghatározzák a cél általános céljának és összetevőinek állapotát. A feltételek hierarchiája az **állapot-diagnosztika** lapon az **állapot feltételek** szakaszban látható.

Az állapot-összesítési házirend az összesített állapotra vonatkozó feltételek konfigurációjának részét képezi (az alapértelmezett beállítás a **legrosszabb**értékre van állítva). Ennek a funkciónak a részeként a jelen cikk [figyelés konfigurációs részletek](#monitoring-configuration-details) szakaszában találhatja meg az alapértelmezett állapotra vonatkozó feltételeket.

A Azure Monitor REST API [figyelése a példányok listájáról erőforrás](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) alapján az összes állapotra vonatkozó feltétel listájának lekéréséhez. Ez a feltétel az Azure VM-erőforráson futó konfigurációs adatokat tartalmazza.

Az **egység** állapotára vonatkozó feltétel típusa a jobb oldalon található három pontot mutató hivatkozás kiválasztásával módosítható. Válassza a **Részletek megjelenítése** lehetőséget a konfigurációs ablaktábla megnyitásához.

![Állapotra vonatkozó feltételek konfigurálása – példa](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Ha a kiválasztott állapothoz tartozó konfigurációs ablaktáblában az **írási idő átlagosan**megjelenő számát használja, a küszöbértéket más numerikus értékkel is konfigurálhatja. Ez egy kétállapotú figyelő, ami azt jelenti, hogy csak az **egészségestől** a **figyelmeztetésig**változhat.

Más állapotra vonatkozó feltételek időnként három állapotot használnak, ahol a figyelmeztetési és a kritikus állapot küszöbértékeit is konfigurálhatja. A küszöbértékeket Azure Monitor REST API [figyelő konfigurációjának](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)használatával is módosíthatja.

>[!NOTE]
>Az állapot-feltételek konfigurációs módosításainak alkalmazása egyetlen példányra az összes figyelt példányra vonatkozik. Ha például a **lemez-1 D:** lehetőséget választja, majd írási küszöbértékként módosítja a **lemez átlagos másodpercét** , a módosítás a virtuális gépen észlelt és megfigyelt összes példányra érvényes lesz.


![Egy egység figyelő állapotára vonatkozó feltételek konfigurálása – példa](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Ha többet szeretne megtudni az állapottal kapcsolatos feltételekről, a Tudásbázisban bemutatjuk a problémák, az okok és a felbontások azonosítását segítő cikkeket. A kapcsolódó Tudásbázis megtekintéséhez válassza az oldal **információinak megtekintése** lehetőséget.

A Azure Monitor for VMs Health szolgáltatásban található összes tudásbáziscikk áttekintéséhez tekintse meg a [Azure monitor Health Knowledge dokumentációját](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Állapot módosításai

Az **Health Diagnostics** oldal jobb szélső oszlopa az **állapot változásai**. Ez az oszlop felsorolja az állapotra vonatkozó **feltételek** szakaszban kiválasztott állapottal kapcsolatos összes változást, vagy a virtuális gép állapotának változását, ha a virtuális gép a tábla **összetevő-modell** vagy **állapot-feltételek** oszlopában lett kiválasztva.

![Állapot-diagnosztika – példa az állapotadatok bemutatására](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A következő szakasz az állapot-feltételek állapotát és a hozzá tartozó időpontot mutatja be. Ez az információ az oszlop tetején található legutóbb látható állapotot jeleníti meg.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Az összetevő-modell, az állapot-feltételek és az állapotváltozás oszlopainak társítása

A három oszlop egymáshoz van csatolva. Ha kiválaszt egy példányt az összetevő- **modell** oszlopban, az **állapot feltétele** oszlop az adott összetevő nézetére lesz szűrve. Ennek megfelelően az **Állapot módosítása** oszlop a kiválasztott állapot alapján frissül.

![Példa a figyelt példány és az eredmények kiválasztására](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Ha például a *lemez-1 d:* lehetőséget választja, a listában az **összetevő-modell**területen az állapot- **feltételek** szűrők a *lemezre-1d:* értékre, az **állapot változásai** pedig az *1 D lemez*rendelkezésre állása alapján változnak.

A frissített állapot megjelenítéséhez a **frissítés** hivatkozásra kattintva frissítheti az állapot-diagnosztika lapot. Ha az állapotra vonatkozó feltétel állapota az előre definiált lekérdezési időköz alapján frissül, ez a feladat lehetővé teszi, hogy elkerülje a legutóbbi állapot várakozását, és tükrözze azt. Az állapotra **vonatkozó feltételek állapota** olyan szűrő, amely lehetővé teszi az eredmények hatókörét a kiválasztott állapot alapján: kifogástalan, figyelmeztetés, kritikus, ismeretlen és mind. A jobb felső sarokban lévő **utolsó frissítés** az állapot-diagnosztika lap utolsó frissítésekor jelent meg.

## <a name="alerts"></a>Riasztások

Azure Monitor for VMs az állapot integrálható az [Azure-riasztásokkal](../../azure-monitor/platform/alerts-overview.md). Riasztást küld, ha a rendszer előre definiált feltételek alapján észleli, hogy kifogástalan állapotról sérült állapotra vált. A riasztások súlyosság szerint vannak kategorizálva, a "0" és "4" között, a legmagasabb szinttel 0.

A riasztások nincsenek társítva egy műveleti csoporttal, hogy értesítést kapjon a riasztás aktiválásakor. Az előfizetés hatókörében lévő tulajdonosi szerepkörrel rendelkező felhasználónak a [riasztások beállítása](#configure-alerts) szakaszban leírt lépésekkel kell konfigurálnia az értesítéseket.

A súlyosság szerint kategorizált virtuálisgép-állapot-riasztások teljes száma a **riasztások** szakaszban található **állapot** -irányítópulton érhető el. Amikor kiválasztja a riasztások teljes számát vagy a súlyossági szintnek megfelelő számot, megnyílik a **riasztások** lap, és felsorolja a kijelölésnek megfelelő összes riasztást.

Ha például az **1. szinthez**tartozó sort választja, a következő nézetet fogja látni:

![1\. súlyossági szintű riasztások – példa](./media/vminsights-health/vminsights-sev1-alerts-01.png)

A **minden riasztás** lap hatóköre nem csak a kiválasztott riasztásokat jeleníti meg. Emellett az **erőforrástípus** szerint is szűrve jelenik meg, hogy csak a virtuálisgép-erőforrások által generált állapot-riasztásokat jelenítse meg. Ez a formátum a riasztások listájában jelenik meg, az oszlop **céljának erőforrása**alatt, ahol az Azure-beli virtuális gép a felmerült riasztást jeleníti meg, ha nem megfelelő állapot teljesül.

A más erőforrástípusok vagy szolgáltatások riasztásai nem tartoznak ebbe a nézetbe. Ezek a riasztások közé tartoznak a naplózási riasztások, amelyek olyan naplózási lekérdezéseken vagy metrikai riasztásokon alapulnak, amelyeket általában az alapértelmezett Azure Monitor [minden riasztás](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) lapon megtekint.

A nézet szűréséhez válassza az értékek lehetőséget a lap tetején található legördülő menükben.

|Oszlop |Leírás |
|-------|------------|
|Előfizetést |Válasszon ki egy Azure-előfizetést. Csak a kijelölt előfizetésben lévő riasztások szerepelnek a nézetben. |
|Erőforráscsoport |Válasszon ki egy erőforráscsoportot. Csak a kijelölt erőforráscsoport céljaival rendelkező riasztások szerepelnek a nézetben. |
|Erőforrás típusa |Válasszon ki egy vagy több erőforrástípust. Alapértelmezés szerint csak a cél **virtuális gépekre** vonatkozó riasztások vannak kiválasztva, és ebben a nézetben szerepelnek. Ez az oszlop csak az erőforráscsoport megadása után érhető el. |
|Erőforrás |Válasszon ki egy erőforrást. A nézet csak az adott erőforrással rendelkező riasztásokat tartalmazza célként. Ez az oszlop csak az erőforrástípus megadása után érhető el. |
|Severity |Válasszon ki egy riasztási súlyosságot, vagy válassza az **összes** lehetőséget az összes súlyossági szintű riasztás befoglalásához. |
|Figyelési feltétel |Válassza ki a figyelési feltételt a riasztások szűréséhez, ha azokat a rendszer kilőtte vagy feloldotta, ha a feltétel már nem aktív. Vagy válassza az **összes** lehetőséget az összes feltétel riasztásának befoglalásához. |
|Riasztás állapota |Válassza ki a riasztási állapotot, az **új**, a **nyugtázás**, a **lezárt**vagy az **összes** beállítást, hogy az összes állapotú riasztást tartalmazzon. |
|Szolgáltatás figyelése |Válasszon ki egy szolgáltatást, vagy válassza az **összes** lehetőséget az összes szolgáltatás belefoglalásához. Ehhez a szolgáltatáshoz csak a VM-adatokból származó riasztások támogatottak.|
|Időtartomány| A nézet csak a kijelölt időintervallumban kilőtt riasztásokat tartalmazza. A támogatott értékek az elmúlt óra, az elmúlt 24 óra, az elmúlt 7 nap és az elmúlt 30 nap. |

Amikor kiválaszt egy riasztást, megjelenik a **riasztás részletei** lap. Ez az oldal a riasztás részleteit tartalmazza, és lehetővé teszi az állapotának módosítását.

A riasztások kezelésével kapcsolatos további tudnivalókért tekintse meg a [riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md)című témakört.

>[!NOTE]
>Jelenleg nem támogatott az új riasztások létrehozása az állapot feltételei alapján, vagy a Azure Monitor meglévő állapot-riasztási szabályainak módosítása a portálon.


![Riasztás részleteit tartalmazó ablaktábla egy kiválasztott riasztáshoz](./media/vminsights-health/alert-details-pane-01.png)

Egy vagy több riasztás állapotának megváltoztatásához jelölje ki őket, majd a bal felső sarokban található **minden riasztás** lapon válassza az **Állapot módosítása** lehetőséget. Válassza ki az egyik állapotot a **riasztás állapotának módosítása** panelen, adja meg a módosítás leírását a **Megjegyzés** mezőben, majd kattintson **az OK** gombra a módosítások elvégzéséhez. Amikor az adatok ellenőrzése megtörtént, és a módosítások érvénybe lépnek, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="configure-alerts"></a>Riasztások konfigurálása
A Azure Portal nem kezelhet bizonyos riasztási felügyeleti feladatokat. Ezeket a feladatokat a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)használatával kell elvégezni. Ezek:

- Állapotra vonatkozó riasztás engedélyezése vagy letiltása
- Értesítések beállítása az állapotra vonatkozó feltételekkel kapcsolatos riasztásokhoz

Mindegyik példa [ARMClient](https://github.com/projectkudu/armclient) használ a Windows rendszerű gépen. Ha nem ismeri ezt a metódust, olvassa el a [ARMClient használata](../platform/rest-api-walkthrough.md#use-armclient)című témakört.

#### <a name="enable-or-disable-an-alert-rule"></a>Riasztási szabály engedélyezése vagy letiltása

Egy adott állapotra vonatkozó riasztás engedélyezéséhez vagy letiltásához a **alertGeneration** tulajdonságot **letiltott** vagy **engedélyezett**értékkel kell módosítani.

Az alábbi példa azt mutatja be, hogyan lehet lekérdezni az adott *monitorId* az adott állapotra vonatkozóan a **LogicalDisk\Avg lemez másodpercek száma**szerint:

1. A terminál ablakban írja be a **armclient. exe bejelentkezési nevet**. Ezzel felszólítja, hogy jelentkezzen be az Azure-ba.

2. Adja meg a következő parancsot az adott virtuális gépen aktív összes állapotra vonatkozó feltétel lekéréséhez, és a *monitorId* tulajdonság értékének azonosításához:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    A következő példa a *ARMCLIENT Get* parancs kimenetét mutatja be. Jegyezze fel a *MonitorId*értékét. Ezt az értéket kötelező megadni a következő lépéshez, ahol meg kell adni az állapotra vonatkozó feltételek AZONOSÍTÓját, és módosítania kell a tulajdonságát, hogy riasztást hozzon létre.

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

3. A *alertGeneration* tulajdonság módosításához írja be a következő parancsot:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Adja meg a 2. lépésben használt GET parancsot annak ellenőrzéséhez, hogy a tulajdonság értéke **Letiltva**értékre van-e állítva.

#### <a name="associate-an-action-group-with-health-criteria"></a>Műveleti csoport hozzárendelése állapotra vonatkozó feltételekkel

A Azure Monitor for VMs Health támogatja az SMS-és e-mail-értesítéseket, ha a riasztások nem kifogástalan állapotú feltételek alapján jönnek létre. Az értesítések konfigurálásához jegyezze fel az SMS-vagy e-mail-értesítések küldésére konfigurált műveleti csoport nevét.

>[!NOTE]
>Ezt a műveletet minden olyan megfigyelt virtuális gépen el kell végrehajtani, amelyhez értesítést szeretne kapni. Az erőforráscsoport összes virtuális gépe esetében nem érvényes.

1. A terminál ablakban írja be a *armclient. exe bejelentkezési nevet*. Ezzel felszólítja, hogy jelentkezzen be az Azure-ba.

2. Adja meg a következő parancsot a műveleti csoport riasztási szabályokkal való hozzárendeléséhez:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. A következő parancs megadásával ellenőrizheti, hogy a **actionGroupResourceIds** tulajdonság értéke sikeresen frissítve lett-e:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    A kimenetnek a következő feltételekhez hasonlóan kell kinéznie:
    
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

## <a name="next-steps"></a>Következő lépések

- A korlátozások és a virtuális gépek teljes teljesítményének azonosításához lásd: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md).

- Az észlelt alkalmazások függőségeivel kapcsolatos további tudnivalókért lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).
