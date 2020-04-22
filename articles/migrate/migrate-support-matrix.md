---
title: Azure Migrate támogatási mátrix
description: Az Azure Migrate szolgáltatás támogatási beállításainak és korlátainak összegzése.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: 44a971894f53a3f31c068b3c3ed4912bc7e00dab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680701"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate támogatási mátrix

Az Azure [Migrate szolgáltatás](migrate-overview.md) segítségével felmérheti és áttelepítheti a gépeket a Microsoft Azure-felhőbe. Ez a cikk összegzi az Azure Migrate-forgatókönyvek és -telepítések általános támogatási beállításait és korlátait.

## <a name="supported-assessmentmigration-scenarios"></a>Támogatott értékelési/áttelepítési forgatókönyvek

A táblázat összefoglalja a támogatott felderítési, értékelési és áttelepítési forgatókönyveket.

**Üzembe helyezés** | **Részletek** 
--- | --- 
**Alkalmazásspecifikus felderítés** | A VMware virtuális gépeken futó alkalmazásokat, szerepköröket és funkciókat fedezhet fel. Jelenleg ez a funkció csak felderítésre korlátozódik. Az értékelés jelenleg a gép szintjén van. Még nem kínálunk alkalmazás-, szerepkör- vagy funkcióspecifikus értékelést. 
**Helyszíni értékelés** | A helyszíni számítási feladatok és a VMware virtuális gépeken, a Hyper-V vM-eken és a fizikai kiszolgálókon futó adatok felmérheti. Értékelje az Azure Migrate Server Assessment és a Microsoft Data Migration Assistant (DMA), valamint egyéb eszközök és isv-ajánlatok használatával.
**Helyszíni áttelepítés az Azure-ba** | A fizikai kiszolgálókon, vmware virtuális gépeken, Hyper-VM-eken, fizikai kiszolgálókon és felhőalapú VMS-en futó számítási feladatok és adatok áttelepítése az Azure-ba. Az Áttelepítés az Azure Migrate Server Assessment és az Azure Database Migration Service (DMS), valamint más eszközök és isv-ajánlatok használatával.

> [!NOTE]
> Az isv-eszközök jelenleg nem küldhetnek adatokat az Azure Migrate in Azure Government szolgáltatásba. Használhatja az integrált Microsoft-eszközöket, vagy egymástól függetlenül használhatja a partnereszközöket.

## <a name="supported-tools"></a>Támogatott eszközök

A konkrét eszköztámogatást a táblázat foglalja össze.

