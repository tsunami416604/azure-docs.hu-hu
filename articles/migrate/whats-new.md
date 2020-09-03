---
title: A Azure Migrate újdonságai
description: Ismerkedjen meg a Azure Migrate szolgáltatás új és legújabb frissítéseivel.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 019c04c5820a82b7910b6755ff69e5dfbe7eb66c
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421611"
---
# <a name="whats-new-in-azure-migrate"></a>A Azure Migrate újdonságai

A [Azure Migrate](migrate-services-overview.md) segítségével helyszíni kiszolgálókat, alkalmazásokat és az Microsoft Azure felhőbe áttelepítheti, elemezheti és áttelepítheti azokat. Ez a cikk a Azure Migrate új kiadásait és szolgáltatásait foglalja össze.
## <a name="update-september-2020"></a>Frissítés (2020. szeptember)
- Azure Migrate mostantól lehetővé teszi a kiszolgálók áttelepíteni Availability Zones

## <a name="update-august-2020"></a>Update (augusztus 2020)

- Továbbfejlesztett bevezetési élmény, amelyben egy Azure Migrate-projekt kulcsa jön létre a portálról, és a készülék regisztrációjának befejezésére szolgál.
- Lehetőség a petesejtek/VHD-fájlok vagy a portálon lévő telepítési parancsfájlok letöltésére a VMware és a Hyper-V berendezések beállításához.
- Frissítette a készülék Configuration Managert a továbbfejlesztett felhasználói felülettel.
- Több hitelesítő adat támogatja a Hyper-V virtuális gépek felderítését.
- Továbbfejlesztett keresési, rendezési és szűrési funkciók a hozzáadott hitelesítő adatok és a felderítési források számára.
- Egyetlen elem bemenete, több elem beolvasása és CSV-importálási beállítások a felhasználó számára a Hyper-V-gazdagépek/-fürtök felderítési forrásainak hozzáadásához & fizikai kiszolgálókon.
- Továbbfejlesztett hiba történt az érvényesítési és felderítési műveletekhez tartozó állapot frissítéseivel a táblázatban szereplő összes hozzáadott forrás esetében. 

## <a name="update-june-2020"></a>Frissítés (2020. június)

- A helyszíni VMware virtuális gépek [Azure VMware-megoldásba (AVS)](https://go.microsoft.com/fwlink/?linkid=2132637) való áttelepítésének felmérése mostantól támogatott. [További információ](how-to-create-azure-vmware-solution-assessment.md)
- Több hitelesítő adat támogatása a készüléken a fizikai kiszolgálók felderítéséhez.
- Támogatás, amely lehetővé teszi az Azure-bejelentkezést a készülékről olyan bérlőre, ahol a bérlői korlátozás konfigurálva van.


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
