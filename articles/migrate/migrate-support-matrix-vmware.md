---
title: VMware Assessment-támogatás a Azure Migrate
description: Ismerkedjen meg az Azure Migrate VMware Assessment támogatásával.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: b887508fb8e422bd83aa9d13e42085d7a6bd2283
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269587"
---
# <a name="support-matrix-for-vmware-assessment"></a>A VMware Assessment támogatási mátrixa 

Ez a cikk a VMware virtuális gépek [Azure Migrate: kiszolgáló értékelésével](migrate-services-overview.md#azure-migrate-server-migration-tool)való értékelésének támogatási beállításait és korlátozásait foglalja össze. Ha a VMware virtuális gépek Azure-ba történő áttelepítésével kapcsolatos információkat keres, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Áttekintés

Ha a helyszíni gépeket az Azure-ba való áttelepítésre szeretné felmérni, adja hozzá a Azure Migrate: Server Assessment eszközt egy Azure Migrate projekthez. Üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és konfigurációs és teljesítményadatokat küld az Azure-nak. A gép felderítése után a felderített gépeket csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok**| Akár 35 000 VMware virtuális gép felderítése és értékelése egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat, akár az értékelési korlátokig is.
**Felfedezés** | A Azure Migrate készülék legfeljebb 10 000 VMware virtuális gépet képes felderíteni egy vCenter Server.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.


## <a name="application-discovery"></a>Alkalmazásfelderítés

A gépek felderítése mellett Azure Migrate: a kiszolgáló értékelése a gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat is képes észlelni. Az alkalmazás leltárának felderítése lehetővé teszi a helyszíni munkaterhelésekhez igazított áttelepítési útvonal azonosítását és megtervezését. 

**Támogatás** | **Részletek**
--- | ---
**Felfedezés** | A felderítés ügynök nélkül, a számítógép vendég hitelesítő adataival és a számítógépek távoli elérésével a WMI-és SSH-hívásokkal.
**Támogatott gépek** | Helyszíni VMware virtuális gépek.
**Gépi operációs rendszer** | Minden Windows-és Linux-verzió.
**vCenter hitelesítő adatai** | Egy vCenter Server fiók írásvédett hozzáféréssel, valamint a Virtual Machines > vendég műveletekhez engedélyezett jogosultságokkal.
**Virtuális gép hitelesítő adatai** | A jelenleg egyetlen hitelesítő adat használatát támogatja az összes Windows-kiszolgálón, valamint egy hitelesítő adatot az összes Linux-kiszolgáló számára.<br/><br/> Létrehoz egy vendég felhasználói fiókot a Windows rendszerű virtuális gépekhez, valamint egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux rendszerű virtuális géphez.
**VMware-eszközök** | A felderíteni kívánt virtuális gépeken telepíteni és futtatni kell a VMware-eszközöket. <br/> Ha a VMware-eszközök verziószáma 9,10 – 10.2.0, győződjön meg róla, hogy frissítenie kell a 10.2.0-re.
**PowerShell** | A virtuális gépeknek a 2,0-es vagy újabb verziójú PowerShell-lel kell rendelkezniük
**Port-hozzáférés** | A felderíteni kívánt virtuális gépeket futtató ESXi-gazdagépeken a Azure Migrate készüléknek képesnek kell lennie csatlakozni a 443-es TCP-porthoz.
**Korlátok** | Az App-Discovery esetében akár 10000 is felderíthető. 

## <a name="vmware-requirements"></a>VMware-követelmények

**VMware** | **Részletek**
--- | ---
**vCenter Server** | A felderítéshez és értékeléshez használni kívánt gépeket a 5,5, 6,0, 6,5 vagy 6,7 vCenter Server-es verzióval kell felügyelni.
**Engedélyek (Assessment)** | írásvédett fiók vCenter Server.
**Engedélyek (App-Discovery)** | vCenter Server fiók csak olvasási hozzáféréssel, és a virtuális gépek számára engedélyezett jogosultságokkal **> vendég műveletekkel**.
**Engedélyek (függőségi vizualizáció)** | A központi kiszolgáló fiók csak olvasási hozzáféréssel, valamint a **virtuális gépekhez** > **vendég műveletekhez**engedélyezett jogosultságokkal.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A VMware rendszerhez készült készülék a vCenter Serverba importált PETESEJT-sablonnal lett üzembe helyezve. 

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Berendezés | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ```https://<appliance-ip-or-name>:44368``` <br/><br/>A 443 (HTTPS), 5671 és 5672 (AMQP) porton kimenő kapcsolatok, amelyek felderítési és teljesítménybeli metaadatok küldését Azure Migrate.
vCenter-kiszolgáló | A 443-es TCP-porton bejövő kapcsolatok lehetővé teszik, hogy a berendezés konfigurációs és teljesítménybeli metaadatokat gyűjtsön az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor a felderítés beállításakor módosíthatja a portot.
ESXi-gazdagépek | **Csak az [alkalmazások felderítéséhez](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) és az [ügynök nélküli függőségek megjelenítéséhez](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization) szükséges** <br/><br/> A készülék a 443-as TCP-porton található ESXi-gazdagépekhez csatlakozik, és ügynök nélküli függőségi vizualizációkat futtat a gazdagépeken futó virtuális gépeken.

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


## <a name="agentless-dependency-visualization"></a>Ügynök nélküli függőségi vizualizáció

Ez az opció jelenleg csak előzetes verzióban érhető el. [További információk](how-to-create-group-machine-dependencies-agentless.md). A követelményeket a következő táblázat foglalja össze.

**Követelmény** | **Részletek**
--- | ---
**Üzembe helyezés** | A függőségi vizualizáció üzembe helyezése előtt rendelkeznie kell egy Azure Migrate-projekttel, és a Azure Migrate: Server Assessment Tool hozzáadva a projekthez. A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni gépek felderítése érdekében.
**VM-támogatás** | Jelenleg csak a VMware virtuális gépek esetében támogatott.
**Windows rendszerű virtuális gépek** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bites)
**Linux rendszerű virtuális gépek** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Windows-fiók** |  A vizualizációnak helyi vagy tartományi rendszergazdai fiókra van szüksége.
**Linux-fiók** | A vizualizációhoz rendszergazdai jogosultsággal rendelkező felhasználói fiókra van szükség.<br/><br/> Másik lehetőségként a felhasználói fióknak szüksége van ezekre az engedélyekre a/bin/netstat és a/bin/ls fájlokra: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.
**VM-ügynökök** | Nincs szükség ügynökre a virtuális gépeken.
**VMware-eszközök** | A VMware-eszközöket az elemezni kívánt virtuális gépeken kell telepíteni és futtatni. <br/> Ha a VMware-eszközök verziószáma 9,10 – 10.2.0, győződjön meg róla, hogy frissítenie kell a 10.2.0-re.
**PowerShell** | A virtuális gépeknek a 2,0-es vagy újabb verziójú PowerShell-lel kell rendelkezniük
**vCenter hitelesítő adatai** | Egy vCenter Server fiók írásvédett hozzáféréssel, valamint a Virtual Machines > vendég műveletekhez engedélyezett jogosultságokkal.
**Port-hozzáférés** | Az elemezni kívánt virtuális gépeket futtató ESXi-gazdagépeken a Azure Migrate készüléknek képesnek kell lennie csatlakozni a 443-es TCP-porthoz.



## <a name="next-steps"></a>Következő lépések

- [Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat.
- [Felkészülés a VMware](tutorial-prepare-vmware.md) -értékelésre.
