---
title: A Azure Migrate újdonságai
description: Ismerkedjen meg a Azure Migrate szolgáltatás új és legújabb frissítéseivel.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: db52c8ce5e1b7e25d4af0c570189597e05b86588
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331865"
---
# <a name="whats-new-in-azure-migrate"></a>A Azure Migrate újdonságai

A [Azure Migrate](migrate-services-overview.md) segítségével helyszíni kiszolgálókat, alkalmazásokat és az Microsoft Azure felhőbe áttelepítheti, elemezheti és áttelepítheti azokat. Ez a cikk a Azure Migrate új kiadásait és szolgáltatásait foglalja össze.

## <a name="update-june-2020"></a>Frissítés (2020. június)

Új Azure Migrate berendezés verziói.

### <a name="azure-public-cloud"></a>Azure nyilvános felhő

**Forgatókönyv*** | **Letöltés** | **SHA256**
--- | --- | ---
Hyper-V (8,93 MB) | [Legújabb verzió](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1
VMware (10,9 GB) | [Legújabb verzió](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd
Fizikai (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0


### <a name="azure-government"></a>Azure Government

**Forgatókönyv*** | **Letöltés** | **SHA256**
--- | --- | ---
Hyper-V (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3
VMware (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de
Fizikai (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1



## <a name="update-april-2020"></a>Frissítés (2020. április)

A Azure Migrate a Azure Government üzemelő példányait támogatja. 

- A VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat is felderítheti és elemezheti.
- VMware virtuális gépeket, Hyper-V virtuális gépeket és fizikai kiszolgálókat telepíthet át az Azure-ba.
- A VMware-áttelepítés esetében ügynök nélküli vagy ügynök alapú áttelepítést használhat. [További információ](server-migrate-overview.md).
- [Tekintse át](migrate-support-matrix.md#supported-geographies-azure-government) a Azure Government támogatott földrajzi területeit és régióit.
- Azure Government nem támogatja az [ügynök-alapú függőségek elemzését](concepts-dependency-visualization.md#agent-based-analysis) .
- Az előzetes verzióban elérhető funkciók a Azure Government, különösen az [ügynök nélküli függőségek elemzése](concepts-dependency-visualization.md#agentless-analysis)és az [alkalmazások felderítése](how-to-discover-applications.md)esetén támogatottak.


## <a name="update-march-2020"></a>Frissítés (2020. március)

Most már elérhető egy parancsfájl-alapú telepítés a [Azure Migrate berendezés](migrate-appliance.md)beállításához:

- A parancsfájl-alapú telepítés a () egyik alternatívája. A berendezés (VMware)/VHD (Hyper-V) telepítése.
- Egy PowerShell-telepítő parancsfájlt biztosít, amely segítségével beállíthatja a készüléket a VMware/Hyper-V-re egy meglévő, Windows Server 2016 rendszerű gépen.

## <a name="update-november-2019"></a>Frissítés (2019. november)

Számos új funkció lett hozzáadva a Azure Migratehoz:

- A **fizikai kiszolgáló értékelése**. A helyszíni fizikai kiszolgálók értékelése mostantól támogatott a már támogatott fizikai kiszolgálók áttelepítése mellett.
- **Importálás-alapú értékelés**. A rendszer mostantól támogatja a CSV-fájlban megadott metaadatokat és teljesítményadatokat használó gépek értékelését.
- **Alkalmazás-felderítés**: Azure Migrate mostantól támogatja az alkalmazások, szerepkörök és szolgáltatások alkalmazás szintű felderítését a Azure Migrate berendezés használatával. Ez jelenleg csak a VMware virtuális gépek esetén támogatott, és csak felderítésre korlátozódik (az értékelés jelenleg nem támogatott). [További információ](how-to-discover-applications.md)
- **Ügynök nélküli függőségi vizualizáció**: már nem kell explicit módon telepítenie az ügynököket a függőségi vizualizációhoz. Mind az ügynök nélküli, mind az ügynök-alapú támogatás már támogatott.
- **Virtuális asztal**: az ISV-eszközök használatával helyszíni virtuális asztali infrastruktúrát (VDI) lehet felmérni és áttelepíteni az Azure-beli Windows rendszerű virtuális asztalra.
- **Webalkalmazás**: a webalkalmazások értékeléséhez és áttelepítéséhez használt Azure app Service Migration Assistant már integrálva van a Azure Migratebe.

Új felmérési és áttelepítési eszközök lettek hozzáadva a Azure Migratehoz:

- **Rackware**: a felhő áttelepítését kínálja.
- **Győztes**: ajánlat értékelése.

[További](migrate-services-overview.md) információ az eszközök és az ISV-ajánlatok használatáról a Azure Migrate értékeléséhez és áttelepítéséhez.

## <a name="azure-migrate-current-version"></a>Azure Migrate aktuális verzió

A Azure Migrate (2019 júliusában kiadott) jelenlegi verziója számos új funkciót kínál:

- **Egységesített áttelepítési platform**: Azure Migrate mostantól egyetlen portált biztosít az Azure-ba való áttelepítés központosításához, kezeléséhez és nyomon követéséhez, amely továbbfejlesztett üzembe helyezési folyamattal és portálral rendelkezik.
- **Értékelési és áttelepítési eszközök**: Azure Migrate natív eszközöket biztosít, és integrálható más Azure-szolgáltatásokkal, valamint független szoftvergyártói eszközökkel. [További](migrate-services-overview.md#isv-integration) információ az ISV-integrációról.
- **Azure Migrate Assessment**: a Azure Migrate Server Assessment eszköz használatával kiértékelheti a VMWare virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez. Más Azure-szolgáltatásokkal és ISV-eszközökkel is elvégezheti az áttelepítés értékelését.
- **Azure Migrate Migrálás**: a Azure Migrate Server áttelepítési eszköz használatával áttelepítheti a helyszíni VMWare virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba, valamint a fizikai kiszolgálókat, más virtualizált kiszolgálókat és magán/nyilvános Felhőbeli virtuális gépeket. Emellett az Azure-ba az ISV-eszközök használatával is áttelepíthetők.
- **Azure Migrate berendezés**: Azure Migrate üzembe helyez egy könnyű berendezést a helyszíni VMWare virtuális gépek és a Hyper-V virtuális gépek felderítéséhez és értékeléséhez.
    - Ezt a készüléket Azure Migrate Server Assessment használja, és Azure Migrate kiszolgáló áttelepítését az ügynök nélküli áttelepítéshez.
    - A készülék folyamatosan felfedi a kiszolgálói metaadatokat és a teljesítményadatokat az értékelés és a Migrálás céljából.  
- **VMWare virtuális gép migrálása**: Azure Migrate kiszolgáló áttelepítése több módszert biztosít a helyszíni VMWare virtuális gépek Azure-ba történő áttelepítéséhez.  Az Azure Migrate készüléket használó ügynök nélküli áttelepítés, valamint egy olyan ügynök-alapú áttelepítés, amely replikációs berendezést használ, és minden áttelepíteni kívánt virtuális gépen üzembe helyezi az ügynököt. [További információ](server-migrate-overview.md)
 - **Adatbázisok felmérése és áttelepítése**: a Azure Migrateból kiértékelheti a helyszíni adatbázisokat az Azure-ba való áttelepítéshez az azure Database Migration Assistant használatával. Az adatbázisokat a Azure Database Migration Service használatával is áttelepítheti.
- **Webalkalmazások áttelepítése**: a webalkalmazások a Azure app Service használatával a nyilvános végpont URL-címével is felmérhetők. A belső .NET-alkalmazások áttelepítéséhez letöltheti és futtathatja a App Service Migration Assistant.
- **Data Box**: nagy mennyiségű offline adatmennyiség importálása az Azure-ba a Azure Migrate Azure Data Box használatával.

## <a name="azure-migrate-previous-version"></a>Előző verzió Azure Migrate

Ha a Azure Migrate korábbi verzióját használja (csak a helyszíni VMware virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. Az előző verzióban már nem lehet új Azure Migrate projekteket létrehozni, vagy új felfedezéseket végezni. Továbbra is hozzáférhet a meglévő projektekhez. Ehhez a Azure Portal > **minden szolgáltatásban**keresse meg a **Azure Migrate**. A Azure Migrate értesítésekben található egy hivatkozás a régi Azure Migrate projektek eléréséhez.



## <a name="next-steps"></a>Következő lépések

- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-assess-vmware.md) és a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)értékeléséhez.
