---
title: What's new in Azure Migrate |} A Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809994"
---
# <a name="whats-new-in-azure-migrate"></a>What's new in Azure Migrate

[Az Azure Migrate](migrate-services-overview.md) segít felderíteni, értékelheti és migrálhatja a kiszolgálók, alkalmazások és adatok a Microsoft Azure felhőbe. Ez a cikk az Azure Migrate új funkciókat összegzi.



## <a name="azure-migrate-new-version"></a>Az Azure Migrate új verziója

Az Azure Migrate új verziója jelent meg. július 2019. 

- **(Új) aktuális verzióját**: Ez a verzió használatával hozható létre az Azure Migrate a helyszíni gépek felderítését és felmérését és áttelepítések. 
- **Előző verzió**: Az ügyfél az Azure Migrate (csak a helyszíni VMware virtuális gépek értékelése támogatta) előző verzióját használja most már a jelenlegi verzióját kell használni. Az előző verzió már nem létrehozhat új Azure Migrate-projekt, vagy hajtsa végre az új felderítések. Meglévő projekteket továbbra is elérheti. Ehhez az Azure Portal > a szolgáltatásokat, keresse meg az Azure Migrate. Az Azure Migrate értesítéseket van régi Azure Migrate projektek mutató hivatkozást.


## <a name="azure-migrate-features"></a>Azure Migrate-funkciók

Az Azure Migrate új verziója számos új szolgáltatást nyújt:


- **Áttelepítési egységes platform**: Az Azure Migrate szolgáltatás nyújt egy portálon történő központosítása, kezelése és az Azure-bA a migrálási folyamat elkezdésekor portálja és a továbbfejlesztett üzembe helyezési folyamat nyomon követése.
- **Elemzésekhez és migráláshoz eszközök**: Az Azure Migrate natív eszközöket biztosít, és az egyéb Azure-szolgáltatásokkal, valamint a független szoftverszállító eszközökkel integrálható. [További](migrate-services-overview.md#isv-integration) ISV-k integrálása.
- **Az Azure Migrate-értékelés**: Az Azure Migrate Server Assessment eszközzel felmérheti a VMware virtuális gépek és a Hyper-V virtuális gépek az Azure-ba való migrálásra. Az áttelepítéshez, más Azure-szolgáltatások és eszközök ISV-k használatával is értékelhet.
- **Az Azure Migrate migrálási**: Az Azure Migrate Server áttelepítési eszköz használata, a helyszíni VMware virtuális gépeket telepíthet át, és a Hyper-V virtuális gépek Azure-ban, valamint fizikai kiszolgálókat, más virtualizált kiszolgálók és a privát és nyilvános felhőbeli virtuális géphez. Emellett az ISV-eszközök használatával áttelepítheti.
- **Az Azure Migrate-berendezés**: Az Azure Migrate üzembe helyez egy egyszerűsített készülék a felderítése és értékelése a helyszíni VMware virtuális gépek és a Hyper-V virtuális gépek.
    - A berendezés használják az Azure Migrate Server Assessment, és az Azure Migrate-kiszolgáló áttelepítése az ügynök nélküli migrálást.
    - A berendezés server metaadatok és a teljesítmény-adatok, folyamatosan elemzésekhez és migráláshoz alkalmazásában deríti fel.  
- **VMware virtuális gépek migrálása**:  Azure Migrate Server Migrálási biztosít több módszerrel történő áttelepítéséhez a helyszíni VMware virtuális gépek az Azure-bA.  Az ügynök nélküli migrálást az Azure Migrate berendezés használata, és a egy ügynök-alapú áttelepítés, amely egy replikációs berendezést használ, és az egyes virtuális Gépeken, amelyeket szeretné áttelepíteni egy ügynököt helyez üzembe. [További információ](server-migrate-overview.md)
 - **Adatbázis-elemzésekhez és migráláshoz**: Az Azure Migrate mérje fel a helyszíni adatbázisok az Azure-bA az Azure Database Migration Assistant használata az áttelepítéshez. Telepíthet át adatbázisokat az Azure Database Migration Service használatával.
- **Webes alkalmazások migrálása**: A nyilvános végpont URL-cím használata az Azure App Service web Apps alkalmazások értékelhet. Belső .NET-alkalmazások áttelepítéséhez töltse le, és futtassa az App Service Migration Assistant eszközzel. 
- **Data Box**: Nagy mennyiségű offline adatok importálása az Azure Data Box az Azure Migrate használatával Azure-bA.


## <a name="next-steps"></a>További lépések

- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- Próbálja ki az oktatóanyagaink felmérése [VMware virtuális gépek](tutorial-assess-vmware.md) és [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md).
