---
title: Ügynökalapú függőségelemzés beállítása az Azure Áttelepítési kiszolgáló felmérésében
description: Ez a cikk bemutatja, hogyan állíthat be ügynökalapú függőségi elemzést az Azure Áttelepítési kiszolgáló értékelése.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453615"
---
# <a name="set-up-dependency-visualization"></a>Függőségi megjelenítés beállítása

Ez a cikk bemutatja, hogyan állíthat be ügynökalapú függőségi elemzést az Azure Áttelepítés:Kiszolgáló értékelése. [A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja és megértheti a felmérni kívánt és az Azure-ba áttelepíteni kívánt gépek közötti függőségeket.

## <a name="before-you-start"></a>Előkészületek

- További információ az ügynökalapú függőségi [elemzésről.](concepts-dependency-visualization.md#agent-based-analysis)
- Tekintse át a [VMware virtuális gépek,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [a fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)és a [hyper-v vm-ek](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)ügynökalapú függőségi vizualizációjának beállításának előfeltételeit és támogatási követelményeit.
- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [hozzáadta](how-to-assess.md) az Azure Migrate:Server Assessment eszközt.
- Győződjön meg arról, hogy beállítottegy [Azure Migrate-berendezést](migrate-appliance.md) a helyszíni gépek felderítéséhez. További információ a [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)vagy [fizikai kiszolgálók készülékének beállításáról.](how-to-set-up-appliance-physical.md) A készülék felderíti a helyszíni gépeket, és metaadatokat, teljesítményadatokat küld az Azure Migrate:Server Assessment szolgáltatásnak.
- A függőségi vizualizáció használatához egy [Log Analytics-munkaterületet](../azure-monitor/platform/manage-access.md) kell társítania egy Azure Migrate projekthez:
    - A munkaterületet csak az Azure Migrate berendezés beállítása után csatolhat, és az Azure Migrate projektben lévő gépek felderítése után.
    - Győződjön meg arról, hogy rendelkezik egy munkaterülettel az Azure Migrate projektet tartalmazó előfizetésben.
    - A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-Európa régióiban kell lennie. Más régiók munkaterületei nem társíthatók projekthez.
    - A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
    - Társíthat egy új vagy meglévő Log Analytics-munkaterületet egy Azure Migrate projekthez.
    - A munkaterületet akkor csatolja, amikor először állít be függőségi vizualizációt egy géphez. Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
    - A Log Analytics, a munkaterület társított Azure Áttelepítés van címkézve a migration projekt kulcs, és a projekt neve.

## <a name="associate-a-workspace"></a>Munkaterület társítása

1. Miután felderítette az értékelésre szánt gépeket, a **Kiszolgálók** > **Azure Áttelepítése: Kiszolgálófelmérés**területén kattintson **az Áttekintés gombra.**  
2. Az **Azure Migrate: Server Assessment (Az Azure Migrate: Server Assessment)** alkalmazásban kattintson **az Essentials**elemre.
3. Az **OMS-munkaterület területén**kattintson **a Konfigurációra van szüksége**lehetőségre.

     ![A Log Analytics munkaterületének konfigurálása](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Az **OMS-munkaterület konfigurálása**területen adja meg, hogy új munkaterületet szeretne-e létrehozni, vagy egy meglévőt szeretne használni.
    - A projekt-előfizetés áttelepítése összes munkaterületéből kiválaszthat egy meglévő munkaterületet.
    - A munkaterület társításához a Reader-hozzáférésszükséges a munkaterülethez.
5. Ha új munkaterületet hoz létre, válassza ki a kívánt helyet.

    ![Új munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

Minden elemezni kívánt gépen telepítse az ügyintézőket.

> [!NOTE]
> A System Center Operations Manager 2012 R2 vagy újabb verzió által felügyelt gépekesetében nem kell telepítenie az MMA-ügynököt. A Szolgáltatástérkép integrálható az Operations Managerrel. [Kövesse](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) az integrációs útmutatást.

1. Az **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson **a Felderített kiszolgálók**elemre.
2. Minden olyan géphez, amelyet függőségi vizualizációval szeretne elemezni, a **Függőségek** oszlopban kattintson az **Ügynök telepítéséhez**elemre.
3. A **Függőségek** lapon töltse le az MMA és a Függőségi ügynököt Windows vagy Linux rendszeren.
4. Az **MMA-ügynök konfigurálása**csoportban másolja a munkaterület-azonosítót és a kulcsot. Az MMA-ügynök telepítésekor ezekre van szükség.

    ![Az ügynökök telepítése](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Az MMA telepítése

Telepítse az MMA-t minden elemezni kívánt Windows vagy Linux rendszerű számítógépre.

### <a name="install-mma-on-a-windows-machine"></a>MmA telepítése Windows rendszerű számítógépre

Az ügynök telepítése Windows rendszerű számítógépen:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** lapon kattintson az **Elfogadom** a licenc elfogadásához elemre.
3. A **Célmappában**tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > **Tovább**.
4. Az **Ügynök beállítási beállításai párbeszédpanelen**válassza az **Azure Log Analytics** > **Next lehetőséget.**
5. Új Log Analytics-munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be a munkaterület-azonosítót és a portálról másolt kulcsot. Kattintson a **Tovább** gombra.

Az ügynököt a parancssorból vagy egy automatikus módszerrel, például a Configuration Manager vagy az [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196)segítségével telepítheti.
- [További információ](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) az MMA-ügynök telepítéséhez ezen módszerek használatáról.
- Az MMA-ügynök ezzel a [szkripttel](https://go.microsoft.com/fwlink/?linkid=2104394) is telepíthető.
- [További információ](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) az MMA által támogatott Windows operációs rendszerekről.

### <a name="install-mma-on-a-linux-machine"></a>MmA telepítése Linux rendszerű gépre

Az MMA telepítése Linux rendszerű gépre:

1. A megfelelő csomag (x86 vagy x64) átvitele linuxos számítógépre az scp/sftp használatával.
2. Telepítse a köteget a --install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További információ](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) az MMA Által támogatott Linux operációs rendszerek listájáról. 

## <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows-gépre való telepítéséhez kattintson duplán a telepítőfájlra, és kövesse a varázslót.
2. A függőségi ügynök Linux-gépre történő telepítéséhez telepítse root-ként a következő paranccsal:

    ```sh InstallDependencyAgent-Linux64.bin```

- [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) arról, hogyan használhatja a parancsfájlokat a függőségi ügynök telepítéséhez.
- [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) a függőségi ügynök által támogatott operációs rendszerekről.


## <a name="create-a-group-using-dependency-visualization"></a>Csoport létrehozása függőségi megjelenítéssel

Most hozzon létre egy csoportot értékelésre. 


> [!NOTE]
> Azok a csoportok, amelyek függőségeit szeretné vizualizálni, nem tartalmazhatnak 10-nél több gépet. Ha több mint 10 gépe van, ossza őket kisebb csoportokra.

1. Az **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson **a Felderített kiszolgálók**elemre.
2. A **Függőségek** oszlopban kattintson a Függőségek megtekintése minden áttekinteni kívánt számítógép **függőségének megtekintése** elemre.
3. A függőségi térképen a következőket láthatja:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a gépre és a gépről.
    - Azok a függő gépek, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A függőségi ügynökökkel rendelkező függő gépek külön mezőkként jelennek meg.
    - A gépbelsejében futó folyamatok. A folyamatok megtekintéséhez bontsa ki az egyes gépeket.
    - A számítógép tulajdonságai (beleértve az FQDN-t, az operációs rendszert, a MAC-címet). Kattintson az egyes gépdobozra a részletek megtekintéséhez.

4. A különböző időtartamok függőségeit az időidőtartamra kattintva vizsgálhatja meg az időtartomány címkéjén.
    - Alapértelmezés szerint a tartomány egy óra. 
    - Módosíthatja az időtartományt, vagy megadhatja a kezdési és befejezési dátumokat és az időtartamot.
    - Az időtartomány akár egy óra is lehet. Ha hosszabb tartományra van szüksége, az Azure Monitor használatával hosszabb ideig kérdezheti le a függő adatokat.

5. Miután azonosította a csoportosítani kívánt függő gépeket, a Ctrl+Click billentyűkombinációval jelöljön ki több gépet a térképen, majd kattintson a **Gépek csoportosítása**gombra.
6. Adja meg a csoport nevét.
7. Ellenőrizze, hogy a függő gépek et az Azure Migrate fedezte fel.

    - Ha egy függő gép nem fedezte fel az Azure Áttelepítés: Server Assessment, nem tudja hozzáadni a csoporthoz.
    - Számítógép hozzáadásához futtassa újra a felderítést, és ellenőrizze, hogy a rendszer felderítette-e a számítógépet.

8. Ha értékelést szeretne létrehozni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, ha új értékelést szeretne létrehozni a csoportszámára.
8. A csoport mentéséhez kattintson az **OK** gombra.

A csoport létrehozása után azt javasoljuk, hogy telepítse az ügynököket a csoport összes gépére, majd jelenítse meg a függőségeket az egész csoport számára.

## <a name="query-dependency-data-in-azure-monitor"></a>Lekérdezési függőségi adatok az Azure Monitorban

Lekérdezheti a szolgáltatáslestamalapzat által rögzített függőségi adatokat az Azure Migrate projekthez társított Log Analytics-munkaterületen. A Log Analytics az Azure Monitor naplólekérdezéseinek írására és futtatására szolgál.

- [További információ a](../azure-monitor/insights/service-map.md#log-analytics-records) Service Map-adatok keresésről a Log Analytics szolgáltatásban.
- A naplólekérdezések írásának [áttekintése](../azure-monitor/log-query/get-started-queries.md) a [Log Analytics](../azure-monitor/log-query/get-started-portal.md)szolgáltatásban.

Lekérdezés futtatása a függőségi adatokhoz a következőképpen:

1. Az ügynökök telepítése után nyissa meg a portált, és kattintson **az Áttekintés gombra.**
2. Az **Azure Migrate: Server Assessment (Az Azure Migrate: Server Assessment)** menüben kattintson **az Áttekintés gombra.** Kattintson a lefelé mutató nyílra az **Essentials kibontásához.**
3. Az **OMS-munkaterületen**kattintson a munkaterület nevére.
3. A Log Analytics munkaterületi lapján > **Általános**lapon kattintson a **Naplók gombra.**
4. Írja meg a lekérdezést, és kattintson a **Futtatás gombra.**

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány mintalekérdezés, amely a függőségi adatok kinyeréséhez használható.

- A lekérdezések módosításával kinyerheti az előnyben részesített adatpontokat.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) a függőségi adatrekordok teljes listáját.
- [Tekintse át](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) a további mintalekérdezéseket.

#### <a name="sample-review-inbound-connections"></a>Példa: Bejövő kapcsolatok áttekintése

Tekintse át a bejövő kapcsolatok at egy sor virtuális gépek.

- A kapcsolati metrikák (VMConnection) tábla rekordjai nem képviselnek egyéni fizikai hálózati kapcsolatokat.
- Több fizikai hálózati kapcsolat logikai kapcsolatba van csoportosítva.
- [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) a fizikai hálózati kapcsolat adatainak összesített ségéről a VMConnection szolgáltatásban.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Minta: Az elküldött és fogadott adatok összegzése

Ez a minta összefoglalja a gépek között bejövő kapcsolatokon küldött és fogadott adatok mennyiségét.

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

[Hozzon létre egy értékelést](how-to-create-assessment.md) egy csoporthoz.


