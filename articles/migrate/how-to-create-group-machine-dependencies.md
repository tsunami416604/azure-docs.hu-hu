---
title: Ügynök-alapú függőségi elemzés beállítása Azure Migrate Server Assessment-ben
description: Ez a cikk azt ismerteti, hogyan állítható be az ügynök-alapú függőségek elemzése Azure Migrate Server Assessment-ben.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79453615"
---
# <a name="set-up-dependency-visualization"></a>Függőségi vizualizáció beállítása

Ez a cikk azt ismerteti, hogyan állítható be az ügynök-alapú függőségi elemzés a Azure Migrate: Server Assessment. A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és migrálni kívánt gépek függőségeinek azonosításában és megismerésében.

## <a name="before-you-start"></a>Előkészületek

- [További információ az](concepts-dependency-visualization.md#agent-based-analysis) ügynök-alapú függőségek elemzéséről.
- Tekintse át a [VMWare virtuális gépek](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)és [Hyper-V virtuális gépek](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)ügynök-alapú függőségi vizualizációjának beállításához szükséges előfeltételeket és támogatási követelményeket.
- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
- Győződjön meg arról, hogy beállított egy [Azure Migrate berendezést](migrate-appliance.md) a helyszíni gépek felderítéséhez. Ismerje meg, hogyan állíthat be egy készüléket [VMware](how-to-set-up-appliance-vmware.md)-, [Hyper-V](how-to-set-up-appliance-hyper-v.md)-vagy [fizikai kiszolgálókhoz](how-to-set-up-appliance-physical.md). A készülék felfedi a helyszíni gépeket, és metaadatokat, teljesítményadatokat Azure Migrate küld: a kiszolgáló értékelését.
- A függőségi vizualizáció használatához egy [log Analytics munkaterületet](../azure-monitor/platform/manage-access.md) társít egy Azure Migrate projekthez:
    - Munkaterületet csak a Azure Migrate berendezés beállítása után csatolhat, és felkeresheti a Azure Migrate projektben található gépeket.
    - Győződjön meg arról, hogy rendelkezik az előfizetésben a Azure Migrate projektet tartalmazó munkaterülettel.
    - A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.
    - A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Új vagy meglévő Log Analytics munkaterületet társíthat egy Azure Migrate projekthez.
    - A munkaterületet a gép függőségi vizualizációjának első beállításakor csatolja. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
    - Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.

## <a name="associate-a-workspace"></a>Munkaterület hozzárendelése

1. Miután felderítte a gépeket az értékeléshez, a **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson az **Áttekintés**elemre.  
2. **Azure Migrate: kiszolgáló értékelése**, kattintson az **Essentials**elemre.
3. A **OMS munkaterületen**kattintson a **Konfigurálás szükséges**elemre.

     ![Log Analytics munkaterület konfigurálása](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Az **OMS-munkaterület konfigurálása**lapon adja meg, hogy új munkaterületet kíván-e létrehozni, vagy egy meglévőt szeretne használni.
    - Kiválaszthat egy meglévő munkaterületet az áttelepítés projekt-előfizetés összes munkaterületéről.
    - Az olvasóhoz való hozzáférésre van szüksége a munkaterülethez a hozzárendeléshez.
5. Ha új munkaterületet hoz létre, válassza ki a kívánt helyet.

    ![Új munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

Az összes elemezni kívánt gépen telepítse az ügynököket.

> [!NOTE]
> System Center Operations Manager 2012 R2 vagy újabb rendszer által figyelt gépek esetében nem kell telepítenie az MMA-ügynököt. A Service Map a Operations Managersal integrálódik. [Kövesse](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) az integrációs útmutatót.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Minden olyan géphez, amelyet elemezni szeretne a függőségi vizualizációval, kattintson a **függőségek** oszlopban az **ügynök telepítésének**megkezdése elemre.
3. A **függőségek** lapon töltse le a Windows vagy Linux rendszerhez készült MMA-és függőségi ügynököt.
4. Az **MMA-ügynök konfigurálása**területen másolja a munkaterület azonosítóját és kulcsát. Ezek az MMA-ügynök telepítésekor szükségesek.

    ![Az ügynökök telepítése](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Az MMA telepítése

Telepítse az MMA-t minden elemezni kívánt Windows-vagy Linux-gépen.

### <a name="install-mma-on-a-windows-machine"></a>Az MMA telepítése Windows rendszerű gépen

Az ügynök telepítése Windows rendszerű gépre:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. **A licencfeltételek oldalon kattintson** **az Elfogadom gombra a** licenc elfogadásához.
3. A **célmappában**tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > a **Tovább gombra**.
4. Az **ügynök telepítési beállításai**területen válassza az **Azure log Analytics** > **tovább**lehetőséget.
5. Új Log Analytics munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be azt a munkaterület-azonosítót és-kulcsot, amelyet a portálról másolt. Kattintson a **Tovább** gombra.

Az ügynököt a parancssorból vagy egy automatizált módszerrel, például Configuration Manager vagy [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196)is telepítheti.
- [További](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) információ az MMA-ügynök telepítésével kapcsolatban ezen módszerek használatával.
- Az MMA-ügynök ezzel a [szkripttel](https://go.microsoft.com/fwlink/?linkid=2104394) is telepíthető.
- [További](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) információ az MMA által támogatott Windows operációs rendszerekről.

### <a name="install-mma-on-a-linux-machine"></a>Az MMA telepítése Linux rendszerű gépen

Az MMA telepítése Linux rendszerű gépre:

1. Vigye át a megfelelő köteget (x86 vagy x64) a Linux rendszerű számítógépre SCP/SFTP használatával.
2. Telepítse a csomagot a--install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) információ az MMA által támogatott Linux operációs rendszerek listájáról. 

## <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows rendszerű gépen való telepítéséhez kattintson duplán a telepítési fájlra, és kövesse a varázslót.
2. A függőségi ügynök Linux rendszerű gépen történő telepítéséhez a következő parancs használatával root-ként kell telepíteni:

    ```sh InstallDependencyAgent-Linux64.bin```

- [További](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) információ arról, hogyan használhatók a parancsfájlok a függőségi ügynök telepítéséhez.
- [További](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) információ a függőségi ügynök által támogatott operációs rendszerekről.


## <a name="create-a-group-using-dependency-visualization"></a>Csoport létrehozása függőségi vizualizáció használatával

Most hozzon létre egy csoportot az értékeléshez. 


> [!NOTE]
> Azok a csoportok, amelyeknek a függőségeit szeretné megjeleníteni, nem tartalmazhatnak 10 rendszernél több gépet. Ha több mint 10 géppel rendelkezik, ossza fel őket kisebb csoportokba.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. A **függőségek** oszlopban kattintson a **függőségek megtekintése** elemre minden áttekinteni kívánt gépen.
3. A függőségi térképen a következők láthatók:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a gépre és a gépről.
    - Azok a függő gépek, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A telepített függőségi ügynökökkel rendelkező függő gépek külön mezőkként jelennek meg.
    - A gépen futó folyamatok. Bontsa ki az egyes gépek mezőt a folyamatok megtekintéséhez.
    - Számítógép tulajdonságai (beleértve a teljes tartománynevet, az operációs rendszert, a MAC-címeket). A részletek megtekintéséhez kattintson az egyes gépek mezőre.

4. A különböző időtartamokra vonatkozó függőségeket az időtartomány címkében lévő idő időtartamára kattintva tekintheti meg.
    - Alapértelmezés szerint a tartomány egy óra. 
    - Módosíthatja az időtartományt, vagy megadhatja a kezdő és a záró dátumot, valamint az időtartamot.
    - Az időtartomány akár egy óráig is elvégezhető. Ha hosszabb tartományra van szüksége, használja a Azure Monitort a függő adatlekérdezés hosszabb időtartamra való lekéréséhez.

5. Miután azonosította a csoportba felvenni kívánt függő gépeket, a CTRL billentyűt lenyomva tartva kiválaszthat több gépet a térképen, majd kattintson a **csoport gépek**elemre.
6. Adja meg a csoport nevét.
7. Győződjön meg arról, hogy a Azure Migrate a függő gépeket deríti fel.

    - Ha egy függő gépet nem talál a Azure Migrate: Server Assessment, nem tudja felvenni a csoportba.
    - Számítógép hozzáadásához futtassa újra a felderítést, és ellenőrizze, hogy a gép fel van-e derítve.

8. Ha értékelést szeretne készíteni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, és hozzon létre egy új értékelést a csoport számára.
8. A csoport mentéséhez kattintson **az OK** gombra.

A csoport létrehozása után javasoljuk, hogy telepítse az ügynököket a csoport összes számítógépére, majd jelenítse meg a függőségeket a teljes csoport számára.

## <a name="query-dependency-data-in-azure-monitor"></a>Függőségi adatlekérdezések Azure Monitor

A Azure Migrate projekthez társított Log Analytics munkaterületen Service Map által rögzített függőségi adatmennyiséget kérdezheti le. A Log Analytics Azure Monitor naplók írására és futtatására szolgál.

- [Megtudhatja, hogyan](../azure-monitor/insights/service-map.md#log-analytics-records) kereshet Service Map-adatLog Analyticsokban.
- [Tekintse](../azure-monitor/log-query/get-started-queries.md) át a [log Analytics](../azure-monitor/log-query/get-started-portal.md)naplójának lekérdezéseit.

Futtasson egy lekérdezést a függőségi értékekhez a következő módon:

1. Az ügynökök telepítése után lépjen a portálra, és kattintson az **Áttekintés**elemre.
2. **Azure Migrate: kiszolgáló értékelése**, kattintson az **Áttekintés**elemre. Az **Essentials**kibontásához kattintson a lefelé mutató nyílra.
3. A **OMS munkaterületen**kattintson a munkaterület nevére.
3. A Log Analytics munkaterület oldalon > **általános**lapon kattintson a **naplók**elemre.
4. Írja be a lekérdezést, és kattintson a **Futtatás**gombra.

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány példa a függőségi adatok kinyerésére.

- A lekérdezéseket módosíthatja az előnyben részesített adatpontok kinyeréséhez.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) a függőségi adatrekordok teljes listáját.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) a további mintavételi lekérdezéseket.

#### <a name="sample-review-inbound-connections"></a>Minta: bejövő kapcsolatok áttekintése

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

#### <a name="sample-summarize-sent-and-received-data"></a>Minta: az elküldött és fogadott adatértékek összefoglalása

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

[Hozzon létre egy értékelést](how-to-create-assessment.md) egy csoport számára.