**Eszköz** | **Értékelje** | **Migrate (Áttelepítés)** 
--- | --- | ---
Azure áttelepítési kiszolgáló értékelése | Értékelje [a VMware virtuális gépeket](tutorial-prepare-vmware.md), [a Hyper-V vM-eket](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálókat.](tutorial-prepare-physical.md) |  Nem érhető el (NA)
Azure Migrate Server Migration | NA | [VMware virtuális gépek,](tutorial-migrate-vmware.md) [hyper-v vm-ek](tutorial-migrate-hyper-v.md)és [fizikai kiszolgálók áttelepítése.](tutorial-migrate-physical-virtual-machines.md)
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | Telepítse ni a VMware virtuális gépek, hyper-vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok áttelepítése. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Értékelje a VMware virtuális gépek, hyper-v vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok. | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware virtuális gépek, Hyper-V vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok felmérheti és áttelepítheti. |  Telepítse ni a VMware virtuális gépek, hyper-vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok áttelepítése.
[Eszköz 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Értékelje a VMware virtuális gépek, hyper-v vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok.| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | A helyszíni SQL Server-adatbázisok felmérése. | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | Telepítse az SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | Virtuális asztali infrastruktúra (VDI) felmérése | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare virtuális gépek, Hyper-V vms, Xen virtuális gépek, fizikai gépek, munkaállomások (beleértve a VDI-t is), nyilvános felhőbeli számítási feladatok | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | VMWare virtuális gépek, Hyper-V vM-ek, Xen virtuális gépek, KVM virtuális gépek, fizikai gépek, nyilvános felhőbeli számítási feladatok áttelepítése 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Értékelje a VMware virtuális gépek, hyper-v vm-ek, fizikai kiszolgálók, nyilvános felhőalapú számítási feladatok. | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Értékelje a VMware virtuális gépek, a Hyper-V VM-ek, a fizikai kiszolgálók, a nyilvános felhőbeli számítási feladatok és az SQL Server-adatbázisok. | NA
[Webapp áttelepítési segéd](https://appmigration.microsoft.com/) | A webalkalmazások felmérése | Webalkalmazások áttelepítése.


## <a name="azure-migrate-projects"></a>Azure Áttelepítési projektek

**Támogatás** | **Részletek**
--- | ---
Előfizetés | Több Azure Migrate-projektet is létrehozhat egy előfizetésben.
Azure-engedélyek | Az Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekre van szüksége az előfizetésben.
VMware virtuális gépek  | Egyetlen projektben akár 35 000 VMware virtuális gépet is felmérhet.
Hyper-V virtuális gépek    | Egyetlen projektben akár 35 000 Hyper-V virtuális gépet is felmérhet.

A projekt tartalmazhat VMware virtuális gépek és hyper-V virtuális gépek, az értékelési korlátokig.

## <a name="azure-permissions"></a>Azure-engedélyek

Az Azure Áttelepítési Azure-ral való együttműködésre szüksége van ezekre az engedélyekre, mielőtt elkezdené a gépek felmérését és áttelepítését.

**Tevékenység** | **Engedélyek** | **Részletek**
--- | --- | ---
Azure Migrate projekt létrehozása | Az Azure-fióknak engedélyekre van szüksége a projekt létrehozásához. | [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)vagy [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-create-project)beállítása.
Az Azure Migrate-berendezés regisztrálása| Az Azure Migrate egy könnyű [Azure Migrate készüléket](migrate-appliance.md) használ az Azure Migrate Server Assessment szolgáltatással rendelkező gépek értékeléséhez, valamint a VMware virtuális gépek [ügynök nélküli migrálásának](server-migrate-overview.md) futtatásához az Azure Migrate Server Migration segítségével. Ez a készülék felderíti a gépeket, és metaadatokat és teljesítményadatokat küld az Azure Migrate szolgáltatásnak.<br/><br/> A regisztráció során regisztrálja szolgáltatók (Microsoft.OffAzure, Microsoft.Migrate és Microsoft.KeyVault) regisztrálva vannak a készülékben kiválasztott előfizetéssel, így az előfizetés együttműködik az erőforrás-szolgáltatóval. A regisztrációhoz közreműködői vagy tulajdonosi hozzáférésre van szüksége az előfizetéshez.<br/><br/> **VMware**- A bevezetés során az Azure Migrate két Azure Active Directory (Azure AD) alkalmazást hoz létre. Az első alkalmazás kommunikál a készülék ügynökök és az Azure Áttelepítésszolgáltatás között. Az alkalmazás nem rendelkezik engedélyekkel az Azure erőforrás-kezelési hívásokhoz, vagy rBAC-hozzáféréssel rendelkezik az erőforrásokhoz. A második alkalmazás csak ügynök nélküli VMware-áttelepítéshez létrehozott Azure Key Vault-ot biztosít. Ügynök nélküli áttelepítés esetén az Azure Migrate létrehoz egy Key Vault-ot az előfizetés replikációs tárfiókhoz való hozzáférési kulcsok kezeléséhez. RBAC-hozzáféréssel rendelkezik az Azure Key Vault (az ügyfél-bérlő) felderítésindításkor a készülékről.<br/><br/> **Hyper-V**-A bevezetés során. Az Azure Migrate egyetlen Azure AD-alkalmazást hoz létre. Az alkalmazás kommunikál a készülék ügynökök és az Azure Áttelepítés szolgáltatás között. Az alkalmazás nem rendelkezik engedélyekkel az Azure erőforrás-kezelési hívásokhoz, vagy rBAC-hozzáféréssel rendelkezik az erőforrásokhoz. | [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)vagy [fizikai kiszolgálók](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)beállítása.
Kulcstartó létrehozása a VMware ügynök nélküli áttelepítéséhez | A VMware virtuális gépek ügynök nélküli Azure Migrate Server Migration alkalmazással történő áttelepítéséhez az Azure Migrate létrehoz egy Key Vault-ot az előfizetés replikációs tárfiókhoz való hozzáférési kulcsok kezeléséhez. A tároló létrehozásához adja meg az engedélyeket (tulajdonos, vagy közreműködői és a felhasználói hozzáférés rendszergazdája) az erőforráscsoport, amelyben az Azure Migrate projekt található. | [Engedélyek beállítása.](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)

## <a name="supported-geographies-public-cloud"></a>Támogatott földrajzi területek (nyilvános felhő)

Létrehozhat egy Azure Migrate projektet számos földrajzi területen a nyilvános felhőben. Bár csak ezeken a földrajzi területeken hozhat létre projekteket, felmérheti vagy áttelepítheti a gépeket más célhelyekre. A projekt földrajzi csak a felderített metaadatok tárolására szolgál.

**Földrajz** | **Metaadatok tárolási helye**
--- | ---
Ázsia és a Csendes-óceáni térség | Kelet-Ázsia vagy Délkelet-Ázsia
Ausztrália | Ausztrália Kelet vagy Ausztrália Délkelet
Brazília | Dél-Brazília
Kanada | Kanada Közép- vagy Kelet-Kanada
Európa | Észak-Európa vagy Nyugat-Európa
Franciaország | Közép-Franciaország
India | Közép-India vagy Dél-India
Japán |  Kelet-Japán vagy Nyugat-Japán
Dél-Korea | Korea Közép- vagy Dél-Korea
Egyesült Királyság | Egyesült Királyság déli vagy egyesült királyságbeli nyugati
Egyesült Államok | USA középső vagy usa nyugati régiója 2


## <a name="supported-geographies-azure-government"></a>Támogatott földrajzi területek (Azure Government)

**Tevékenység** | **Földrajz** | **Részletek**
--- | --- | ---
Projekt létrehozása | Egyesült Államok | Metaadatok tárolása az Egyesült Államok gov Arizona, US Gov Virginia
Célfelmérés | Egyesült Államok | Célrégiók: US Gov Arizona, US Gov Virginia, US Gov Texas
Célreplikáció | Egyesült Államok | Célrégiók: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>VMware értékelése és áttelepítése

[Tekintse át](migrate-support-matrix-vmware.md) az Azure Áttelepítési kiszolgáló értékelése és a kiszolgáló áttelepítése támogatási mátrix vmware virtuális gépek.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V felmérése és migrálása

[Tekintse át](migrate-support-matrix-hyper-v.md) az Azure Áttelepítési kiszolgáló értékelése és a kiszolgáló áttelepítése támogatási mátrix hyper-V virtuális gépek.



## <a name="azure-migrate-versions"></a>Az Azure Áttelepítés verziói

Az Azure Migrate szolgáltatásnak két verziója van:

- **Jelenlegi verzió:** Ezzel a verzióval új Azure Migrate projekteket hozhat létre, felderítheti a helyszíni értékeléseket, és vezényelheti az értékeléseket és az áttelepítéseket. [További információ](whats-new.md).
- **Előző verzió:** Az Azure Migrate korábbi verzióját használó ügyfelek számára (csak a helyszíni VMware virtuális gépek felmérése támogatott), most használja az aktuális verziót. Az előző verzióban nem hozhat létre új Azure Migrate-projekteket, és nem végezhet új felderítéseket.

## <a name="next-steps"></a>További lépések

- [VMware virtuális gépek áttelepítése.](tutorial-assess-vmware.md)
- [Értékelje a Hyper-V virtuális gépek áttelepítésére.](tutorial-assess-hyper-v.md)

