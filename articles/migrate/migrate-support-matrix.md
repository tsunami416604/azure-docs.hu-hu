---
title: Azure Migrate támogatási mátrix
description: Összefoglalja a Azure Migrate szolgáltatás támogatási beállításait és korlátozásait.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c29b80f30b024d34ec4e8f65e51b59fc70e8f93
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846564"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate támogatási mátrix

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a Azure Migrate forgatókönyvek és központi telepítések általános támogatási beállításait és korlátozásait foglalja össze.

## <a name="supported-assessmentmigration-scenarios"></a>Támogatott értékelési/áttelepítési forgatókönyvek

A táblázat összefoglalja a támogatott felderítési, felmérési és áttelepítési forgatókönyveket.

**Üzembe helyezés** | **Részletek** 
--- | --- 
**Alkalmazás-specifikus felderítés** | A VMware virtuális gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat is felderítheti. Ez a funkció jelenleg csak felderítésre korlátozódik. Az értékelés jelenleg a számítógép szintjén történik. Még nem kínálunk alkalmazás-, szerepkör-vagy szolgáltatás-specifikus értékelést. 
**Helyszíni Értékelés** | A VMware virtuális gépeken, Hyper-V virtuális gépeken és fizikai kiszolgálókon futó helyszíni munkaterhelések és adatok értékelése. Azure Migrate Server Assessment és Microsoft Data Migration Assistant (DMA), valamint más eszközök és ISV-ajánlatok használatával történő Értékelés.
**Helyszíni áttelepítés az Azure-ba** | Áttelepítheti a fizikai kiszolgálókon, a VMware virtuális gépeken, a Hyper-V virtuális gépeken, a fizikai kiszolgálókon és a felhőalapú virtuális gépeken futó számítási feladatokat és az Azure-ba. Migrálás Azure Migrate Server Assessment és Azure Database Migration Service (DMS) használatával, valamint más eszközökkel és ISV-ajánlatokkal.


## <a name="supported-tools"></a>Támogatott eszközök

Az eszközök adott támogatását a táblázat foglalja össze.

