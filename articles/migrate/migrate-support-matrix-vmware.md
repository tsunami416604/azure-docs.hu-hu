---
title: A VMware-felmérés támogatása az Azure Migrate ben
description: Ismerje meg a VMware VM-felmérés támogatását az Azure Áttelepítési kiszolgáló felmérésével.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 03d07adb6f19346901286bdae148f95e68290e4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336878"
---
# <a name="support-matrix-for-vmware-assessment"></a>A VMware értékelésének támogatási mátrixa 

Ez a cikk összegzi a VMware virtuális gépek értékelésének előfeltételeit és támogatási követelményeit az Azure-ba való migrálás előkészítéseként. Ha a VMware virtuális gépeket az Azure-ba szeretné áttelepíteni, tekintse át az [áttelepítéstámogatási mátrixot.](migrate-support-matrix-vmware-migration.md)

A fizikai kiszolgálókat az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel mérheti fel. Hozzon létre egy Azure Migrate projektet, majd adja hozzá az eszközt a projekthez. Az eszköz hozzáadása után telepíti az [Azure Migrate készüléket.](migrate-appliance.md) A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure-ba. A gépfelderítés után a felderített gépeket csoportokba gyűjti, és egy csoport ra vonatkozó értékelést futtat.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Projektkorlátok** | Azure-előfizetésben több projektet is létrehozhat.<br/><br/> Egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 VMware virtuális gépet is felfedezhet és értékelhet. A projekt tartalmazhat VMware virtuális gépek, fizikai kiszolgálók és a Hyper-V virtuális gépek, az egyes értékelési korlátokig.
**Felfedezés** | Az Azure Migrate készülék legfeljebb 10 000 vmware virtuális gépet fedezhet fel egy vCenter-kiszolgálón.
**Értékelés** | Egy csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékelés ben legfeljebb 35 000 virtuális gépet értékelhet.

[További információ](concepts-assessment-calculation.md) az értékelésekről.


## <a name="application-discovery"></a>Alkalmazásfelderítés

A gépek felderítése mellett az Azure Migrate: Server Assessment is felfedezheti a gépeken futó alkalmazásokat, szerepköröket és funkciókat. Az alkalmazáskészlet felderítése lehetővé teszi a helyszíni számítási feladatokhoz igazított áttelepítési útvonal azonosítását és megtervezését. 

**Támogatás** | **Részletek**
--- | ---
**Támogatott gépek** | Az alkalmazásfelderítés jelenleg csak a helyszíni VMware virtuális gépek en támogatott.
**Felfedezés** | Az alkalmazásfelderítés ügynök nélküli. A számítógép vendéghitelesítő adatait használja, és távolról éri el a wmi- és SSH-hívásokat használó gépeket.
**Virtuális gép támogatása** | Az alkalmazásfelderítés minden Windows- és Linux-verzióesetében támogatott.
**vCenter hitelesítő adatok** | Az alkalmazásfelderítéshez írásvédett hozzáféréssel rendelkező vCenter Server-fiókra van szükség, és a virtuális gépek hez engedélyezett jogosultságok > vendégműveletek.
**Virtuális gép hitelesítő adatai** | Az alkalmazásfelderítés jelenleg egy hitelesítő adat használatát támogatja az összes Windows-kiszolgálóhoz, és egy hitelesítő adatot az összes Linux-kiszolgálóhoz.<br/><br/> Hozzon létre egy vendég felhasználói fiókot a Windows virtuális gépekhez, és egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux-virtuális géphez.
**VMware eszközök** | A VMware-eszközöket telepíteni és futtatni kell a felderíteni kívánt virtuális gépeken. <br/> A VMware eszközverziónak 10.2.0-nál későbbinek kell lennie.
**Powershell** | A virtuális gépeken telepítve kell lennie a PowerShell 2.0-s vagy újabb verziójának.
**Port-hozzáférés** | A felderíteni kívánt virtuális gépeket futtató ESXi-állomásokon az Azure Migrate készüléknek képesnek kell lennie a 443-as TCP-porthoz való csatlakozásra.
**Korlátok** | Az alkalmazásfelderítéshez legfeljebb 10000 virtuális gépet fedezhet fel az egyes Azure Migrate-készülékeken.



## <a name="vmware-requirements"></a>VMware követelmények

