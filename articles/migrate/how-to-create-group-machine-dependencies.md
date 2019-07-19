---
title: Gépek csoportosítása gépi függőségekkel Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet értékelést létrehozni a Azure Migrate szolgáltatással rendelkező számítógép-függőségek használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 7cde18f2da764a055443900e7daf160f72e2eeb5
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301658"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Függőségi vizualizáció beállítása az értékeléshez

Ez a cikk azt ismerteti, hogyan állítható be a függőségi leképezés a Azure Migrateban: Kiszolgáló értékelése.

A függőségi leképezés segítségével megjelenítheti a függőségeket a felmérni és áttelepíteni kívánt gépek között.

- Azure Migrate: A kiszolgálók értékelése során összegyűjtheti a gépeket az értékeléshez. Általában azokat a gépeket, amelyeket együtt szeretne áttelepíteni.
- Jellemzően függőségi leképezést használ, ha a magasabb szintű megbízhatóságú csoportokat szeretné felmérni.
- A függőségek leképezése segítséget nyújt a számítógép függőségeinek ellenőrzéséhez az értékelés és a Migrálás futtatása előtt.
- A függőségek leképezése és megjelenítése révén hatékonyan megtervezheti az Azure-ba való áttelepítést. Ezzel biztosítható, hogy semmi ne maradjon hátra, így elkerülhetők a váratlan kimaradások az áttelepítés során.
- A leképezés használatával felderítheti azokat az egymástól függő rendszereket, amelyeknek együtt kell áttelepíteniük. Azt is meghatározhatja, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt.

