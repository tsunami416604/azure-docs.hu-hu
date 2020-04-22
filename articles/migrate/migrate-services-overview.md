---
title: Az Azure Migrate bemutatása
description: További információ az Azure Áttelepítés szolgáltatásról.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768404"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk az Azure Migrate szolgáltatás gyors áttekintését tartalmazza.

Az Azure Migrate egy központi központot biztosít a helyszíni Azure-kiszolgálók, infrastruktúra, alkalmazások és adatok felméréséhez és átköltöztetéséhez.

Az Azure Migrate a következő szolgáltatásokat nyújtja:

- **Egységes áttelepítési platform:** Egyetlen portál az Azure-ba való migrálás elindításához, futtatásához és nyomon követéséhez.
- **Eszközök köre**: Az értékeléshez és a migrációhoz szükséges eszközök széles választéka. Az eszközök közé tartozik az Azure Migrate: Server Assessment és az Azure Migrate: Server Migration. Az Azure Migrate integrálható más Azure-szolgáltatásokkal, valamint más eszközökkel és független szoftverszállítói (ISV) ajánlatokkal.
- **Értékelés és áttelepítés:** Az Azure Áttelepítési központban felmérheti és áttelepítheti:
    - **Kiszolgálók:** Értékelje a helyszíni kiszolgálókat, és telepítse át őket az Azure virtuális gépekre.
    - **Adatbázisok:** Értékelje a helyszíni adatbázisokat, és telepítse át őket az Azure SQL Database vagy egy Azure SQL Database felügyelt példányba.
    - **Webalkalmazások:** Értékelje a helyszíni webalkalmazásokat, és telepítse át őket az Azure App Service-be az Azure App Service-áttelepítési segéd használatával.
    - **Virtuális asztalok:** Mérje fel a helyszíni virtuális asztali infrastruktúrát (VDI), és telepítse át az Azure-beli Windows virtuális asztalra.
    - **Adatok:** Nagy mennyiségű adatot telepítheti át az Azure-ba gyorsan és költséghatékonyan az Azure Data Box-termékek használatával.

## <a name="integrated-tools"></a>Integrált eszközök

Az Azure Migrate hub a következő eszközöket tartalmazza:

