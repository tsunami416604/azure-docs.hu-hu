---
title: A Hyper-V-felmérés támogatása az Azure Migrate szolgáltatásban
description: Tudnivalók a Hyper-V-felmérés támogatásáról az Azure Áttelepítési kiszolgáló felmérésével
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: e8a698b110f19dff593a93a41e9d6f20eb80cdb0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389001"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Támogatási mátrix a Hyper-V felméréshez

Ez a cikk összegzi az előfeltételeket és a támogatási követelményeket, amikor az [Azure Áttelepítés:Kiszolgáló értékelés](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz használatával értékeli a Hyper-V virtuális gépek az Azure-ba való áttelepítéshez. Ha a Hyper-V virtuális gépeket az Azure-ba szeretné áttelepíteni, tekintse át az [áttelepítéstámogatási mátrixot.](migrate-support-matrix-hyper-v-migration.md)

Hyper-V VM-felmérés beállítása, hozzon létre egy Azure Áttelepítési projektet, és adja hozzá a kiszolgálóértékelés eszközt a projekthez. Az eszköz hozzáadása után telepíti az [Azure Migrate készüléket.](migrate-appliance.md) A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure-ba. A felderítés befejezése után a felderített gépeket csoportokba gyűjti, és egy csoport ra vonatkozó értékelést futtat.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési határértékek** | Egyetlen [Azure Migrate-projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 Hyper-V virtuális gépet is felfedezhet és értékelhet.
**Projektkorlátok** | Azure-előfizetésben több projektet is létrehozhat. A Hyper-V virtuális gépek mellett a projekt virtuális gépeket és fizikai kiszolgálókat is tartalmazhat, az egyes értékelési korlátokig.
**Felfedezés** | Az Azure Migrate készülék legfeljebb 5000 Hyper-V virtuális gépet fedezhet fel.<br/><br/> A készülék legfeljebb 300 Hyper-V állomáshoz tud csatlakozni.
**Értékelés** | Egy csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egy csoport egyetlen felmérésében legfeljebb 35 000 virtuális gépet értékelhet.

[További információ](concepts-assessment-calculation.md) az értékelésekről.



## <a name="hyper-v-host-requirements"></a>Hyper-V gazdagép követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Hyper-V gazdagép**       | A Hyper-V állomás lehet önálló, vagy fürtben telepített.<br/><br/> A Hyper-V állomás futtathatja a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszert.<br/> A Windows Server 2012-t futtató Hyper-V-gazdagépeken található virtuális gépeket nem lehet értékelni.
| **Engedélyek**           | Rendszergazdai engedélyekre van szükség a Hyper-V állomáson. <br/> Ha nem szeretne rendszergazdai engedélyeket hozzárendelni, hozzon létre egy helyi vagy tartományi felhasználói fiókot, és adja hozzá a felhasználói fiókot ezekhez a csoportokhoz– Távfelügyeleti felhasználók, Hyper-V rendszergazdák és Teljesítményfigyelő felhasználók. |
| **PowerShell távoli eljáráshívás**   | [A PowerShell-mutatót](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) minden Hyper-V gazdagépen engedélyezni kell. |
| **Hyper-V replika**       | Ha hyper-V replica (vagy több virtuális gép azonos virtuális gépazonosítókkal), és az Azure Migrate használatával felderíti mind az eredeti, mind a replikált virtuális gépeket, előfordulhat, hogy az Azure Migrate által létrehozott értékelés nem pontos. |


## <a name="hyper-v-vm-requirements"></a>Hyper-V VM követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Minden [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operációs rendszer. |
| **Integrációs szolgáltatások**       | [A Hyper-V integrációs szolgáltatásoknak](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) az Ön által felmérhető virtuális gépeken kell futniuk az operációs rendszer adatainak rögzítéséhez. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

Az Azure Migrate az [Azure Migrate készüléket](migrate-appliance.md) használja felderítéshez és értékeléshez. Az eszköz üzembe helyezhető egy tömörített Hyper-V vHD használatával, amelyet a portálról tölt le, vagy egy [PowerShell-parancsfájl](deploy-appliance-script.md)használatával.

- További információ a Hyper-V [készülékre vonatkozó követelményekről.](migrate-appliance.md#appliance---hyper-v)
- Ismerje meg azokat az [URL-címeket, amelyekhez](migrate-appliance.md#url-access) a készüléknek szüksége van.

## <a name="port-access"></a>Port-hozzáférés

Az alábbi táblázat összefoglalja a port értékelési követelményeit.

**Eszköz** | **Kapcsolat**
--- | ---
**Készülék** | Bejövő kapcsolatok a 3389-es TCP-porton, hogy lehetővé tegyék a távoli asztali kapcsolatot a készülékhez.<br/><br/> Bejövő kapcsolatok a 44368-as porton, hogy távolról is elérhesse a készülékkezelő alkalmazást az URL-cím használatával:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as (HTTPS) portokon, hogy felderítési és teljesítménymetaadatokat küldjön az Azure Migrate.Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Hyper-V állomás/fürt** | Bejövő kapcsolatok a WinRM-portok 5985 (HTTP) és 5986 (HTTPS), a metaadatok és a teljesítmény adatok lekérése a Hyper-V virtuális gépek egy közös információs modell (CIM) munkamenet.

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynökalapú függőségelemzési követelmények

[A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja a helyszíni gépek közötti függőségeket, amelyeket fel szeretne mérni, és át szeretne térni az Azure-ba. A tábla összefoglalja az ügynökalapú függőségelemzés beállításának követelményeit. A Hyper-V jelenleg csak az ügynökalapú függőségi megjelenítést támogatja. 

**Követelmény** | **Részletek** 
--- | --- 
**Telepítés előtt** | Rendelkeznie kell egy Azure Áttelepítési projekt a helyén, a kiszolgálóértékelése eszköz hozzá a projekthez.<br/><br/>  Függőségi vizualizációt üzembe helyez, miután létrehozott egy Azure Migrate-berendezést a helyszíni gépek felderítéséhez<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre első alkalommal projektet.<br/> [További információ arról, hogyan](how-to-assess.md) adhat hozzá értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be az Azure Migrate-készüléket a [Hyper-V virtuális gépek](how-to-set-up-appliance-hyper-v.md)értékeléséhez.
**Azure Government** | Függőségi megjelenítés nem érhető el az Azure Government.
**Log Analytics** | Az Azure Migrate a [Service Map-megoldást](../operations-management-suite/operations-management-suite-service-map.md) használja az [Azure Monitor naplóiban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Egy új vagy meglévő Log Analytics-munkaterületet társít egy Azure Migrate projekthez. Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-Európa régióiban kell lennie. Más régiók munkaterületei nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> A Log Analytics, a munkaterület társított Azure Áttelepítés van címkézve a migration projekt kulcs, és a projekt neve.
**Kötelező ügynökök** | Minden elemezni kívánt gépen telepítse a következő ügynököket:<br/><br/> A [Microsoft Monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell rájuk a Log Analytics átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics-munkaterület** | A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> Az Azure Migrate támogatja az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európa régióiban található munkaterületeket.<br/><br/>  A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | A Szolgáltatástérkép-megoldás nem számít fel díjat az első 180 napra vonatkozóan (attól a naptól számítva, amikor a Log Analytics-munkaterületet társítja az Azure Migrate projekthez)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Szolgáltatástérképen kívül bármilyen más megoldás használata esetén a Log Analytics [általános díjakat](https://azure.microsoft.com/pricing/details/log-analytics/) számít fel.<br/><br/> Az Azure Migrate projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlése után a Szolgáltatástérkép használata nem ingyenes, és minden csomópontnak a Log Analytics-munkaterület fizetett szintje szerint kell fizetnie.<br/><br/>Ha olyan projektjei vannak, amelyeket az Azure Migrate általános elérhetősége előtt hozott létre (GA– 2018. február 28.), előfordulhat, hogy további szolgáltatástérkép-díjak merültek fel. Annak érdekében, hogy a kifizetés csak 180 nap után, azt javasoljuk, hogy hozzon létre egy új projektet, mivel a meglévő munkaterületek előtt GA továbbra is felszámítható.
**Felügyelet** | Amikor ügynököket regisztrál a munkaterületre, az Azure Migrate projekt által biztosított azonosítót és kulcsot használja.<br/><br/> Használhatja a Log Analytics munkaterületet az Azure Migrate-en kívül.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje az Azure Migrate által létrehozott munkaterületet, hacsak nem törli az Azure Migrate projektet. Ha így tesz, a függőségi vizualizációs funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem csatlakoznak az internethez, telepítenie kell rájuk a Log Analytics-átjárót.

## <a name="next-steps"></a>További lépések

[Felkészülés a Hyper-V VM értékelésére](tutorial-prepare-hyper-v.md)
