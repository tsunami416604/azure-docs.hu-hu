---
title: VMware Assessment-támogatás a Azure Migrate
description: Ismerkedjen meg a VMware virtuális gépek felmérésének támogatásával Azure Migrate kiszolgáló értékelésével.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532204"
---
# <a name="support-matrix-for-vmware-assessment"></a>A VMware Assessment támogatási mátrixa 

Ez a cikk a VMware virtuális gépek Azure-ba történő áttelepítésére vonatkozó előfeltételeket és támogatási követelményeket foglalja össze a Azure Migrate: Server Assessment] (Migrálás-szolgáltatások-áttekintés. MD # Azure-migrál-Server-Assessment-Tool) eszköz használatával. Ha VMware virtuális gépeket szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-vmware-migration.md).

A VMware virtuális gépek felméréséhez létre kell hoznia egy Azure Migrate projektet, majd hozzá kell adnia a kiszolgáló-értékelési eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld az Azure-nak. A felderítés befejezése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat.<br/><br/> Egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 VMWare virtuális gépet is felderítheti és felbecsülheti. Egy projekt tartalmazhatja a fizikai kiszolgálókat és a Hyper-V virtuális gépeket is, az egyes rendszerekhez tartozó értékelési korlátokig.
**Felfedezés** | A Azure Migrate készülék legfeljebb 10 000 VMware virtuális gépet képes felderíteni egy vCenter Server.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.


## <a name="application-discovery"></a>Alkalmazásfelderítés

A gépek felderítése mellett a kiszolgálók értékelése a gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat is képes észlelni. Az alkalmazás leltárának felderítése lehetővé teszi a helyszíni munkaterhelésekhez igazított áttelepítési útvonal azonosítását és megtervezését. 

**Támogatás** | **Részletek**
--- | ---
**Támogatott gépek** | Az alkalmazások felderítése jelenleg csak a VMware virtuális gépek esetében támogatott.
**Felfedezés** | Az alkalmazás felderítése ügynök nélkül történik. A számítógép vendég hitelesítő adatait használja, és távolról fér hozzá a gépekhez a WMI-és SSH-hívásokkal.
**VM-támogatás** | Az App-Discovery minden Windows-és Linux-verzió esetében támogatott.
**vCenter hitelesítő adatai** | Az alkalmazás-felderítésnek egy vCenter Server fiókra van szüksége, amely csak olvasási hozzáféréssel rendelkezik, és Virtual Machines > vendég műveletekhez engedélyezett jogosultságokkal rendelkezik.
**Virtuális gép hitelesítő adatai** | Az alkalmazás-felderítés jelenleg egyetlen hitelesítő adat használatát támogatja az összes Windows-kiszolgálón, valamint egy hitelesítő adatot az összes Linux-kiszolgáló számára.<br/><br/> Létrehoz egy vendég felhasználói fiókot a Windows rendszerű virtuális gépekhez, valamint egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux rendszerű virtuális géphez.
**VMware-eszközök** | A felderíteni kívánt virtuális gépeken telepíteni és futtatni kell a VMware-eszközöket. <br/> A VMware-eszközök verziójának későbbinek kell lennie, mint 10.2.0.
**PowerShell** | A virtuális gépeknek telepítve kell lennie a PowerShell 2,0-es vagy újabb verziójával.
**Port-hozzáférés** | A felderíteni kívánt virtuális gépeket futtató ESXi-gazdagépeken a Azure Migrate készüléknek képesnek kell lennie csatlakozni a 443-es TCP-porthoz.
**Korlátok** | Az App-Discovery esetében akár 10000 virtuális gépet is felderítheti az egyes Azure Migrate berendezéseken.



## <a name="vmware-requirements"></a>VMware-követelmények

**VMware** | **Részletek**
--- | ---
**VMware virtuális gépek** | Az értékelés minden Windows-és Linux-operációs rendszeren támogatott.
**vCenter Server** | A felderítéshez és értékeléshez használni kívánt gépeket a 5,5, 6,0, 6,5 vagy 6,7 vCenter Server-es verzióval kell felügyelni.
**Engedélyek (Assessment)** | írásvédett fiók vCenter Server.
**Engedélyek (App-Discovery)** | vCenter Server fiók csak olvasási hozzáféréssel, és a virtuális gépek számára engedélyezett jogosultságokkal **> vendég műveletekkel**.
**Engedélyek (függőségi vizualizáció)** | Központ-kiszolgáló fiók írásvédett hozzáféréssel, valamint a **virtuális gépek** > **vendég műveleteihez**engedélyezett jogosultságok.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A készüléket VMWare virtuális gépként is telepítheti egy PETESEJT-sablonnal, amely a vCenter Serverba lett importálva, vagy egy [PowerShell-parancsfájl](deploy-appliance-script.md)használatával.

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.
- Azure Government a készüléket a szkript használatával kell telepítenie.


## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Berendezés | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával:```https://<appliance-ip-or-name>:44368``` <br/><br/>Kimenő kapcsolatok a 443-as porton (HTTPS), a felderítési és a teljesítménybeli metaadatok küldéséhez Azure Migrate.
vCenter-kiszolgáló | A 443-es TCP-porton bejövő kapcsolatok lehetővé teszik, hogy a berendezés konfigurációs és teljesítménybeli metaadatokat gyűjtsön az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor a felderítés beállításakor módosíthatja a portot.
ESXi-gazdagépek (alkalmazás-felderítés/ügynök nélküli függőség elemzése) | Ha az alkalmazások [felderítését](how-to-discover-applications.md) vagy az [ügynök nélküli függőségek elemzését](concepts-dependency-visualization.md#agentless-analysis)szeretné elvégezni, akkor a készülék a 443-as TCP-porton keresztül csatlakozik az ESXi-gazdagépekhez, hogy felderítse az alkalmazásokat, és futtassa az ügynök nélküli függőségi vizualizációt a virtuális gépeken.

## <a name="agentless-dependency-analysis-requirements"></a>Ügynök nélküli függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. A táblázat összefoglalja az ügynök nélküli függőségi elemzés beállításának követelményeit. 

**Követelmény** | **Részletek**
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy Azure Migrate-projekttel, és a kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni VMWare-gépek felderítése érdekében.<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> [Ismerje meg, hogyan](how-to-set-up-appliance-vmware.md) állíthatja be a Azure Migrate berendezést a VMWare virtuális gépek kiértékeléséhez.
**VM-támogatás** | Jelenleg csak a VMware virtuális gépek esetében támogatott.
**Windows rendszerű virtuális gépek** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bites).
**Windows-fiók** |  A függőségek elemzéséhez a Azure Migrate készüléknek helyi vagy tartományi rendszergazdai fiókra van szüksége a Windows rendszerű virtuális gépek eléréséhez.
**Linux rendszerű virtuális gépek** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-fiók** | A függőségek elemzéséhez Linux rendszerű gépeken a Azure Migrate berendezésnek rendszergazdai jogosultsággal rendelkező felhasználói fiókra van szüksége.<br/><br/> Másik lehetőségként a felhasználói fióknak szüksége van ezekre az engedélyekre a/bin/netstat és a/bin/ls fájlokra: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.
**Szükséges ügynökök** | Nem szükséges ügynök az elemezni kívánt gépeken.
**VMware-eszközök** | Az elemezni kívánt virtuális gépeken telepíteni és futtatni kell a VMware-eszközöket (a 10,2-nál újabb).
**hitelesítő adatok vCenter Server** | A függőségi vizualizációhoz egy vCenter Server fiókra van szükség, amely csak olvasási hozzáféréssel rendelkezik, és Virtual Machines > vendég műveletekhez engedélyezett jogosultságokkal rendelkezik. 
**PowerShell** | A virtuális gépeken telepítve kell lennie a PowerShell 2,0-es vagy újabb verziójának.
**Port-hozzáférés** | Az elemezni kívánt virtuális gépeket futtató ESXi-gazdagépeken a Azure Migrate készüléknek képesnek kell lennie csatlakozni a 443-es TCP-porthoz.


## <a name="agent-based-dependency-analysis-requirements"></a>Ügynök-alapú függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. 

**Követelmény** | **Részletek** 
--- | --- 
**Üzembe helyezés előtt** | Azure Migrate-projektet kell megadnia, és a Azure Migrate: kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizáció üzembe helyezése egy Azure Migrate berendezés beállítása után a helyszíni gépek felderítése érdekében<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Log Analytics** | A Azure Migrate a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldást használja [Azure monitor naplókban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Telepítse az alábbi ügynököket minden egyes elemezni kívánt gépen:<br/><br/> A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics-munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület Azure Migrate projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.


## <a name="next-steps"></a>További lépések

- [Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat.
- [Felkészülés a VMware](tutorial-prepare-vmware.md) -értékelésre.
