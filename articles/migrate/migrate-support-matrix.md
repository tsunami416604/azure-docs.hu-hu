---
title: Azure Migrate támogatási mátrix
description: Összefoglalja a Azure Migrate szolgáltatás támogatási beállításait és korlátozásait.
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: raynew
ms.openlocfilehash: fae5af9c8717604d71ec2bf2628ea25125d5b2d5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022718"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate támogatási mátrix

A [Azure Migrate szolgáltatással](./migrate-services-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a Azure Migrate forgatókönyvek és központi telepítések általános támogatási beállításait és korlátozásait foglalja össze.

## <a name="supported-assessmentmigration-scenarios"></a>Támogatott értékelési/áttelepítési forgatókönyvek

A táblázat összefoglalja a támogatott felderítési, felmérési és áttelepítési forgatókönyveket.

**Üzembe helyezés** | **Részletek** 
--- | --- 
**Felderítés** | Felderítheti a gépek metaadatait és a dinamikus teljesítményadatokat is.
**Alkalmazás – felderítés** | A VMware virtuális gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat is felderítheti. Ez a funkció jelenleg csak felderítésre korlátozódik. Az értékelés jelenleg a számítógép szintjén történik. Még nem kínálunk alkalmazás-, szerepkör-vagy szolgáltatás-alapú értékeléseket. 
**Értékelés** | A VMware virtuális gépeken, Hyper-V virtuális gépeken és fizikai kiszolgálókon futó helyszíni munkaterhelések és adatok értékelése. Azure Migrate Server Assessment, Microsoft Data Migration Assistant (DMA), valamint más eszközök és ISV-ajánlatok használatával történő Értékelés.
**Migrálás** | Áttelepítheti a fizikai kiszolgálókon, a VMware virtuális gépeken, a Hyper-V virtuális gépeken, a fizikai kiszolgálókon és a felhőalapú virtuális gépeken futó számítási feladatokat és az Azure-ba. Migrálás Azure Migrate Server Assessment és Azure Database Migration Service (DMS) használatával, valamint más eszközökkel és ISV-ajánlatokkal.

> [!NOTE]
> Az ISV-eszközök jelenleg nem tudnak adatküldést küldeni a Azure Government Azure Migrateba. Használhat integrált Microsoft-eszközöket, vagy önállóan is használhatja a partneri eszközöket.

## <a name="supported-tools"></a>Támogatott eszközök


Az eszközök adott támogatását a táblázat foglalja össze.

**Eszköz** | **Kiértékelés** | **Migrate** 
--- | --- | ---
Azure Migrate kiszolgáló értékelése | A [VMWare virtuális gépek](tutorial-prepare-vmware.md), a [Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálók](tutorial-prepare-physical.md)értékelése. |  Nem érhető el (NA)
Azure Migrate Server Migration | NA | Telepítse át a [VMWare virtuális gépeket](tutorial-migrate-vmware.md), a [Hyper-V virtuális gépeket](tutorial-migrate-hyper-v.md)és a [fizikai kiszolgálókat](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | VMware virtuális gépek, Hyper-V virtuális gépek, fizikai kiszolgálók, nyilvános Felhőbeli számítási feladatok migrálása. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése. | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése és migrálása. |  VMware virtuális gépek, Hyper-V virtuális gépek, fizikai kiszolgálók, nyilvános Felhőbeli számítási feladatok migrálása.
[Eszköz 42](https://go.microsoft.com/fwlink/?linkid=2097158) | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése.| NA
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | SQL Server adatbázisok értékelése. | NA
[DMS](../dms/dms-overview.md) | NA | SQL Server, Oracle, MySQL, PostgreSQL, MongoDB migrálása. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | A virtuális asztali infrastruktúra (VDI) felmérése | NA
[Movere](https://www.movere.io/) | A VMWare virtuális gépek, a Hyper-V virtuális gépek, a Xen virtuális gépek, a fizikai gépek, a munkaállomások (beleértve a VDI-ket is) felmérése | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | VMWare virtuális gépek, Hyper-V virtuális gépek, Xen virtuális gépek, KVM-alapú virtuális gépek, fizikai gépek, nyilvános Felhőbeli számítási feladatok migrálása 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése. | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Felméri a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, a nyilvános Felhőbeli számítási feladatokat és a SQL Server adatbázisokat. | NA
[WebApp Migration Assistant](https://appmigration.microsoft.com/) | Webes alkalmazások értékelése | Webes alkalmazások migrálása.


## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
Előfizetés | Egy előfizetéshez több Azure Migrate projekt is tartozhat.
Azure-engedélyek | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
VMware virtuális gépek  | Egyetlen projektben akár 35 000 VMware virtuális gépet is megvizsgálhat.
Hyper-V virtuális gépek    | Akár 35 000 Hyper-V virtuális gépet is kivizsgálhat egyetlen projektben.

A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.

## <a name="azure-permissions"></a>Azure-engedélyek

Ahhoz, hogy a Azure Migrate az Azure-ban működjön, a gépek felmérése és áttelepítése előtt szüksége lesz ezekre az engedélyekre.

**Feladat** | **Engedélyek** | **Részletek**
--- | --- | ---
Azure Migrate projekt létrehozása | Az Azure-fióknak rendelkeznie kell a projekt létrehozásához szükséges engedélyekkel. | A [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), a [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)vagy a [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-create-project)beállítása.
A Azure Migrate berendezés regisztrálása| A Azure Migrate egy könnyű [Azure Migrate berendezés](migrate-appliance.md) használatával értékeli a gépeket Azure Migrate kiszolgáló értékelésével, valamint a VMWare virtuális gépek [ügynök nélküli áttelepítését](server-migrate-overview.md) Azure Migrate kiszolgáló áttelepítésével. Ez a készülék felfedi a gépeket, és metaadatokat és teljesítményadatokat küld Azure Migrate.<br/><br/> A regisztráció során a regisztrálók (Microsoft. OffAzure, Microsoft. Migrál és Microsoft. kulcstartó) regisztrálva vannak a berendezésben kiválasztott előfizetésben, hogy az előfizetés működjön az erőforrás-szolgáltatóval. A regisztráláshoz közreműködői vagy tulajdonosi hozzáférésre van szükség az előfizetésben.<br/><br/> **VMware**– a bevezetéskor Azure Migrate két Azure Active Directory (Azure ad) alkalmazást hoz létre. Az első alkalmazás kommunikál a berendezés-ügynökök és a Azure Migrate szolgáltatás között. Az alkalmazás nem rendelkezik engedéllyel az Azure Resource Management-hívások létrehozásához, vagy RBAC hozzáférés az erőforrásokhoz. A második alkalmazás csak az ügynök nélküli VMware Migrálás felhasználói előfizetésében létrehozott Azure Key Vault fér hozzá. Ügynök nélküli Migrálás esetén Azure Migrate létrehoz egy Key Vault, amely a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezeli. A RBAC hozzáférése a Azure Key Vault (az ügyfél bérlője), ha a készülékről indított felderítést.<br/><br/> **Hyper-V**– az előkészítés során. Azure Migrate létrehoz egy Azure AD-alkalmazást. Az alkalmazás kommunikál a berendezés-ügynökök és a Azure Migrate szolgáltatás között. Az alkalmazás nem rendelkezik engedéllyel az Azure Resource Management-hívások létrehozásához, vagy RBAC hozzáférés az erőforrásokhoz. | A [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps), a [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-azure-ad-apps)vagy a [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)beállítása.
Key Vault létrehozása a VMware ügynök nélküli Migrálás számára | Ha a VMware virtuális gépeket ügynök nélküli Azure Migrate kiszolgáló áttelepítésével szeretné áttelepíteni, Azure Migrate létrehoz egy Key Vault, amely a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezeli. A tároló létrehozásához meg kell határoznia az engedélyeket (tulajdonos, közreműködő és felhasználói hozzáférés rendszergazdája) azon az erőforráson, amelyben a Azure Migrate projekt található. | Engedélyek [beállítása](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) .

## <a name="supported-geographies-public-cloud"></a>Támogatott földrajzi területek (nyilvános felhő)

Azure Migrate projektet a nyilvános felhőben számos földrajzi helyen hozhat létre.

- Habár ezekben a földrajzi területeken csak projekteket hozhat létre, a gépeket más célhelyekre is kielemezheti vagy áttelepítheti.
- A projekt földrajza csak a felderített metaadatok tárolására szolgál.
- Projekt létrehozásakor ki kell választania egy földrajzot. A projekt és a kapcsolódó erőforrások a földrajz egyik régiójában jönnek létre. A régiót a Azure Migrate szolgáltatás foglalta le.

**Földrajz** | **Metaadatok tárolási helye**
--- | ---
Ázsia és a Csendes-óceáni térség | Kelet-Ázsia vagy Délkelet-Ázsia
Ausztrália | Kelet-Ausztrália vagy Délkelet-Ausztrália
Brazília | Dél-Brazília
Kanada | Közép-Kanada vagy Kelet-Kanada
Európa | Észak-Európa vagy Nyugat-Európa
Franciaország | Közép-Franciaország
India | Közép-India vagy Dél-India
Japán |  Kelet-japán vagy Nyugat-Japán
Dél-Korea | Korea középső régiója vagy Dél-Korea
Egyesült Királyság | Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
Egyesült Államok | USA középső régiója vagy USA 2. nyugati régiója


## <a name="supported-geographies-azure-government"></a>Támogatott földrajzi területek (Azure Government)

**Feladat** | **Földrajz** | **Részletek**
--- | --- | ---
Projekt létrehozása | Egyesült Államok | A metaadatok tárolása US Gov Arizona, US Gov Virginia
Cél értékelése | Egyesült Államok | Célcsoportok: US Gov Arizona, US Gov Virginia, US Gov Texas
Cél replikációja | Egyesült Államok | Célcsoportok: US DoD – középső régió, US DoD – keleti régió, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>VMware Assessment és Migration

[Tekintse át](migrate-support-matrix-vmware.md) a Azure Migrate Server Assessment and Server áttelepítési támogatási mátrixot a VMWare virtuális gépekhez.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V felmérése és migrálása

[Tekintse át](migrate-support-matrix-hyper-v.md) a Hyper-V virtuális gépek Azure Migrate kiszolgáló-értékelési és kiszolgáló-áttelepítési támogatási mátrixát.



## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: ezzel a verzióval új Azure Migrate projekteket hozhat létre, felderítheti a helyszíni értékeléseket, és összehangolhatja az értékeléseket és áttelepítéseket. [További információ](whats-new.md).
- **Előző verzió**: a Azure Migrate korábbi verzióját használó ügyfelek esetében (csak a helyszíni VMWare virtuális gépek felmérése támogatott), most az aktuális verziót kell használnia. Az előző verzióban nem hozhat létre új Azure Migrate projekteket, és nem végezhet új felfedezéseket.

## <a name="next-steps"></a>További lépések

- A [VMWare virtuális gépek kiértékelése](tutorial-assess-vmware.md) áttelepítéshez.
- [Vizsgálja át a Hyper-V virtuális gépeket](tutorial-assess-hyper-v.md) az áttelepítéshez.