**Vmware** | **Részletek**
--- | ---
**VMware virtuális gépek** | Az értékelés minden Windows és Linux operációs rendszeren támogatott.
**vCenter Server** | A felderítésre és felmérésre kívánt gépeket a vCenter Server 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verziójának kell kezelnie.
**Engedélyek (értékelés)** | vCenter Server írásvédett fiók.
**Engedélyek (alkalmazásfelderítés)** | VCenter Server-fiók írásvédett hozzáféréssel és a **virtuális gépekhez engedélyezve lévő**jogosultságokkal > vendégműveletek hez.
**Engedélyek (függőségi megjelenítés)** | Center Server fiók írásvédett hozzáféréssel és a **virtuális gépek** > **vendégműveletekhez engedélyezett jogosultságokkal.**


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

Az Azure Migrate az [Azure Migrate készüléket](migrate-appliance.md) használja felderítéshez és értékeléshez. A VMware-készülék egy OVA sablon használatával van telepítve, amelyet a vCenter Server alkalmazásba importálnak. 

- További információ a VMware [készülékkövetelményeiről.](migrate-appliance.md#appliance---vmware)
- Ismerje meg azokat az [URL-címeket, amelyekhez](migrate-appliance.md#url-access) a készüléknek szüksége van.

## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Készülék | Bejövő kapcsolatok a 3389-es TCP-porton, hogy lehetővé tegyék a távoli asztali kapcsolatot a készülékhez.<br/><br/> Bejövő kapcsolatok a 44368-as porton, hogy távolról is elérhesse a készülékkezelő alkalmazást az URL-cím használatával:```https://<appliance-ip-or-name>:44368``` <br/><br/>Kimenő kapcsolatok a 443-as porton (HTTPS), felderítési és teljesítménymetaadatok küldéséhez az Azure Migrate.
vCenter-kiszolgáló | Bejövő kapcsolatok a 443-as TCP-porton, hogy a készülék összegyűjthesse a konfigurációs és teljesítménymetaadatokat az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint a 443-as porton csatlakozik a vCenterhez. Ha a vCenter-kiszolgáló egy másik porton figyel, a felderítés beállításakor módosíthatja a portot.
ESXi-gazdagépek (alkalmazásfelderítés/ügynök nélküli függőségelemzés) | Ha azt szeretné, hogy [az alkalmazás felderítése](how-to-discover-applications.md) vagy [ügynök nélküli függőségi elemzés,](concepts-dependency-visualization.md#agentless-analysis)majd a készülék csatlakozik ESXi állomások A TCP-port 443, alkalmazások felderítésére, és fuss ügynök nélküli függőségi vizualizáció a virtuális gépeken.

## <a name="agentless-dependency-analysis-requirements"></a>Ügynök nélküli függőségelemzési követelmények

[A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja a helyszíni gépek közötti függőségeket, amelyeket fel szeretne mérni, és át szeretne térni az Azure-ba. A tábla összefoglalja az ügynök nélküli függőségelemzés beállításának követelményeit. 

**Követelmény** | **Részletek**
--- | --- 
**Telepítés előtt** | Rendelkeznie kell egy Azure Migrate projekt a helyén, az Azure Migrate: Server Assessment eszköz hozzá a projekthez.<br/><br/>  Függőségi vizualizációüzembe helyezése után egy Azure Migrate berendezés a helyszíni VMWare-gépek felderítéséhez.<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre első alkalommal projektet.<br/> [További információ arról, hogyan](how-to-assess.md) adhat hozzá értékelési eszközt egy meglévő projekthez.<br/> [Ismerje meg, hogyan](how-to-set-up-appliance-vmware.md) állíthatja be az Azure Migrate-készüléket a VMware virtuális gépek értékeléséhez.
**Virtuális gép támogatása** | Jelenleg csak a VMware virtuális gépek támogatottak.
**Windows rendszerű virtuális gépek** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bites).
**Windows-fiók** |  A függőségi elemzéshez az Azure Migrate berendezésnek helyi vagy tartományi rendszergazdai fiókra van szüksége a Windows virtuális gépek eléréséhez.
**Linux rendszerű virtuális gépek** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-fiók** | A függőségelemzéshez linuxos gépeken az Azure Migrate berendezésroot jogosultsággal rendelkező felhasználói fiókra van szüksége.<br/><br/> A felhasználói fióknak a /bin/netstat és /bin/ls fájlokra vonatkozó engedélyekre is szüksége van: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.
**Kötelező ügynökök** | Nincs szükség ügynökre az elemezni kívánt gépeken.
**VMware eszközök** | VMware Tools (később 10.2) kell telepíteni, és fut minden virtuális gép elemezni kívánt.
**vCenter Server hitelesítő adatok** | A függőségi vizualizációhoz írásvédett hozzáféréssel rendelkező vCenter Server-fiókra van szükség, és a virtuális gépek hez > vendégműveletekhez engedélyezett jogosultságokra van szükség. 
**Powershell** | A virtuális gépeken telepítve kell lennie a PowerShell 2.0-s vagy újabb verziójának.
**Port-hozzáférés** | Az elemezni kívánt virtuális gépeket futtató ESXi-állomásokon az Azure Migrate-készüléknek képesnek kell lennie a 443-as TCP-porthoz való csatlakozásra.

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynökalapú függőségelemzési követelmények

[A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja a helyszíni gépek közötti függőségeket, amelyeket fel szeretne mérni, és át szeretne térni az Azure-ba. A tábla összefoglalja az ügynökalapú függőségelemzés beállításának követelményeit. 

**Követelmény** | **Részletek** 
--- | --- 
**Telepítés előtt** | Rendelkeznie kell egy Azure Migrate projekt a helyén, az Azure Migrate: Server Assessment eszköz hozzá a projekthez.<br/><br/>  Függőségi vizualizációt üzembe helyez, miután létrehozott egy Azure Migrate-berendezést a helyszíni gépek felderítéséhez<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre első alkalommal projektet.<br/> [További információ arról, hogyan](how-to-assess.md) adhat hozzá értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be az Azure Migrate készüléket a [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | Függőségi megjelenítés nem érhető el az Azure Government.
**Log Analytics** | Az Azure Migrate a [Service Map-megoldást](../operations-management-suite/operations-management-suite-service-map.md) használja az [Azure Monitor naplóiban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Egy új vagy meglévő Log Analytics-munkaterületet társít egy Azure Migrate projekthez. Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-Európa régióiban kell lennie. Más régiók munkaterületei nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> A Log Analytics, a munkaterület társított Azure Áttelepítés van címkézve a migration projekt kulcs, és a projekt neve.
**Kötelező ügynökök** | Minden elemezni kívánt gépen telepítse a következő ügynököket:<br/><br/> A [Microsoft Monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell rájuk a Log Analytics átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics-munkaterület** | A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> Az Azure Migrate támogatja az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európa régióiban található munkaterületeket.<br/><br/>  A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | A Szolgáltatástérkép-megoldás nem számít fel díjat az első 180 napra vonatkozóan (attól a naptól számítva, amikor a Log Analytics-munkaterületet társítja az Azure Migrate projekthez)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Szolgáltatástérképen kívül bármilyen más megoldás használata esetén a Log Analytics [általános díjakat](https://azure.microsoft.com/pricing/details/log-analytics/) számít fel.<br/><br/> Az Azure Migrate projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlése után a Szolgáltatástérkép használata nem ingyenes, és minden csomópontnak a Log Analytics-munkaterület fizetett szintje szerint kell fizetnie.<br/><br/>Ha olyan projektjei vannak, amelyeket az Azure Migrate általános elérhetősége előtt hozott létre (GA– 2018. február 28.), előfordulhat, hogy további szolgáltatástérkép-díjak merültek fel. Annak érdekében, hogy a kifizetés csak 180 nap után, azt javasoljuk, hogy hozzon létre egy új projektet, mivel a meglévő munkaterületek előtt GA továbbra is felszámítható.
**Felügyelet** | Amikor ügynököket regisztrál a munkaterületre, az Azure Migrate projekt által biztosított azonosítót és kulcsot használja.<br/><br/> Használhatja a Log Analytics munkaterületet az Azure Migrate-en kívül.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje az Azure Migrate által létrehozott munkaterületet, hacsak nem törli az Azure Migrate projektet. Ha így tesz, a függőségi vizualizációs funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem csatlakoznak az internethez, telepítenie kell rájuk a Log Analytics-átjárót.


## <a name="next-steps"></a>További lépések

- [Tekintse át](best-practices-assessment.md) az értékelések készítésére vonatkozó bevált gyakorlatokat.
- [Felkészülés a VMware](tutorial-prepare-vmware.md) értékelésére.
