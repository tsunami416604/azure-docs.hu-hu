---
title: Az Azure Migrate bemutatása
description: Ismerkedjen meg a Azure Migrate szolgáltatással.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81768404"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate szolgáltatás gyors áttekintését tartalmazza.

A Azure Migrate központosított központot biztosít az Azure helyszíni kiszolgálóira, infrastruktúrára, alkalmazásokra és adatszolgáltatásokra való kiértékeléséhez és átköltöztetéséhez.

Azure Migrate a következő funkciókat biztosítja:

- **Egységes áttelepítési platform**: egyetlen portál az Azure-ba való Migrálás elindításához, futtatásához és nyomon követéséhez.
- **Eszközök köre**: számos eszköz az értékeléshez és az áttelepítéshez. Az eszközök közé tartozik a Azure Migrate: a kiszolgáló értékelése és a Azure Migrate: kiszolgáló áttelepítése. Azure Migrate integrálható más Azure-szolgáltatásokkal, valamint más eszközökkel és független szoftvergyártói (ISV) ajánlatokkal.
- **Értékelés és áttelepítés**: az Azure Migrate központban a következőket értékelheti és telepítheti át:
    - **Kiszolgálók**: a helyszíni kiszolgálók értékelése és az Azure-beli virtuális gépekre való migrálása.
    - **Adatbázisok**: kiértékelése a helyszíni adatbázisokat, és áttelepíti azokat Azure SQL Database vagy egy Azure SQL Database felügyelt példányra.
    - **Webalkalmazások**: kiértékelheti a helyszíni webalkalmazásokat, és áttelepítheti azokat Azure app Servicere a Azure app Service Migration Assistant használatával.
    - **Virtuális asztalok**: mérje fel a helyszíni virtuális asztali infrastruktúrát (VDI), és telepítse át a Windows rendszerű virtuális asztalra az Azure-ban.
    - **Adatforgalom**: nagy mennyiségű adatforgalom migrálása az Azure-ba gyorsan és költséghatékonyan Azure Data Box termékek használatával.

## <a name="integrated-tools"></a>Integrált eszközök

Az Azure Migrate hub az alábbi eszközöket tartalmazza:

