---
title: A Hyper-V értékelésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V felmérés támogatásával Azure Migrateokkal.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245810"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>A Hyper-V felmérés támogatási mátrixa

Ez a cikk a Hyper-V [rendszerű virtuális gépek Azure Migrate: kiszolgáló értékelésével](migrate-services-overview.md#azure-migrate-server-assessment-tool) való értékelésének támogatási beállításait és korlátozásait foglalja össze. Ha a Hyper-V virtuális gépek Azure-ba történő áttelepítésével kapcsolatos információkat keres, tekintse át az [áttelepítési támogatás mátrixát](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Áttekintés

Ha a helyszíni gépeket az Azure-ba való áttelepítésre szeretné felmérni, adja hozzá a Azure Migrate: Server Assessment eszközt egy Azure Migrate projekthez. Üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és konfigurációs és teljesítményadatokat küld az Azure-nak. A gép felderítése után a felderített gépeket csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok**| Akár 35 000 Hyper-V virtuális gép felderítése és értékelése egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat, akár az értékelési korlátokig is.
**Felfedezés** | A Azure Migrate készülék legfeljebb 5000 Hyper-V virtuális gépet képes felderíteni.<br/><br/> A készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.



## <a name="hyper-v-host-requirements"></a>A Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Gazdagép üzembe helyezése**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. |
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. <br/> Ha nem szeretne rendszergazdai engedélyeket rendelni, hozzon létre egy helyi vagy tartományi felhasználói fiókot, és adja hozzá a felhasználót a következő csoportokhoz: távfelügyeleti felhasználók, Hyper-V-rendszergazdák és Teljesítményfigyelő felhasználók. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2.<br/> A Windows Server 2012-t futtató Hyper-V-gazdagépeken található virtuális gépeket nem lehet értékelni. |
| **PowerShell távoli eljáráshívás**   | Minden gazdagépen engedélyezve kell lennie. |
| **Hyper-V replika**       | Ha a Hyper-V replikát használja (vagy több virtuális GÉPET ugyanazzal a virtuálisgép-azonosítóval), és az eredeti és a replikált virtuális gépeket is Azure Migrate használatával észleli, előfordulhat, hogy a Azure Migrate által létrehozott értékelés nem pontos. |


## <a name="hyper-v-vm-requirements"></a>Hyper-V virtuális gépekre vonatkozó követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
| **Integrációs szolgáltatások**       | Az operációs rendszer adatainak rögzítéséhez a [Hyper-V integrációs szolgáltatásoknak](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) olyan virtuális gépeken kell futniuk, amelyeket Ön értékelni fog. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A Hyper-V rendszerű gép egy Hyper-V virtuális gépen fut, és a Azure Portalból letöltött tömörített Hyper-V VHD-vel van üzembe helyezve. 

- A Hyper-V [készülékre vonatkozó követelmények](migrate-appliance.md#appliance---hyper-v) ismertetése.
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/> A 443 (HTTPS), 5671 és 5672 (AMQP) portok kimenő kapcsolatainak felderítési és teljesítménybeli metaadatok küldésére Azure Migrate.
**Hyper-V-gazdagép/-fürt** | A WinRM-portok 5985 (HTTP) és 5986 (HTTPS) bejövő kapcsolatai a Hyper-V virtuális gépek konfigurációjának és teljesítményének lekéréséhez CIM (CIM) munkamenet használatával.

## <a name="agent-based-dependency-visualization"></a>Ügynök-alapú függőségi vizualizáció

A függőségi [vizualizáció](concepts-dependency-visualization.md) segítséget nyújt a függőségek megjelenítéséhez a felmérni és áttelepíteni kívánt gépek között. Az ügynök-alapú vizualizációk, követelmények és korlátozások összegzését az alábbi táblázat tartalmazza


**Követelmény** | **Részletek**
--- | ---
**Üzembe helyezés** | A függőségi vizualizáció üzembe helyezése előtt rendelkeznie kell egy Azure Migrate-projekttel, és a Azure Migrate: Server Assessment Tool hozzáadva a projekthez. A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni gépek felderítése érdekében.<br/><br/> A függőségi vizualizáció nem érhető el Azure Governmentban.
**Szolgáltatástérkép** | Az ügynök-alapú függőségi vizualizáció a [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) megoldást használja [Azure monitor naplókban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Az üzembe helyezéshez új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez.
**Log Analytics-munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Díjak** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (a Log Analytics munkaterülethez a Azure Migrate projekttel társított naptól számítva).<br/><br/> 180 nap után a standard Log Analytics díjak érvényesek lesznek.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a rendszer standard Log Analytics díjat számít fel.<br/><br/> Ha törli a Azure Migrate projektet, a munkaterület nincs törölve. A projekt törlése után Service Map nem ingyenes, és az egyes csomópontok Log Analytics munkaterület fizetős szintjéhez hasonlóan lesznek felszámítva.
**Ügynökök** | Az ügynök-alapú függőségi vizualizációhoz két ügynököt kell telepíteni az elemezni kívánt gépeken.<br/><br/> - [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.


## <a name="next-steps"></a>További lépések

[Felkészülés a Hyper-V virtuális gépek felmérésére](tutorial-prepare-hyper-v.md)
