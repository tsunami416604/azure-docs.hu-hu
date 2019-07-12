---
title: Az Azure Migrate támogatási mátrix
description: Támogatási beállításainak és az Azure Migrate szolgáltatás korlátozásai összegzését tartalmazza.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811556"
---
# <a name="azure-migrate-support-matrix"></a>Az Azure Migrate támogatási mátrix

Használhatja a [Azure Migrate szolgáltatás](migrate-overview.md) segítségével mérheti fel, és a gépek áttelepítése a Microsoft Azure felhőbe. Ez a cikk általános támogatási beállításait és a korlátozások az Azure Migrate-forgatókönyveinek és a központi telepítések foglalja össze.


## <a name="azure-migrate-versions"></a>Az Azure Migrate-verziók

Nincsenek az Azure Migrate szolgáltatás két verziója:

- **Aktuális verzió**: Ezen verziójával új Azure Migrate projekteket hozhat létre, ismerje meg a helyszíni értékeli, és megszervezhető értékelések és áttelepítések. [További információk](whats-new.md#azure-migrate-new-version).
- **Előző verzió**: Az ügyfél az Azure Migrate (csak a helyszíni VMware virtuális gépek értékelése támogatta) előző verzióját használja most már a jelenlegi verzióját kell használni. Az előző verzió nem hozható létre új Azure Migrate, és hajtsa végre az új felderítések.

## <a name="supported-migration-scenarios"></a>Támogatott áttelepítési forgatókönyvek

A táblázat összefoglalja a támogatott áttelepítési forgatókönyvek.

**Üzembe helyezés** | **Részletek*** 
--- | --- 
**A helyszíni értékelése** | Felmérheti a helyszíni számítási feladatok és a VMware virtuális gépek és a Hyper-V virtuális gépeken futó adatokat. Mérje fel az Azure Migrate Server Assessment és a Microsoft Data Migration Assistant (DMA), valamint a külső eszközöket, amelyek tartalmazzák a Cloudamize Corent Tech és Turbonomic kiszolgáló használatával.
**Helyszíni áttelepítés az Azure-bA** | Számítási feladatok és az adatok fizikai kiszolgálókon vagy VMware virtuális gépeket, Hyper-V virtuális gépeket, és az AWS/GCP-példányokon, az Azure-bA migrálása. Áttelepítés az Azure Migrate Server Assessment és az Azure Database Migration Service (DMS), és csakúgy, mint például Carbonite és CorentTech külső eszközök használatával.

Adott eszköz támogatja a következőképpen foglalhatók össze.

**Eszköz** | **Értékelés és a Migrálás** | **Részletek**
--- | --- | ---
Az Azure Migrate Server Assessment | Értékelés | Próbálja ki a kiszolgáló értékelési [Hyper-V](tutorial-prepare-hyper-v.md) és [VMware](tutorial-prepare-vmware.md).
Cloudamize | Értékelés | [További információk](https://www.cloudamize.com/platform#tab-0).
CorentTech | Értékelés | [További információk](https://www.corenttech.com/).
A Turbonomic | Értékelés | [További információk](https://turbonomic.com/solutions/technologies/azure-cloud/).
Az Azure Migrate-kiszolgáló áttelepítése | Áttelepítés | Próbálja ki a kiszolgáló-áttelepítési [Hyper-V](tutorial-migrate-hyper-v.md) és [VMware](tutorial-migrate-vmware.md).
Carbonite | Áttelepítés | [További információk](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Áttelepítés | [További információk](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Az Azure Migrate-projekt

**Támogatás** | **Részletek**
--- | ---
Subscription | Egy adott előfizetés akkor is egyetlen Azure Migrate-projekt.
Azure-engedélyeket | Az Azure Migrate-projekt létrehozása az előfizetés közreműködő vagy tulajdonos engedélyek szükségesek.
VMware virtuális gépek  | Mérje fel, akár 35,000 VMware virtuális gépek, egyetlen projekt.
Hyper-V virtuális gépek | Mérje fel, legfeljebb 10 000 Hyper-V virtuális gépek, egyetlen projekt.

A projekt tartalmazhatnak VMware virtuális gépek és a Hyper-V virtuális gépek, egészen az értékelés korlátjáig.


## <a name="vmware-assessment-and-migration"></a>VMware-elemzésekhez és migráláshoz

[Felülvizsgálat](migrate-support-matrix-vmware.md) az Azure Migrate Server Assessment és a kiszolgáló áttelepítése a VMware virtuális gépek támogatási mátrixa.

## <a name="hyper-v-assessment-and-migration"></a>A Hyper-V elemzésekhez és migráláshoz

[Felülvizsgálat](migrate-support-matrix-hyper-v.md) az Azure Migrate Server Assessment és a kiszolgáló áttelepítése a Hyper-V virtuális gépek támogatási mátrixa.


## <a name="next-steps"></a>További lépések

- [Felmérheti a VMware virtuális gépek](tutorial-assess-vmware.md) az áttelepítéshez.
- [A Hyper-V virtuális gépek értékelése](tutorial-assess-hyper-v.md) az áttelepítéshez.