**Eszköz** | **Értékelés és Migrálás** | **Részletek**
--- | --- | ---
**Azure Migrate: kiszolgáló értékelése** | Kiszolgálók értékelése. | A helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók felderítése és kiértékelése az Azure-ba való Migrálás előkészítése során.
**Azure Migrate: kiszolgáló áttelepítése** | Kiszolgálók migrálása. | Telepítse át a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, a többi virtualizált gépet és az Azure-ba irányuló nyilvános Felhőbeli
**Data Migration Assistant** | A helyszíni SQL Server-adatbázisok kiértékelése Azure SQL Database, Azure SQL Database felügyelt példány vagy SQL Server rendszert futtató Azure-beli virtuális gépek áttelepítésére. | A Data Migration Assistant segít az áttelepítést blokkoló lehetséges problémák azonosításában. Azonosítja a nem támogatott funkciókat, új szolgáltatásokat, amelyek hasznosak lehetnek az áttelepítés után, valamint az adatbázis-áttelepítés helyes elérési útját. [További információ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Helyszíni adatbázisok migrálása SQL Server-, Azure SQL Database-vagy Azure SQL Database felügyelt példányokat futtató Azure-beli virtuális gépekre. | [További](https://docs.microsoft.com/azure/dms/dms-overview) információ a Database Migration Serviceról.
**Movere** | Kiszolgálók értékelése. | [További](#movere) információ a mozgató szolgáltatásról.
**Webalkalmazás-áttelepítési segéd** | Mérje fel a helyszíni webalkalmazásokat, és telepítse át őket az Azure-ba. |  A Azure App Service Migration Assistant segítségével kihasználhatja a helyszíni webhelyeket Azure App Servicere való áttelepítéshez.<br/><br/> A .NET-és PHP-alapú webalkalmazásokat az Azure-ba áttelepítheti a Migration Assistant használatával. [További](https://appmigration.microsoft.com/) információ a Azure app Service Migration Assistantról.
**Azure Data Box** | Offline adatáttelepítés. | Azure Data Box termékek használatával nagy mennyiségű offline állapotú adatátvitelt helyezhet át az Azure-ba. [További információ](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Ha Azure Government, a külső integrált eszközök és az ISV-ajánlatok nem tudnak adatküldeni Azure Migrate projekteknek. Az eszközöket egymástól függetlenül is használhatja.

## <a name="isv-integration"></a>ISV-integráció

A Azure Migrate számos ISV-ajánlattal integrálható. 

**ISV**    | **Szolgáltatás**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók migrálása.
[Cloudamize](https://www.cloudamize.com/platform) | Kiszolgálók értékelése.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók felmérése és migrálása.
[Device42](https://docs.device42.com/) | Kiszolgálók értékelése.
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI kiértékelése.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Kiszolgálók migrálása.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Kiszolgálók értékelése.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Kiszolgálók és adatbázisok értékelése.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: kiszolgáló-Assessment eszköz

A Azure Migrate: a kiszolgáló-értékelő eszköz felkeresi és kiértékeli a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat az Azure-ba való áttelepítéshez.

Az eszköz a következőket teszi:

- **Azure-készültség**: azt vizsgálja, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- **Azure-méretezés**: a Migrálás után megbecsüli az Azure-beli virtuális gépek méretét.
- **Azure Cost-becslés**: a helyszíni kiszolgálók Azure-ban történő futtatásával kapcsolatos költségek becslése.
- **Függőség elemzése**: a kiszolgálók közötti függőségeket és optimalizálási stratégiákat azonosítja a egymástól függő kiszolgálók Azure-ba történő áthelyezéséhez. További információ a [függőségi elemzéssel](concepts-dependency-visualization.md)rendelkező kiszolgálók értékeléséről.

A kiszolgáló értékelése a helyszínen üzembe helyezett, könnyű [Azure Migrate berendezést](migrate-appliance.md) használ.

- A készülék egy virtuális gépen vagy fizikai kiszolgálón fut. Egyszerűen telepítheti egy letöltött sablon használatával.
- A készülék felfedi a helyszíni gépeket. Emellett folyamatosan küldi a számítógép-metaadatokat és teljesítményadatokat a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre.
- A készülék felderítése után a felderített gépeket csoportokba gyűjtheti, és az egyes csoportok értékeléseit is futtathatja.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: kiszolgáló áttelepítési eszköze

A Azure Migrate: a kiszolgáló áttelepítési eszköze segíti az Azure-ba való áttelepítést:

- Helyszíni VMware virtuális gépek
- Hyper-V virtuális gépek
- Fizikai kiszolgálók
- Egyéb virtualizált gépek
- Nyilvános Felhőbeli virtuális gépek

A gépeket az értékelést követően áttelepítheti, vagy áttelepítheti azokat.

A VMware virtuális gépek ügynök nélküli áttelepítése és a Hyper-V virtuális gépek áttelepítése esetén a kiszolgáló áttelepítése egy Azure Migrate berendezést használ, amelyet a helyszínen telepít. A készüléket a kiszolgáló értékelésének beállításakor is használja a rendszer. Az előző szakaszban olvasható.

## <a name="selecting-assessment-and-migration-tools"></a>Értékelési és áttelepítési eszközök kiválasztása

Az Azure Migrate központban kiválaszthatja az értékeléshez vagy az áttelepítéshez használni kívánt eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha ISV-eszközt vagy Mozgatót ad hozzá:

- Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz utasításait követve. Minden ISV vagy eszköz megadja az eszköz licencelését.
- Minden eszközhöz lehetőség van a Azure Migratehoz való kapcsolódásra. A kapcsolódáshoz kövesse az eszközre vonatkozó utasításokat.
- Az áttelepítést nyomon követheti az összes eszközön a Azure Migrate projekten belül.

## <a name="movere"></a>Movere

A mozgató egy szolgáltatott szoftver (SaaS) platform. Az üzleti intelligenciát úgy növeli, hogy a teljes informatikai környezeteket egyetlen napon belül kiterjesztheti. A szervezetek és a vállalatok növelhetik, módosíthatják és digitálisan optimalizálják az optimalizálást. Ahogy igen, a mozgatói a szükséges megbízhatóságot biztosítanak a környezetük, a platform, az alkalmazás vagy a földrajz tekintetében is.

A Microsoft [beszerzett](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) győztese, és már nem önálló ajánlatként vásárolható meg. A mozgató szolgáltatás a Microsoft Solution Assessment és Microsoft Cloud Economics programon keresztül érhető el. [További](https://www.movere.io) információ a mozgató szolgáltatásról.

Javasoljuk, hogy tekintse meg a Azure Migrate, a beépített áttelepítési szolgáltatást is. A Azure Migrate egy központi központot biztosít a felhő áttelepítésének egyszerűbbé tételéhez. A hub átfogóan támogatja a számítási feladatokat, például a fizikai és virtuális kiszolgálókat, az adatbázisokat és az alkalmazásokat. A végpontok közötti láthatósággal könnyedén követheti nyomon az előrehaladást a felderítés, az értékelés és az áttelepítés során.

A beépített Azure-és partner ISV-eszközökkel Azure Migrate számos funkcióval rendelkezik, beleértve a következőket:

- Virtuális és fizikai kiszolgálók felderítése.
- Teljesítmény-alapú megadásában.
- A Cost megtervezése.
- Importálás-alapú értékelések.
- Ügynök nélküli alkalmazások függőségi elemzése.

Ha szakértői segítségre van szüksége az első lépésekhez, a Microsoft az [Azure szakértői által felügyelt szolgáltatók](https://azure.microsoft.com/partners) segítségével segíti Önt. Tekintse meg a [Azure Migrate webhelyét](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van.

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Előző verzió**: a Azure Migrate korábbi verziója csak a helyszíni VMWare virtuális gépek értékelését támogatja. Ha az előző verziót használta, most az aktuális verziót kell használnia. Már nem hozhat létre Azure Migrate projekteket az előző verzió használatával. Azt javasoljuk, hogy ne végezze el az új felfedezéseket.

    A Azure Portal meglévő projektjeinek eléréséhez keresse meg és válassza a **Azure Migrate**lehetőséget. A **Azure Migrate** irányítópulton egy értesítés és egy hivatkozás látható a régi Azure Migrate projektek eléréséhez.

## <a name="next-steps"></a>További lépések

- A [VMWare virtuális gépek](tutorial-prepare-vmware.md), a [Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálók](tutorial-prepare-physical.md)értékeléséhez próbálja ki az oktatóanyagokat.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
