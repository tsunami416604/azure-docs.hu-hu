---
title: A Azure Migrate újdonságai | Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9eb70d74752b3db5fcbc185f596d518100992672
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158569"
---
# <a name="whats-new-in-azure-migrate"></a>A Azure Migrate újdonságai

A [Azure Migrate](migrate-services-overview.md) segítségével helyszíni kiszolgálókat, alkalmazásokat és az Microsoft Azure felhőbe áttelepítheti, elemezheti és áttelepítheti azokat. Ez a cikk a Azure Migrate új szolgáltatásait foglalja össze.



## <a name="update-november-2019"></a>Frissítés (2019. november)

Számos új funkció lett hozzáadva a Azure Migratehoz:

- A **fizikai kiszolgáló értékelése**. A helyszíni fizikai kiszolgálók értékelése mostantól támogatott a már támogatott fizikai kiszolgálók áttelepítése mellett.
- **Importálás-alapú értékelés**. A rendszer mostantól támogatja a CSV-fájlban megadott metaadatokat és teljesítményadatokat használó gépek értékelését.
- **Alkalmazás-felderítés**: Azure Migrate mostantól támogatja az alkalmazások, szerepkörök és szolgáltatások alkalmazás szintű felderítését a Azure Migrate berendezés használatával. Ez jelenleg csak a VMware virtuális gépek esetén támogatott, és csak felderítésre korlátozódik (az értékelés jelenleg nem támogatott). [Részletek](how-to-discover-applications.md)
- **Ügynök nélküli függőségi vizualizáció**: már nem kell explicit módon telepítenie az ügynököket a függőségi vizualizációhoz. Mind az ügynök nélküli, mind az ügynök-alapú támogatás már támogatott.
- **Virtuális asztal**: az ISV-eszközök használatával helyszíni virtuális asztali infrastruktúrát (VDI) lehet felmérni és áttelepíteni az Azure-beli Windows rendszerű virtuális asztalra.
- **Webalkalmazás**: a webalkalmazások értékeléséhez és áttelepítéséhez használt Azure app Service Migration Assistant már integrálva van a Azure Migratebe.

Új felmérési és áttelepítési eszközök lettek hozzáadva a Azure Migratehoz:

- **Rackware**: a felhő áttelepítését kínálja.
- **Győztes**: ajánlat értékelése.

[További](migrate-services-overview.md) információ az eszközök és az ISV-ajánlatok használatáról a Azure Migrate értékeléséhez és áttelepítéséhez.

## <a name="release-version-july-2019"></a>Kiadás verziója (július 2019)

A Azure Migrate jelenlegi verziója 2019 júliusában jelent meg.

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket.
- **Előző verzió**: a Azure Migrate korábbi verzióját használó ügyfelek esetében (csak a helyszíni VMWare virtuális gépek felmérése támogatott), most az aktuális verziót kell használnia. Az előző verzióban már nem lehet új Azure Migrate projekteket létrehozni, vagy új felfedezéseket végezni. Továbbra is hozzáférhet a meglévő projektekhez. Ehhez a Azure Portal > **minden szolgáltatásban**keresse meg a **Azure Migrate**. A Azure Migrate értesítésekben található egy hivatkozás a régi Azure Migrate projektek eléréséhez.


### <a name="azure-migrate-features"></a>Azure Migrate funkciók

A Azure Migrate aktuális verziója számos új funkciót kínál:


- **Egységesített áttelepítési platform**: Azure Migrate mostantól egyetlen portált biztosít az Azure-ba való áttelepítés központosításához, kezeléséhez és nyomon követéséhez, amely továbbfejlesztett üzembe helyezési folyamattal és portálral rendelkezik.
- **Értékelési és áttelepítési eszközök**: Azure Migrate natív eszközöket biztosít, és integrálható más Azure-szolgáltatásokkal, valamint független szoftvergyártói eszközökkel. [További](migrate-services-overview.md#isv-integration) információ az ISV-integrációról.
- **Azure Migrate Assessment**: a Azure Migrate Server Assessment eszköz használatával kiértékelheti a VMWare virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez. Más Azure-szolgáltatásokkal és ISV-eszközökkel is elvégezheti az áttelepítés értékelését.
- **Azure Migrate Migrálás**: a Azure Migrate Server áttelepítési eszköz használatával áttelepítheti a helyszíni VMWare virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba, valamint a fizikai kiszolgálókat, más virtualizált kiszolgálókat és magán/nyilvános Felhőbeli virtuális gépeket. Emellett az Azure-ba az ISV-eszközök használatával is áttelepíthetők.
- **Azure Migrate berendezés**: Azure Migrate üzembe helyez egy könnyű berendezést a helyszíni VMWare virtuális gépek és a Hyper-V virtuális gépek felderítéséhez és értékeléséhez.
    - Ezt a készüléket Azure Migrate Server Assessment használja, és Azure Migrate kiszolgáló áttelepítését az ügynök nélküli áttelepítéshez.
    - A készülék folyamatosan felfedi a kiszolgálói metaadatokat és a teljesítményadatokat az értékelés és a Migrálás céljából.  
- **VMWare virtuális gép migrálása**: Azure Migrate kiszolgáló áttelepítése több módszert biztosít a helyszíni VMWare virtuális gépek Azure-ba történő áttelepítéséhez.  Az Azure Migrate készüléket használó ügynök nélküli áttelepítés, valamint egy olyan ügynök-alapú áttelepítés, amely replikációs berendezést használ, és minden áttelepíteni kívánt virtuális gépen üzembe helyezi az ügynököt. [Részletek](server-migrate-overview.md)
 - **Adatbázisok felmérése és áttelepítése**: a Azure Migrateból kiértékelheti a helyszíni adatbázisokat az Azure-ba való áttelepítéshez az azure Database Migration Assistant használatával. Az adatbázisokat a Azure Database Migration Service használatával is áttelepítheti.
- **Webalkalmazások áttelepítése**: a webalkalmazások a Azure app Service használatával a nyilvános végpont URL-címével is felmérhetők. A belső .NET-alkalmazások áttelepítéséhez letöltheti és futtathatja a App Service Migration Assistant.
- **Data Box**: nagy mennyiségű offline adatmennyiség importálása az Azure-ba a Azure Migrate Azure Data Box használatával.


## <a name="next-steps"></a>Következő lépések

- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-assess-vmware.md) és a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)értékeléséhez.