**Eszköz** | **Felmérés és áttelepítés** | **Részletek**
--- | --- | ---
**Azure Áttelepítés: Kiszolgálófelmérés** | Értékelje a kiszolgálókat. | Fedezze fel és értékelje a helyszíni VMware virtuális gépeket, hyper-v-virtuális gépeket és fizikai kiszolgálókat az Azure-ba való migrálásra való felkészülés során.
**Azure áttelepítése: Kiszolgáló áttelepítése** | Kiszolgálók áttelepítése. | Telepítse a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, más virtualizált gépeket és a nyilvános felhőalapú virtuális gépeket az Azure-ba.
**Adatáttelepítési segéd** | Felmérheti a helyszíni SQL Server-adatbázisokat az Azure SQL Database- alkalmazásba, egy Azure SQL Database által felügyelt példányba vagy AZ SQL Servert futtató Azure virtuális gépekre való áttelepítéshez. | Az Adatáttelepítési segéd segít az áttelepítést blokkoló lehetséges problémák azonosításában. Azonosítja a nem támogatott szolgáltatásokat, az áttelepítés után előnyös új szolgáltatásokat, valamint az adatbázis-áttelepítés helyes elérési útját. [További információ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Telepítse át a helyszíni adatbázisokat az SQL Server, az Azure SQL Database vagy az Azure SQL Database által felügyelt példányokat futtató Azure-virtuális gépekre. | [További információ](https://docs.microsoft.com/azure/dms/dms-overview) az Adatbázis-áttelepítési szolgáltatásról.
**Movere** | Értékelje a kiszolgálókat. | [További információ](#movere) a Movere-ról.
**Webalkalmazás áttelepítési segédje** | Értékelje a helyszíni webalkalmazásokat, és telepítse át őket az Azure-ba. |  Az Azure App Service-áttelepítési segéddel felmérheti a helyszíni webhelyeket az Azure App Service-be való áttelepítéshez.<br/><br/> A Migrálási segéd segítségével telepítse át a .NET és a PHP webalkalmazásokat az Azure-ba. [További információ](https://appmigration.microsoft.com/) az Azure App Service-áttelepítési segédről.
**Azure Data Box** | Kapcsolat nélküli adatok áttelepítése. | Azure Data Box-termékekkel nagy mennyiségű offline adatot helyezhet át az Azure-ba. [További információ](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Ha az Azure Government szolgáltatásban van, a külső integrált eszközök és az isv-ajánlatok nem küldhetnek adatokat az Azure Migrate-projekteknek. Az eszközöket egymástól függetlenül is használhatja.

## <a name="isv-integration"></a>IsV integráció

Az Azure Migrate számos isv-ajánlattal integrálható. 

**Isv**    | **Szolgáltatás**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók áttelepítése.
[Cloudamize](https://www.cloudamize.com/platform) | Értékelje a kiszolgálókat.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók felmérése és áttelepítése.
[Device42](https://docs.device42.com/) | Értékelje a kiszolgálókat.
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | Értékelje a VDI-t.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Kiszolgálók áttelepítése.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Értékelje a kiszolgálókat.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Értékelje a kiszolgálókat és adatbázisokat.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Áttelepítés: Kiszolgálóértékelési eszköz

Az Azure Migrate: Server Assessment eszköz felderíti és felméri a helyszíni VMware virtuális gépeket, a Hyper-V VM-eket és a fizikai kiszolgálókat az Azure-ba való migráláshoz.

Az eszköz a következőket teszi:

- **Azure-készenlét:** Felméri, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- **Azure méretezése:** Megbecsüli az Azure virtuális gépek méretét az áttelepítés után.
- **Azure-költségbecslés:** Megbecsüli a helyszíni kiszolgálók azure-beli futtatásának költségeit.
- **Függőségelemzés**: Azonosítja a kiszolgálók közötti függőségeket és optimalizálási stratégiákat az egymástól függő kiszolgálók Azure-ba való áthelyezéséhez. További információ a [kiszolgálóállapot-elemzéssel](concepts-dependency-visualization.md)kapcsolatos további tudnivalókról.

A Server Assessment egy könnyű [Azure Migrate-berendezést](migrate-appliance.md) használ, amelyet a helyszínen telepít.

- A készülék virtuális gépen vagy fizikai kiszolgálón fut. Könnyen telepítheti egy letöltött sablon használatával.
- A készülék helyszíni gépeket fedez fel. Emellett folyamatosan küldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásba.
- A készülék felderítése ügynök nélküli. Semmi sincs telepítve a felfedezett gépeken.
- A készülék felderítése után a felderített gépeket csoportokba gyűjtheti, és minden csoportra vonatkozóan lefutthat.

## <a name="azure-migrate-server-migration-tool"></a>Azure Áttelepítés: Kiszolgálóáttelepítési eszköz

Az Azure Áttelepítés: Kiszolgálóáttelepítés eszköz segít az Azure-ba való áttelepítésben:

- Helyszíni VMware virtuális gépek
- Hyper-V virtuális gépek
- Fizikai kiszolgálók
- Egyéb virtualizált gépek
- Nyilvános felhőbeli virtuális gépek

A gépek et a felmérés után, vagy azokat értékelés nélkül is áttelepítheti.

A VMware virtuális gépek ügynök nélküli áttelepítése és a Hyper-V virtuális gépek áttelepítése esetén a Kiszolgálóáttelepítés egy Azure Migrate-berendezést használ, amelyet a helyszínen telepít. A készülék akkor is használatos, ha kiszolgálófelmérést állít be. Ez az előző szakaszban található.

## <a name="selecting-assessment-and-migration-tools"></a>Értékelési és áttelepítési eszközök kiválasztása

Az Azure Migrate hubban kiválaszthatja az értékeléshez vagy áttelepítéshez használni kívánt eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha független eszközegyesítő eszközt vagy Movere eszközt ad hozzá:

- A kezdéshez szerezzen licencet, vagy regisztráljon egy ingyenes próbaverzióra az eszköz utasításait követve. Minden független független eszköz vagy eszköz meghatározza az eszközlicencelést.
- Minden eszköz rendelkezik egy lehetőség, hogy csatlakozzon az Azure Migrate. A csatlakozáshoz kövesse az eszköz utasításait.
- Kövesse nyomon az áttelepítést az Azure Migrate projektösszes eszközén.

## <a name="movere"></a>Movere

Movere egy szoftver, mint szolgáltatás (SaaS) platform. Növeli az üzleti intelligenciát azáltal, hogy egyetlen nap alatt pontosan bemutatja a teljes informatikai környezeteket. A szervezetek és a vállalatok növekednek, változnak és digitálisan optimalizálnak. Ennek során a Movere biztosítja számukra a szükséges magabiztosságot, hogy láthassák és vezéreljék környezetüket, függetlenül a platformtól, az alkalmazástól vagy a földrajzi elhelyezkedéstől.

A Microsoft [felvásárolta a](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) Movere-t, és már nem önálló ajánlatként értékesítik. A Movere a Microsoft Solution Assessment és a Microsoft Cloud Economics Program keretében érhető el. [További információ](https://www.movere.io) a Movere-ról.

Javasoljuk, hogy tekintse meg az Azure Migrate beépített áttelepítési szolgáltatásunkat is. Az Azure Migrate központi központot biztosít a felhőbe való migrálás egyszerűsítéséhez. A központ átfogóan támogatja a fizikai és virtuális kiszolgálókat, adatbázisokat és alkalmazásokat. A teljes körű láthatóság lehetővé teszi, hogy a felderítés, az értékelés és az áttelepítés során könnyen nyomon követheti az előrehaladást.

With both Azure and partner ISV tools built in, Azure Migrate has an extensive range of features, including:

- Virtuális és fizikai kiszolgálók felderítése.
- Teljesítményalapú jogok.
- Költségtervezés.
- Importalapú értékelések.
- Ügynök nélküli alkalmazások függőségi elemzése.

Ha az első lépésekhez szakértői segítségre van szüksége, a Microsoft képzett [Azure Expert Managed Service Szolgáltatókkal](https://azure.microsoft.com/partners) vezeti Önt. Tekintse meg az [Azure Migrate webhelyet.](https://azure.microsoft.com/services/azure-migrate/) 

## <a name="azure-migrate-versions"></a>Az Azure Áttelepítés verziói

Az Azure Migrate szolgáltatás két verziója létezik.

- **Jelenlegi verzió:** Ezzel a verzióval azure-áttelepítési projekteket hozhat létre, helyszíni gépeket fedezhet fel, és vezényelhet értékeléseket és áttelepítéseket. [További információ](whats-new.md) a jelen verzió újdonságairól.
- **Előző verzió:** Az Azure Migrate korábbi verziója csak a helyszíni VMware virtuális gépek értékelését támogatja. Ha az előző verziót használta, most az aktuális verziót kell használnia. Az Azure Migrate-projektek már nem hozhatnak létre az előző verzióval. És azt javasoljuk, hogy ne tegyen új felfedezéseket vele.

    Az Azure Portalon meglévő projektek eléréséhez keresse meg és válassza az **Azure Migrate**lehetőséget. Az **Azure Migrate** irányítópult egy értesítéssel és egy hivatkozást a régi Azure Migrate projektek eléréséhez.

## <a name="next-steps"></a>További lépések

- Próbálja ki oktatóanyagainkat a [VMware VM-ek,](tutorial-prepare-vmware.md) [hyper-v vm-ek](tutorial-prepare-hyper-v.md)vagy [fizikai kiszolgálók értékeléséhez.](tutorial-prepare-physical.md)
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
