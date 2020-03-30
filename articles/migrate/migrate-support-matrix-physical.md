---
title: Fizikai kiszolgáló-felmérés támogatása az Azure Áttelepítésben
description: További információ a fizikai kiszolgálók értékelésének támogatásáról az Azure Migrate Server Assessment segítségével
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: f6c70ac2517a29497f4f11073e4b16067bab9576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336894"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Támogatási mátrix a fizikai kiszolgáló konkretinációs rendszeréhez 

Ez a cikk összegzi a fizikai kiszolgálók azure-ba való áttelepítésre való felkészüléshez szükséges előfeltételeit és támogatási követelményeit. Ha fizikai kiszolgálókat szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítéstámogatási mátrixot.](migrate-support-matrix-physical-migration.md)


A fizikai kiszolgálókat az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel mérheti fel. Hozzon létre egy Azure Migrate projektet, majd adja hozzá az eszközt a projekthez. Az eszköz hozzáadása után telepíti az [Azure Migrate készüléket.](migrate-appliance.md) A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure-ba. A gépfelderítés után a felderített gépeket csoportokba gyűjti, és egy csoport ra vonatkozó értékelést futtat.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési határértékek** | Egyetlen [Azure Migrate projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 fizikai kiszolgálót is felderíthet és értékelhet.
**Projektkorlátok** | Azure-előfizetésben több projektet is létrehozhat. A fizikai kiszolgálók mellett a projekt tartalmazhat VMware virtuális gépeket és hyper-v vm-eket is, az egyes értékelési korlátokig.
**Felfedezés** | Az Azure Migrate készülék legfeljebb 250 fizikai kiszolgálót fedezhet fel.
**Értékelés** | Egy csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékelés alatt akár 35 000 gépet is felmérhet.

[További információ](concepts-assessment-calculation.md) az értékelésekről.

## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló telepítése**       | A fizikai kiszolgáló lehet önálló, vagy fürtben van telepítve. |
| **Engedélyek**           | **Windows:** Helyi vagy tartományi felhasználói fiókra van szüksége az összes felderíteni kívánt Windows-kiszolgálón. A felhasználói fiókot hozzá kell adni a következő csoportokhoz: Távoli asztali felhasználók, Teljesítményfigyelő-felhasználók és Teljesítménynapló-felhasználók. <br/><br/> **Linux:** Szüksége van egy gyökérfiókra a Linux-kiszolgálókon, amelyeket fel szeretne deríteni. |
| **Operációs rendszer** | A Windows Server 2003 és a SUSE Linux kivételével minden [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operációs rendszer támogatott.|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

Az Azure Migrate az [Azure Migrate készüléket](migrate-appliance.md) használja felderítéshez és értékeléshez. A fizikai kiszolgálók hoz a készülék futtatható virtuális gépen vagy fizikai gépen. Állítsa be egy PowerShell-parancsfájl használatával, amely letölti az Azure Portalról.

- További információ a fizikai kiszolgálók [ra vonatkozó berendezésekre vonatkozó követelményekről.](migrate-appliance.md#appliance---physical)
- Ismerje meg azokat az [URL-címeket, amelyekhez](migrate-appliance.md#url-access) a készüléknek szüksége van.

## <a name="port-access"></a>Port-hozzáférés

Az alábbi táblázat összefoglalja a port értékelési követelményeit.

**Eszköz** | **Kapcsolat**
--- | ---
**Készülék** | Bejövő kapcsolatok a 3389-es TCP-porton, hogy lehetővé tegyék a távoli asztali kapcsolatokat a készülékhez.<br/><br/> Bejövő kapcsolatok a 44368-as porton, hogy távolról is elérhesse a készülékkezelő alkalmazást az URL-cím használatával:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as (HTTPS) portokon, hogy felderítési és teljesítménymetaadatokat küldjön az Azure Migrate.Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** Bejövő kapcsolatok az 5985 (HTTP) és az 5986 (HTTPS) rendszerű WinRM-portokon a konfigurációs és teljesítménymetaadatok Windows-kiszolgálókról történő lekérése érdekében. <br/><br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (UDP), a konfiguráció és a teljesítmény metaadatainak lekérése linuxos kiszolgálókról. |

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
**VMware eszközök** |  VMware Tools (később 10.2) kell telepíteni, és fut minden virtuális gép elemezni kívánt.
**vCenter Server** |  A függőségi vizualizációhoz írásvédett hozzáféréssel rendelkező vCenter Server-fiókra van szükség, és a virtuális gépek hez > vendégműveletekhez engedélyezett jogosultságokra van szükség. **ESXi-gazdagépek:** Az elemezni kívánt virtuális gépeket futtató ESXi-állomásokon az Azure Migrate-készüléknek képesnek kell lennie a 443-as TCP-porthoz való csatlakozásra.

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

[Felkészülés a fizikai kiszolgáló értékelésére](tutorial-prepare-physical.md).