**Eszköz** | **Felmérése** | **Migrate (Áttelepítés)** 
--- | --- | ---
Azure Migrate kiszolgáló értékelése | A [VMWare virtuális gépek](tutorial-prepare-vmware.md), a [Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálók](tutorial-prepare-physical.md)értékelése. |  Nem érhető el (NA)
Azure Migrate kiszolgáló áttelepítése | n/a | Telepítse át a [VMWare virtuális gépeket](tutorial-migrate-vmware.md), a [Hyper-V virtuális gépeket](tutorial-migrate-hyper-v.md)és a [fizikai kiszolgálókat](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | n/a | VMware virtuális gépek, Hyper-V virtuális gépek, fizikai kiszolgálók, nyilvános Felhőbeli számítási feladatok migrálása. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése. | n/a
[A kölcsönzés technológiája](https://go.microsoft.com/fwlink/?linkid=2084928) | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése és migrálása. |  VMware virtuális gépek, Hyper-V virtuális gépek, fizikai kiszolgálók, nyilvános Felhőbeli számítási feladatok migrálása.
[Eszköz 42](https://go.microsoft.com/fwlink/?linkid=2097158) | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése.| n/a
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Helyszíni SQL Server-adatbázisok értékelése. | n/a
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | n/a | SQL Server, Oracle, MySQL, PostgreSQL, MongoDB migrálása. 
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | A virtuális asztali infrastruktúra (VDI) felmérése | n/a
[Mozgató](https://go.microsoft.com/fwlink/?linkid=2109528) | A VMWare virtuális gépek, a Hyper-V virtuális gépek, a Xen virtuális gépek, a fizikai gépek, a munkaállomások (beleértve a VDI-ket is) felmérése | n/a
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | n/a | VMWare virtuális gépek, Hyper-V virtuális gépek, Xen virtuális gépek, KVM-alapú virtuális gépek, fizikai gépek, nyilvános Felhőbeli számítási feladatok migrálása 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a nyilvános Felhőbeli munkaterhelések felmérése. | n/a
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Felméri a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, a nyilvános Felhőbeli számítási feladatokat és a SQL Server adatbázisokat. | n/a
[WebApp Migration Assistant](https://appmigration.microsoft.com/) | Webes alkalmazások értékelése | Webes alkalmazások migrálása.


## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
Előfizetés | Egy előfizetéshez több Azure Migrate projekt is tartozhat.
Azure-engedélyek | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
VMware virtuális gépek  | Egyetlen projektben akár 35 000 VMware virtuális gépet is megvizsgálhat.
Hyper-V virtuális gépek | Akár 35 000 Hyper-V virtuális gépet is kivizsgálhat egyetlen projektben.

A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.

## <a name="azure-permissions"></a>Azure-engedélyek

Ahhoz, hogy a Azure Migrate az Azure-ban működjön, a gépek felmérése és áttelepítése előtt szüksége lesz ezekre az engedélyekre.

**Tevékenység** | **Engedélyek** | **Részletek**
--- | --- | ---
Azure Migrate projekt létrehozása | Az Azure-fióknak rendelkeznie kell a projekt létrehozásához szükséges engedélyekkel. | A [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), a [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)vagy a [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-create-project)beállítása.
A Azure Migrate berendezés regisztrálása | A Azure Migrate egy egyszerűsített [Azure Migrate berendezés](migrate-appliance.md) használatával értékeli a VMWare virtuális gépeket Azure Migrate Server Assessment használatával, valamint a VMWare virtuális gépek [ügynök nélküli áttelepítését](server-migrate-overview.md) Azure Migrate kiszolgáló áttelepítésével. Ez a készülék felfedi a virtuális gépeket, és a VM-metaadatokat és teljesítményadatokat Azure Migrateba küldi.<br/><br/> A regisztráció során Azure Migrate két Azure Active Directory-(Azure AD-) alkalmazást hoz létre, amelyek egyedileg azonosítják a készüléket, és engedélyekkel kell rendelkezniük ezeknek az alkalmazásoknak a létrehozásához.<br/><br/> – Az első alkalmazás a Azure Migrate szolgáltatási végpontokkal kommunikál.<br/><br/> – A második alkalmazás a regisztráció során létrehozott Azure Key Vault fér hozzá az Azure AD-alkalmazás adatai és a készülék konfigurációs beállításainak tárolásához. | A [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), a [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)vagy a [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)beállítása.
Key Vault létrehozása a VMware ügynök nélküli Migrálás számára | Ha a VMware virtuális gépeket ügynök nélküli Azure Migrate kiszolgáló áttelepítésével szeretné áttelepíteni, Azure Migrate létrehoz egy Key Vault, amely a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezeli. A tároló létrehozásához meg kell határoznia az engedélyeket (tulajdonos, közreműködő és felhasználói hozzáférés rendszergazdája) azon az erőforráson, amelyben a Azure Migrate projekt található. | Engedélyek [beállítása](tutorial-prepare-vmware.md#assign-role-assignment-permissions) .

## <a name="supported-geographies"></a>Támogatott földrajzi területek

Azure Migrate-projektet számos földrajzi régióban is létrehozhat. Habár ezekben a földrajzi területeken csak projekteket hozhat létre, a gépeket más célhelyekre is kielemezheti vagy áttelepítheti. A projekt földrajza csak a felderített metaadatok tárolására szolgál.

**Régiócsoport** | **Metaadatok tárolási helye**
--- | ---
Azure Government | US Gov Virginia
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


 > [!NOTE]
 > A Azure Government támogatása jelenleg csak a Azure Migrate [régebbi verziójához](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) érhető el.



## <a name="vmware-assessment-and-migration"></a>VMware Assessment és Migration

[Tekintse át](migrate-support-matrix-vmware.md) a Azure Migrate Server Assessment and Server áttelepítési támogatási mátrixot a VMWare virtuális gépekhez.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V felmérése és migrálása

[Tekintse át](migrate-support-matrix-hyper-v.md) a Hyper-V virtuális gépek Azure Migrate kiszolgáló-értékelési és kiszolgáló-áttelepítési támogatási mátrixát.



## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: ezzel a verzióval új Azure Migrate projekteket hozhat létre, felderítheti a helyszíni értékeléseket, és összehangolhatja az értékeléseket és áttelepítéseket. [További információk](whats-new.md#release-version-july-2019).
- **Előző verzió**: a Azure Migrate korábbi verzióját használó ügyfelek esetében (csak a helyszíni VMWare virtuális gépek felmérése támogatott), most az aktuális verziót kell használnia. Az előző verzióban nem hozhat létre új Azure Migrate projekteket, és nem végezhet új felfedezéseket.

## <a name="next-steps"></a>Következő lépések

- A [VMWare virtuális gépek kiértékelése](tutorial-assess-vmware.md) áttelepítéshez.
- [Vizsgálja át a Hyper-V virtuális gépeket](tutorial-assess-hyper-v.md) az áttelepítéshez.

