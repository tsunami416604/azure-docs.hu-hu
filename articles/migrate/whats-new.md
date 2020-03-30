---
title: Az Azure Migrate újdonságai
description: Ismerje meg az Azure Migrate szolgáltatás új és legújabb frissítéseit.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: 9767f3ea31b57d23c8a6772ff5eb6500f7550802
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127589"
---
# <a name="whats-new-in-azure-migrate"></a>Az Azure Migrate újdonságai

[Az Azure Migrate](migrate-services-overview.md) segítségével felderítheti, felmérheti és áttelepítheti a helyszíni kiszolgálókat, alkalmazásokat és adatokat a Microsoft Azure-felhőbe. Ez a cikk összegzi az Azure Migrate új kiadásait és funkcióit.

## <a name="update-march-2020"></a>Update (2020. március)

Az [Azure Migrate készülék](migrate-appliance.md)beállításához parancsfájlalapú telepítés érhető el:

- A parancsfájlalapú telepítés a . OVA (VMware)/VHD (Hyper-V) a készülék telepítése.
- Egy PowerShell telepítő parancsfájlt biztosít, amely a VMware/Hyper-V készülék beállítására használható egy Windows Server 2016 rendszert futtató meglévő gépen.

## <a name="update-november-2019"></a>Frissítés (2019. november)

Az Azure Migrate számos új funkcióval bővült:

- **Fizikai szerver értékelése**. A helyszíni fizikai kiszolgálók értékelése már támogatott, a már támogatott fizikai kiszolgálóáttelepítés mellett.
- **Importalapú értékelés**. A CSV-fájlban megadott metaadatokat és teljesítményadatokat használó gépek értékelése mostantól támogatott.
- **Alkalmazásfelderítés:** Az Azure Migrate mostantól támogatja az alkalmazások, szerepkörök és szolgáltatások alkalmazásszintű felderítését az Azure Migrate készülék használatával. Ez jelenleg csak a VMware virtuális gépek, és csak a felderítés (értékelés jelenleg nem támogatott). [További információ](how-to-discover-applications.md)
- **Ügynök nélküli függőségi megjelenítés**: Már nem kell explicit módon telepítenie az ügynököket a függőségi vizualizációhoz. Mind az ügynök nélküli, mind az ügynökalapú most már támogatott.
- **Virtuális asztal:** Az ISV-eszközök segítségével felmérheti és áttelepítheti a helyszíni virtuális asztali infrastruktúrát (VDI) az Azure-beli Windows virtuális asztalra.
- **Webalkalmazás:** Az Azure App Service-áttelepítési segéd, amely a webes alkalmazások értékelésére és áttelepítésére szolgál, most már integrálva van az Azure Migrate szolgáltatásba.

Új értékelési és áttelepítési eszközök kerültek be az Azure Áttelepítésbe:

- **Rackware**: Felhőbe való bemigráció felajánlása.
- **Movere**: Felajánlás értékelés.

[További információ](migrate-services-overview.md) az azure-áttelepítéshez szükséges értékelésekhez és áttelepítéshez szükséges eszközök és isv-ajánlatok használatáról.

## <a name="azure-migrate-current-version"></a>Az Azure áttelepítése jelenlegi verziója

Az Azure Migrate jelenlegi verziója (2019 júliusában jelent meg) számos új funkciót kínál:

- **Egyesített áttelepítési platform:** Az Azure Migrate mostantól egyetlen portált biztosít az Azure-ba való migrálási folyamat központosítására, kezelésére és nyomon követésére, továbbfejlesztett telepítési folyamattal és portállal.
- **Értékelési és áttelepítési eszközök:** Az Azure Migrate natív eszközöket biztosít, és más Azure-szolgáltatásokkal, valamint független szoftverszállítói (ISV) eszközökkel integrálható. [További információ](migrate-services-overview.md#isv-integration) a hálózati és a hálózatért szükséges hálózatba szóló rendszerintegrációról.
- **Azure Migrate assessment**: Az Azure Migrate Server Assessment eszközzel felmérheti a VMware virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való migráláshoz. Más Azure-szolgáltatások és isv-eszközök használatával is felmérheti a migrációt.
- **Azure Áttelepítés áttelepítése:** Az Azure Áttelepítési kiszolgáló áttelepítése eszközzel áttelepítheti a helyszíni vmware virtuális gépeket és a hyper-v-virtuális gépeket az Azure-ba, valamint a fizikai kiszolgálókat, más virtualizált kiszolgálókat és a magán-/nyilvános felhőbeli virtuális gépeket. Emellett az ISV-eszközök használatával áttelepítheti az Azure-ba.
- **Azure Migrate appliance:** Az Azure Migrate egy könnyű berendezést telepít a helyszíni VMware virtuális gépek és a Hyper-VM-ek felderítéséhez és értékeléséhez.
    - Ezt a készüléket az Azure Migrate Server Assessment és az Azure Migrate Server Migration használja ügynök nélküli áttelepítéshez.
    - A készülék folyamatosan felderíti a kiszolgáló metaadatait és teljesítményadatait az értékelés és az áttelepítés céljából.  
- **VMware virtuális gép áttelepítése:** Az Azure Áttelepítési kiszolgáló áttelepítése néhány módszert biztosít a helyszíni VMware virtuális gépek Azure-ba való áttelepítéséhez.  Ügynök nélküli áttelepítés az Azure Migrate-berendezés használatával, és egy ügynök-alapú áttelepítés, amely egy replikációs berendezést használ, és telepíti az ügynököt minden virtuális gép áttelepíteni kívánt. [További információ](server-migrate-overview.md)
 - **Adatbázis-felmérés és áttelepítés:** Az Azure Áttelepítése, felmérheti a helyszíni adatbázisok az Azure-ba az Azure-adatbázis-áttelepítési segéd használatával. Az Azure Database Migration Service használatával áttelepítheti az adatbázisokat.
- **Webalkalmazás-áttelepítés:** A webalkalmazásokat nyilvános végpont URL-cím használatával értékelheti az Azure App Service szolgáltatással. A belső .NET-alkalmazások áttelepítéséhez letöltheti és futtathatja az App Service áttelepítési segédjét.
- **Adatmező:** Nagy mennyiségű offline adatot importálhat az Azure-ba az Azure Data Box használatával az Azure Migrate szolgáltatásban.

## <a name="azure-migrate-previous-version"></a>Az Azure Áttelepítése korábbi verzió

Ha az Azure Migrate korábbi verzióját használta (csak a helyszíni VMware virtuális gépek felmérése támogatott), most használja az aktuális verziót. Az előző verzióban már nem hozhat létre új Azure Migrate-projekteket, és nem hajthat végre új felderítéseket. A meglévő projektek továbbra is elérhetők. Ehhez az Azure Portal on > **All Services**alkalmazásban keresse meg az Azure **Migrate (Azure Migrate)** kifejezést. Az Azure Migrate értesítések, van egy link a régi Azure Migrate projektek eléréséhez.



## <a name="next-steps"></a>További lépések

- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- Próbálja ki oktatóanyagainkat a [VMware vm-ek](tutorial-assess-vmware.md) és [a Hyper-V vm-ek értékeléséhez.](tutorial-assess-hyper-v.md)
