---
title: Az Azure Migrate bemutatása | Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 083eaaa5be35d515a477ce6286f226b267569e1e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840364"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk egy rövid áttekintést nyújt az Azure Migrate.

Az Azure Migrate segít az Azure-bA migrálása. Az Azure Migrate biztosít egy információs központ nyomon követéséhez a felderítése, a felmérési és a helyszíni infrastruktúra, alkalmazások és adatok áttelepítése az Azure-bA. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure eszközöket biztosít. Az alábbiakat biztosítja:

- **Áttelepítési egységes platform**: Használatával egyetlen portál indításához futtassa, és nyomon követheti a migrálási folyamat az Azure-bA.
- **Számos eszközt**: Az Azure Migrate natív eszközöket biztosít, és integrálja az Azure-szolgáltatásokat, valamint az ISV-eszközökkel. Válassza ki a megfelelő elemzésekhez és migráláshoz eszközökkel, a szervezeti követelmények alapján. 
- **Az Azure Migrate Server Assessment**: Felmérheti az Server Assessment eszköz használatával a helyszíni VMware virtuális gépek és a Hyper-V virtuális gépek, az Azure-ba való migrálásra.
- **Az Azure Migrate-kiszolgáló áttelepítése**: A kiszolgáló-áttelepítési eszköz használata a helyszíni VMware virtuális gépeket, Hyper-V virtuális gépek, cloud virtuális gépek és fizikai kiszolgálók áttelepítése az Azure-bA.
- **Az Azure Migrate az adatbázis-értékelés**: Mérje fel a helyszíni adatbázisok az Azure-ba való migrálásra.
- **Az Azure Migrate az adatbázis-Migrálás**: A helyszíni adatbázisok migrálása az Azure-bA.


## <a name="azure-migrate-versions"></a>Az Azure Migrate-verziók

Nincsenek az Azure Migrate szolgáltatás két verziója:

- **Aktuális verzió**: Ez a verzió használatával hozható létre az Azure Migrate a helyszíni gépek felderítését és felmérését és áttelepítések. [További](whats-new.md) kapcsolatos újdonságok ebben a verzióban.
- **Előző verzió**: Ha az Azure Migrate (csak a helyszíni VMware virtuális gépek értékelése támogatta) előző verzióját használja, most aktuális verzióját kell használni. Már nem hozható létre az Azure Migrate a korábbi verziót használ, vagy hajtsa végre az új felderítések. Azonban továbbra is elérheti meglévő projekteket. Ehhez az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**. Az Azure Migrate irányítópulton nincs értesítés és a egy hivatkozást a régi Azure Migrate projektek.

## <a name="isv-integration"></a>Az ISV-integráció

Natív Azure-eszközök Azure Migrate integrálódik az ISV-ajánlatokról számos. Azonosíthatja az eszközt kell, és adja hozzá az Azure Migrate-projektet. A migrálási folyamat az az Azure Migrate-projektben a központilag nyomon követése az Azure és a független Szoftverszállítók eszközök között.

**ISV** | **Funkció**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Értékelés
[Eszköz 42](https://docs.device42.com/) | Értékelés
[A Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Értékelés
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Értékelés
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Értékelni és migrálni
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrate (Áttelepítés)

### <a name="selecting-an-isv-tool"></a>Egy független Szoftverszállító eszköz kiválasztása

A felvett egy ISV-eszköz az Azure Migrate-projektet, első lépésként a eszközzel licenc, vagy regisztráljon egy ingyenes próbaverzióra, az ISV-szabályzatának megfelelően. Minden egyes eszköz csatlakozni az Azure Migrate lehetőség van. Kövesse az eszköz utasításokat és dokumentációja, az eszköz csatlakoztatása az Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Az Azure Migrate Server Assessment

Az Azure Migrate Server Assessment deríti fel, és felméri a helyszíni VMware virtuális gépek és a Hyper-V virtuális gépek, az Azure-ba való migrálásra. Ez segít azonosítani a következő:

- **Azure-kompatibilitás:** Annak ellenőrzéséhez, hogy készen áll a helyszíni gépek az Azure-ba való migrálásra.
- **Az Azure méretezési:** Azure virtuális gépek méretének becslése az áttelepítés után.
- **Az Azure költségbecslés:** Becsült költségek a helyszíni kiszolgálók Azure-ban futó.
- **Függőségek képi megjelenítése:** Azonosítsa a kiszolgálók közötti függőségeket, és a legjobb módszer a függő kiszolgálók áthelyezése az Azure-bA. 

Server Assessment egy egyszerűsített, hogy telepítse őket a helyszínen, és regisztrálja Server Assessment berendezést használ.

- A berendezés felderíti a helyszíni gépek, Server Assessment csatlakozik, és folyamatosan elküldi a metaadatokat és a teljesítménnyel kapcsolatos adatokat az Azure Migrate.
- Ügynök nélküli kivételfigyelés az észlelés. Semmit nem kell telepíteni a felderített virtuális gépen.
- Miután a felfedezett gépek őket csoportokba rendezheti, amelyek általában együtt áttelepíteni kívánt virtuális gépek állnak.
- Hozzon létre egy értékelést a csoport számára. Az értékelés, döntse el, az áttelepítési stratégiáját, majd elemezheti.

## <a name="azure-migrate-server-migration"></a>Az Azure Migrate-kiszolgáló áttelepítése

Az Azure Migrate kiszolgáló áttelepítése segít, hogy telepítse a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, fizikai kiszolgálókon, más virtuális gépek és a nyilvános felhőbeli virtuális géphez, az Azure-bA. Gépek áttelepítheti azokat értékelése után, vagy anélkül egy értékelést. 

## <a name="azure-migrate-database-assessment"></a>Az Azure Migrate az adatbázis-értékelés

Az Azure Migrate integrálható a Data Migration Assistant (DMA) a helyszíni SQL Server-adatbázisok Azure SQL Database, Azure SQL felügyelt példánya vagy SQL Server rendszert futtató Azure virtuális gépek a migrálás értékeléséhez. DMA áttelepítés lehetséges hátráltató tényezőkkel kapcsolatos információkat biztosít. Nem támogatott szolgáltatások, valamint, hogy az áttelepítés után is kihasználhatják, és segít azonosítani a helyes elérési útjával adatbázis migrálása az új funkciók azonosítja. [További információk](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Az Azure Migrate az adatbázis-Migrálás

Az Azure Migrate integrálható az az Azure Database Migration Service (DMS), a helyszíni adatbázisok áttelepítése az Azure-bA. A DMS használatával a helyszíni adatbázisok migrálása az Azure virtuális gépeken futó SQL, Azure SQL Database és az Azure SQL felügyelt példányok. [További információk](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Webes alkalmazás elemzésekhez és migráláshoz

Az Azure Migrate hubról mérje fel, és a helyileg üzemeltetett webes alkalmazások migrálása az Azure-bA.

- **Mérje fel a webalkalmazások online**: Az Azure App Service Migration Assistant annak ellenőrzésére fog használni a helyszíni webhelyek az Azure App Service-ben való migráláshoz.
- **Web Apps alkalmazások áttelepítése a**: .NET és a PHP webes alkalmazások migrálása az Azure-ba, az Azure App Service Migration Assistant használata.

[Részletek](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Offline adatok migrálása

A Data Box offline termékcsalád használatával nagy mennyiségű adat áthelyezése az Azure-bA. [További információ](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>További lépések

- Próbálja ki az oktatóanyagaink felmérése [VMware virtuális gépek](tutorial-assess-vmware.md) és [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md).
- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