[További](concepts-dependency-visualization.md#how-does-it-work) információ a függőségi vizualizációról.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Kiszolgáló-értékelési eszköz.
- Győződjön meg arról, hogy felderítette a gépeket a Azure Migrate; Ezt úgy teheti meg, hogy a [VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)Azure Migrate berendezését állítja be. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld a Azure Migratenak: Kiszolgáló értékelése. [További információk](migrate-appliance.md).


**Szolgáltatások** | **Megjegyzés**
--- | ---
Rendelkezésre állás | A függőségi vizualizáció nem érhető el Azure Governmentban.
Szolgáltatástérkép | A függőségi vizualizáció Service Map megoldást használ Azure Monitor naplókban. [Service Map](../azure-monitor/insights/service-map-configure.md) automatikusan felfedi és megjeleníti a kiszolgálók közötti kapcsolatokat.
Ügynökök | A függőségi vizualizáció használatához telepítsen néhány ügynököt a leképezni kívánt gépekre:<br/> - [Azure log Analytics](../azure-monitor/platform/log-analytics-agent.md) -ügynök (korábbi nevén Microsoft monitoring Agent (MMA).<br/> – Service Map függőségi ügynököt.<br/><br/> Az ügynök telepítésének automatizálásához használhat olyan központi telepítési eszközt, mint például a System Center Configuration Manager vagy egy partneri eszköz, például a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), amely rendelkezik ügynök-telepítési megoldással Azure Migratehoz.
Függőségi ügynök | Tekintse át a [Windows](/azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) és a [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems)függőségi ügynökének támogatását.<br/><br/> [További](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) információ a függőségi ügynök telepítéséhez szükséges parancsfájlok használatával.
Log Analytics ügynök (MMA) | [További](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) információ az MMA telepítési módszereiről.<br/><br/> System Center Operations Manager 2012 R2 vagy újabb rendszer által figyelt gépek esetében nem kell telepítenie az MMA-ügynököt. A Service Map a Operations Managersal integrálódik. Az integrációt az [itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)található útmutatás használatával engedélyezheti. Vegye figyelembe azonban, hogy a függőségi ügynöknek telepítve kell lennie ezekre a gépekre.<br/><br/> [Tekintse át](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) a log Analytics ügynök által támogatott Linux operációs rendszereket.
Értékelési csoportok | Azok a csoportok, amelyeknek a függőségeit szeretné megjeleníteni, nem tartalmazhatnak 10 rendszernél több gépet. Ha több mint 10 géppel rendelkezik, a függőségek megjelenítéséhez ossza fel őket kisebb csoportokba.

## <a name="associate-a-log-analytics-workspace"></a>Log Analytics munkaterület hozzárendelése

A függőségi vizualizáció használatához hozzá kell rendelnie egy [log Analytics](../azure-monitor/platform/manage-access.md) munkaterületet egy Azure Migrate projekthez.

- Csak a Azure Migrate projekt-előfizetésben csatolhat munkaterületet.
- Egy meglévő munkaterületet is csatolhat, vagy létrehozhat egy újat.
- A munkaterületet a gép függőségi vizualizációjának első beállításakor csatolja.
- A munkaterületet csak a Azure Migrate projektben lévő gépek felfedése után lehet csatlakoztatni. Ezt úgy teheti meg, hogy a [VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)Azure Migrate berendezését állítja be. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld a Azure Migratenak: Kiszolgáló értékelése. [További információk](migrate-appliance.md).

Csatoljon egy munkaterületet az alábbiak szerint:

1. **Azure Migrate: Kiszolgáló értékelése**, kattintson az **Áttekintés**elemre. Ha még nem adta hozzá a kiszolgáló-értékelési eszközt, [tegye](how-to-assess.md)meg az elsőt.
2. Az **Áttekintés**területen kattintson a lefelé mutató nyílra az **alapvető**erőforrások kibontásához.
3. A **OMS**munkaterületen kattintson a **Konfigurálás szükséges**elemre.
4. A **munkaterület konfigurálása**lapon adja meg, hogy új munkaterületet kíván-e létrehozni, vagy egy meglévőt szeretne használni:

    ![Munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Miután megadta az új munkaterülethez tartozó nevet, a rendszer ugyanabban a földrajzi régióban jön létre, mint a Azure Migrate-projekt.
    - Ha meglévő munkaterületet csatol, az összes elérhető munkaterületet az áttelepítési projekttel megegyező előfizetésben is kiválaszthatja.
    - A munkaterülethez olvasói hozzáféréssel kell rendelkeznie ahhoz, hogy csatolni lehessen.
    - A projekthez társított munkaterület nem módosítható a csatolása után.

## <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

Töltse le és telepítse az ügynököket minden olyan helyszíni gépen, amelyet a függőségi leképezéssel szeretne megjeleníteni.

1. **Azure Migrate: Kiszolgáló értékelése**, kattintson a felderített **kiszolgálók**elemre.
2. Minden olyan géphez, amelyhez függőségi vizualizációt kíván használni, kattintson az **ügynök telepítésének**megkezdése elemre.
3. A gép **függőségek** oldalán > **töltse le és telepítse az MMA**-t, töltse le a megfelelő ügynököt, és telepítse az alább leírtak szerint.
4. A **függőségi ügynök letöltése és telepítése**területen töltse le a megfelelő ügynököt, és telepítse az alább leírtak szerint.
5. Az **MMA-ügynök konfigurálása**területen másolja a munkaterület azonosítóját és kulcsát. Ezek az MMA-ügynök telepítésekor szükségesek.

### <a name="install-the-mma"></a>Az MMA telepítése

#### <a name="install-the-agent-on-a-windows-machine"></a>Az ügynök telepítése Windows rendszerű gépre

Az ügynök telepítése Windows rendszerű gépre:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3. A **célmappában**tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > a **Tovább gombra**.
4. Az **ügynök telepítési beállításai**területen válassza az **Azure log Analytics** > **tovább**lehetőséget.
5. Új Log Analytics munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be azt a munkaterület-azonosítót és-kulcsot, amelyet a portálról másolt. Kattintson a **Tovább** gombra.

#### <a name="install-the-agent-on-a-linux-machine"></a>Az ügynök telepítése Linux rendszerű gépre

Az ügynök telepítése Linux rendszerű gépre:

1. Vigye át a megfelelő köteget (x86 vagy x64) a Linux rendszerű számítógépre SCP/SFTP használatával.
2. Telepítse a csomagot a--install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök Windows rendszerű gépen való telepítéséhez kattintson duplán a telepítési fájlra, és kövesse a varázslót.
2. A függőségi ügynök Linux rendszerű gépen történő telepítéséhez a következő parancs használatával root-ként kell telepíteni:

    ```sh InstallDependencyAgent-Linux64.bin```

## <a name="create-a-group-using-dependency-visualization"></a>Csoport létrehozása függőségi vizualizáció használatával

1. **Azure Migrate: Kiszolgáló értékelése**, kattintson a felderített **kiszolgálók**elemre.
2. A **függőségek** oszlopban kattintson a **függőségek megtekintése** elemre minden áttekinteni kívánt gépen.
3. A függőségi térképen a következők láthatók:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a gépre és a gépről.
    - Azok a függő gépek, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A telepített függőségi ügynökökkel rendelkező függő gépek külön mezőkként jelennek meg.
    - A gépen futó folyamatok. Bontsa ki az egyes gépek mezőt a folyamatok megtekintéséhez.
    - Számítógép tulajdonságai (beleértve a teljes tartománynevet, az operációs rendszert, a MAC-címeket). A részletek megtekintéséhez kattintson az egyes gépek mezőre.

4. A különböző időtartamokra vonatkozó függőségeket az időtartomány címkében lévő idő időtartamára kattintva tekintheti meg. Alapértelmezés szerint a tartomány egy óra. Módosíthatja az időtartományt, vagy megadhatja a kezdő és a záró dátumot, valamint az időtartamot.

    > [!NOTE]
    > Az időtartomány akár egy óráig is elvégezhető. Ha hosszabb tartományra van szüksége, használja a Azure Monitort a függő adatlekérdezés hosszabb időtartamra való lekéréséhez.

5. Miután azonosította a csoportba felvenni kívánt függő gépeket, a CTRL billentyűt lenyomva tartva kiválaszthat több gépet a térképen, majd kattintson a **csoport gépek**elemre.
6. Adja meg a csoport nevét.
7. Győződjön meg arról, hogy a Azure Migrate a függő gépeket deríti fel.

    - Ha a Azure Migrate nem észleli a függő gépet: A kiszolgáló értékelése nem vehető fel a csoportba.
    - Számítógép hozzáadásához futtassa újra a felderítést, és ellenőrizze, hogy a gép fel van-e derítve.

8. Ha értékelést szeretne készíteni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, és hozzon létre egy új értékelést a csoport számára.
8. A csoport mentéséhez kattintson **az OK** gombra.

A csoport létrehozása után javasoljuk, hogy telepítse az ügynököket a csoport összes számítógépére, majd jelenítse meg a függőségeket a teljes csoport számára.

## <a name="query-dependency-data-in-azure-monitor"></a>Függőségi adatlekérdezések Azure Monitor

A Azure Migrate projekthez társított Log Analytics munkaterületen Service Map által rögzített függőségi adatait kérdezheti le. A Log Analytics Azure Monitor naplók írására és futtatására szolgál.

- Megtudhatja, [Hogyan](../azure-monitor/insights/service-map.md#log-analytics-records) kereshet Service Map-adatLog Analyticsokban.
- [Tekintse](../azure-monitor/log-query/get-started-queries.md) át a [log Analytics](../azure-monitor/log-query/get-started-portal.md)naplójának lekérdezéseit.

Futtasson egy lekérdezést a függőségi értékekhez a következő módon:

1. Az ügynökök telepítése után lépjen a portálra, és kattintson az **Áttekintés**elemre.
2. **Azure Migrate: Kiszolgáló értékelése**, kattintson az **Áttekintés**elemre. Az **Essentials**kibontásához kattintson a lefelé mutató nyílra.
3. A **OMS**munkaterületen kattintson a munkaterület nevére.
3. A Log Analytics munkaterület oldalon > **általános**lapon kattintson a **naplók**elemre.
4. Írja be a lekérdezést, és kattintson a **Futtatás**gombra.

### <a name="sample-queries"></a>Mintalekérdezések

A függőségi adatok kinyeréséhez használhatunk egy minta típusú lekérdezéseket.

- A lekérdezéseket módosíthatja az előnyben részesített adatpontok kinyeréséhez.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) a függőségi adatrekordok teljes listáját.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) a további mintavételi lekérdezéseket.

#### <a name="sample-review-inbound-connections"></a>Minta: Bejövő kapcsolatok áttekintése

Egy virtuális gép bejövő kapcsolatainak áttekintése.

- A kapcsolati metrikák (VMConnection) táblában lévő rekordok nem jelölik az egyes fizikai hálózati kapcsolatokat.
- A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva.
- [További](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) információ a fizikai hálózati kapcsolatok adatainak összesítéséről a VMConnection-ben.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Minta: Küldött és fogadott adathalmazok összegzése

Ez a minta összefoglalja a számítógépek egy készlete közötti bejövő kapcsolatokon küldött és fogadott adatmennyiséget.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>További lépések

[Hozzon létre](how-to-create-assessment.md) egy értékelést egy csoport számára.
