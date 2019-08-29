---
title: Azure Migrate támogatási mátrix
description: Összefoglalja a Azure Migrate szolgáltatás támogatási beállításait és korlátozásait.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: raynew
ms.openlocfilehash: 99ef5c8a4a4291dd1b9d047da9006623dacd0b53
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147868"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate támogatási mátrix

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a Azure Migrate forgatókönyvek és központi telepítések általános támogatási beállításait és korlátozásait foglalja össze.


## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: Ezzel a verzióval új Azure Migrate projekteket hozhat létre, felderítheti a helyszíni értékeléseket, és összehangolhatja az értékeléseket és áttelepítéseket. [További információk](whats-new.md#azure-migrate-new-version).
- **Előző verzió**: Az Azure Migrate korábbi verzióját használó ügyfelek esetében (csak a helyszíni VMware virtuális gépek felmérése támogatott), most a jelenlegi verziót kell használnia. Az előző verzióban nem hozhat létre új Azure Migrate projekteket, és nem végezhet új felfedezéseket.

## <a name="supported-migration-scenarios"></a>Támogatott áttelepítési forgatókönyvek

A táblázat összefoglalja a támogatott áttelepítési forgatókönyveket.

**Üzembe helyezés** | **Részletek** 
--- | --- 
**Helyszíni Értékelés** | A VMware virtuális gépeken és a Hyper-V virtuális gépeken futó helyszíni munkaterhelések és adatok értékelése. Értékelje Azure Migrate Server Assessment és Microsoft Data Migration Assistant (DMA) használatát, valamint a Cloudamize, a kölcsönzési technológiát és a Turbonomic kiszolgálót is tartalmazó külső eszközöket.
**Helyszíni áttelepítés az Azure-ba** | Áttelepítheti a fizikai kiszolgálókon, a VMware virtuális gépeken, a Hyper-V virtuális gépeken, a fizikai kiszolgálókon és a felhőalapú virtuális gépeken futó számítási feladatokat és az Azure-ba. Migrálás Azure Migrate Server Assessment és Azure Database Migration Service (DMS) használatával, valamint a harmadik féltől származó, a Carbonite-és a CorentTech tartalmazó eszközök használatával.

Az eszközök adott támogatását az alábbiak szerint összegzi a rendszer.

**Eszköz** | **Értékelés/áttelepítés** | **Részletek**
--- | --- | ---
Azure Migrate kiszolgáló értékelése | Értékelés | Próbálja ki a [Hyper-V](tutorial-prepare-hyper-v.md) és a [VMware](tutorial-prepare-vmware.md)kiszolgáló értékelését.
Cloudamize | Értékelés | [További információk](https://www.cloudamize.com/platform#tab-0).
CorentTech | Értékelés | [További információk](https://www.corenttech.com/).
Turbonomic | Értékelés | [További információk](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate kiszolgáló áttelepítése | Áttelepítés | Próbálja ki a kiszolgáló áttelepítését a [Hyper-V](tutorial-migrate-hyper-v.md) és a [VMware](tutorial-migrate-vmware.md)rendszerhez.
Carbonite | Áttelepítés | [További információk](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Áttelepítés | [További információk](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
Subscription | Egy előfizetéshez több Azure Migrate projekt is tartozhat.
Azure-engedélyek | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
VMware virtuális gépek  | Egyetlen projektben akár 35 000 VMware virtuális gépet is megvizsgálhat.
Hyper-V virtuális gépek | Akár 10 000 Hyper-V virtuális gépet is kivizsgálhat egyetlen projektben.

A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.


## <a name="vmware-assessment-and-migration"></a>VMware Assessment és Migration

[Tekintse át](migrate-support-matrix-vmware.md) a Azure Migrate Server Assessment and Server áttelepítési támogatási mátrixot a VMWare virtuális gépekhez.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V felmérése és migrálása

[Tekintse át](migrate-support-matrix-hyper-v.md) a Hyper-V virtuális gépek Azure Migrate kiszolgáló-értékelési és kiszolgáló-áttelepítési támogatási mátrixát.


## <a name="next-steps"></a>További lépések

- A [VMWare virtuális gépek](tutorial-assess-vmware.md) kiértékelése áttelepítéshez.
- [Vizsgálja át a Hyper-V virtuális gépeket](tutorial-assess-hyper-v.md) az áttelepítéshez.

