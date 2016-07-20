<properties
    pageTitle="A platformszolgáltatásos SQL Database és a felhőben, virtuális gépeken futó SQL Server összehasonlítása | Microsoft Azure"
    description="Cikkünkből megtudhatja, melyik felhőalapú SQL Server-verzió illik a legjobban az alkalmazásához: az Azure SQL Database (platformszolgáltatásos) adatbázis vagy az Azure virtuális gépeken futó felhőalapú SQL Server?"
    services="sql-database, virtual-machines"
    keywords="SQL Server cloud, SQL Server in the cloud, PaaS database, cloud SQL Server, DBaaS"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/25/2016"
    ms.author="carlrab"/>

# Felhőalapú SQL Server-verzió választása: Azure SQL Database (platformszolgáltatásos) adatbázis vagy az Azure virtuális gépeken futó SQL Server (infrastruktúra-szolgáltatás)

Az Azure két megoldást kínál az SQL Server-számítási feladatok felhőben történő futtatására:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): natív módon felhőalapú, szoftverszolgáltatás (SaaS) típusú alkalmazások fejlesztésére optimalizált SQL-adatbázis, amely platformszolgáltatási adatbázisként vagy adatbázis-szolgáltatásként is ismert. Ez a megoldás az SQL Server legtöbb funkciójával kompatibilis.
* [Azure virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): az SQL Servert ebben a megoldásban a felhőben, az Azure-platformon futó Windows Server virtuális gépekre telepítik, illetve ezek futtatják a programot. Ez a módszer infrastruktúra-szolgáltatás néven is ismert.

Ebből a cikkből megtudhatja, hogyan illeszkednek ezek a verziók a Microsoft adatplatformjának rendszerébe, és rátalálhat az üzleti igényeinek leginkább megfelelő lehetőségre. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.


## A Microsoft adatplatformja

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását.  Így egyedi és sokszínű üzleti igényeit helyszíni és felhőben futó üzemelő példányok együttes használatával elégítheti ki, ráadásul az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőalapú SQL Server-verziók: SQL Server infrastruktúra-szolgáltatásban vagy SaaS SQL-adatbázis a felhőben.](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

- SQL Server nem virtuális, fizikai gépeken
- SQL Server helyszíni virtuális gépeken (magánfelhő)
- SQL Server Azure virtuális gépen (nyilvános felhő)
- Azure SQL Database (nyilvános felhő)

A következő részekben a nyilvános felhőben futó SQL Server-verziókról olvashat, azaz az Azure SQL Database-ről és az Azure virtuális gépeken futó SQL Serverről. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása

Az **Azure SQL Database** az Azure-felhőben üzemeltetett relációs adatbázis-szolgáltatás, amely a *szoftverszolgáltatás (SaaS)* és a *platformszolgáltatás (PaaS)* ipari kategóriákba tartozik. Az SQL Database a Microsoft tulajdonában álló, illetve általa futtatott és fenntartott szabványos hardvereken és szoftvereken fut. Az SQL Database beépített szolgáltatásai és funkciói segítségével közvetlenül a szolgáltatásban végezheti el a fejlesztést. Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is.

Az **Azure virtuális gépeken futó SQL Server** az *infrastruktúra-szolgáltatás* kategóriába esik. Ebben a verzióban az SQL Server egy felhőalapú virtuális gépen fut. Az SQL Database-hez hasonlóan ez is a Microsoft tulajdonában álló, illetve általa üzemeltetett és fenntartott szabványos hardvereken fut. Az SQL Server virtuális gépen történő futtatásához használhatja saját SQL Server-licencét, de választhat egy előlicencelt SQL Server-rendszerképet is az Azure portálról.

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

- Az **SQL Database** segítségével nagyszámú adatbázis üzembe helyezése és kezelése esetében a lehető legalacsonyabbra csökkenthetők a költségek. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. A csomag frissítésre, magas rendelkezésre állásra, illetve biztonsági mentésre használható funkciókat is tartalmaz. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá.
- Az **Azure virtuális gépeken futó SQL Server** segítségével meglévő helyszíni SQL Server-alkalmazásait hibrid megoldásként a felhőbe helyezheti, illetve az Azure alá helyezheti őket áttelepítés vagy fejlesztés és tesztelés céljából. Hibrid megoldás például, amikor másodlagos adatbázis-replikákat tart meg az Azure-ban az [Azure Virtual Network ](../virtual-network/virtual-networks-overview.md) hálózatok segítségével. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. A virtuális gépeken futó SQL Server segítségével a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">SQL Database</th>
   <th align="left" valign="middle">Azure virtuális gépen futó SQL Server</th>

</tr>
<tr>
   <td valign="middle"><p><b>A következőkre alkalmas</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Új felhőalapú alkalmazásokhoz, amelyek fejlesztésére és népszerűsítésére szűkösebb időkeret áll rendelkezésre.
          <li type=round>Beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokat igénylő alkalmazásokhoz.
          <li type=round>Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni.
         <li type=round>Horizontális felskálázást használó alkalmazásokhoz.
         <li type=round>Legfeljebb 1 TB méretű adatbázisokhoz.
         <li type=round>Szoftverszolgáltatás (SaaS) típusú alkalmazásokhoz.
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Gyors felhőbeli áttelepítést igénylő alkalmazásokhoz, amelyekben legfeljebb minimális változtatásokra van szükség.
      <li type=round>Helyszíni erőforrásokhoz (például az Active Directoryhoz) biztonságos alagúton át való Azure-hozzáférést igénylő SQL Server-alkalmazásokhoz.
      <li type=round>Ha testre szabható számítástechnikai környezetre van szüksége, amelyhez teljes körű rendszergazdai jogokkal rendelkezik.
      <li type=round>Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni.
      <li type=round>Vészhelyreállításhoz helyszíni SQL Server-alkalmazásokhoz [Azure Storage-biztonsági mentés](http://msdn.microsoft.com/library/jj919148.aspx) vagy Azure virtuális gépeken futó AlwaysOn-replikák használatával.
      <li type=round>1 TB-nál nagyobb méretű adatbázisokhoz.
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>Erőforrások</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>Ha nem szeretne számítástechnikai erőforrásokat fordítani az alkalmazás mögötti infrastruktúra támogatására és karbantartására.
       <li type=round>Ha az alkalmazási rétegre szeretne koncentrálni.
       </ul></td>
   <td valign="middle"><ul><li type=round>Ha rendelkezik támogatási és karbantartási számítástechnikai erőforrásokkal.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Tulajdonosi költségek</b></p></td>
   <td valign="middle"><ul><li type=round>Nincsenek hardverköltségek. Csökkenti a felügyeleti költségeket.</ul></td>
   <td valign="middle"><ul><li type=round>Nincsenek hardverköltségek. </ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Az üzletmenet folytonossága</b></p></td>
   <td valign="middle"><ul><li type=round>Az infrastruktúra beépített hibatűrési jellemzői mellett az Azure SQL Database is tartalmaz az üzletmenet folytonosságát elősegítő funkciókat (például időponthoz kötött visszaállítás, georedundáns helyreállítás, georeplikáció). További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése).</ul></td>
   <td valign="middle"><ul><li type=round>Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren).</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Hibrid felhő</b></p></td>
   <td valign="middle"><ul><li type=round>A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. Ezenfelül az [Azure SQL Server-adatfájlok](http://msdn.microsoft.com/library/dn385720.aspx) funkciójával elhelyezheti a helyszíni adatfájlokat az Azure Storage tárhelyen. További információk: [Introduction to SQL Server 2014 Hybrid Cloud](http://msdn.microsoft.com/library/dn606154.aspx) (Bevezetés az SQL Server 2014 által biztosított hibrid felhő használatába).
      <li type=round>Az [Azure Blob Storage által biztosított SQL Server biztonsági mentés és helyreállítás](http://msdn.microsoft.com/library/jj919148.aspx) vagy az [Azure virtuális gépeken futó AlwaysOn-replikák](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) segítségével támogatja a helyszíni SQL Server-alkalmazások vészhelyreállítását.
      </ul></td>

</tr>
</table>

## Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok

### Költségek

Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a részben először az Azure-ben elérhető két relációsadatbázis-változatra (az SQL Database-re és az Azure virtuális gépeken futó SQL Serverre) vonatkozó számlázási és licencelési alapokkal ismerkedhet meg. Ezt követően megtanulhatja, hogyan számítható ki  az alkalmazás összköltsége.

#### A számlázás és a licencelés alapjai

Az **SQL Database**-t szolgáltatásként értékesítjük, nem licenc formájában, míg az Azure virtuális gépeken futó SQL Serverhez hagyományos SQL Server-licenc vásárlása szükséges.

Az **SQL Database** jelenleg többféle szolgáltatáscsomagban kapható, amelyek esetében a választott szolgáltatás- és teljesítményszinten alapuló rögzített óradíjat kell fizetni. Ezenfelül a kimenő internetforgalom díját is felszámítjuk. Az Alapszintű, a Standard és a Prémium csomag és a hozzá választható különböző teljesítményszintek az alkalmazás maximális terhelése esetén is megbízhatóan képesek működni. Az alkalmazás változó sebességigényét követve akár válthat is a szolgáltatás- és teljesítményszintek között. Amennyiben az adatbázisban sok tranzakció zajlik, illetve nagyszámú egyidejű felhasználót kell kiszolgálnia, javasoljuk, hogy válassza a Prémium szolgáltatásszintet. Az aktuálisan választható szolgáltatásszintekkel kapcsolatos további információkért lásd: [Azure SQL Database Service Tiers](sql-database-service-tiers.md) (Az Azure SQL Database szolgáltatáscsomagjai).

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-business-continuity.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure virtuális gépeken futó SQL Server** esetében a hagyományos SQL Server-licencelési mechanizmusok érvényesek. Használhatja a platformon elérhető SQL Server-rendszerképet (amelyhez licenc is tartozik), de akár saját SQL Server-licencét is. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. A virtuális gép méretétől és az SQL Server verziójától függetlenül meg kell azonban fizetnie az SQL Server és a Windows Server percalapú licencelési költségeit, valamint a virtuális gép lemezeivel kapcsolatban felmerülő Azure Storage-költségeket. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha saját SQL Server-licencét használja az Azure-ban, csak a Windows Server és a tárhely költségeit kell kifizetnie. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében).

#### Az alkalmazás összköltségének kiszámítása

A felhőplatform használatának kezdetén az alkalmazás futtatásának díját elsősorban a fejlesztés és a felügyelet költségei, valamint a nyilvános felhőplatform használatának szolgáltatási költségei teszik ki.

Az alábbiakban az alkalmazás SQL Database-zel és az Azure virtuális gépeken futó SQL Serverrel történő futtatásának részletes költségszámításait láthatja:

**Az Azure SQL Database használata esetében:**

*Alkalmazás összköltsége = a lehető legalacsonyabbra csökkentett adminisztrációs költségek + szoftverfejlesztési költségek + SQL Database szolgáltatási költségei*

**Az Azure virtuális gépeken futó SQL Server esetében:**

*Alkalmazás összköltsége = a szoftver fejlesztésének/átalakításának lehető legalacsonyabbra csökkentett költségei + adminisztrációs költségek + az SQL Server és a Windows Server licencelési költségei + az Azure Storage költségei*

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtuális gépek – Díjszabás ](https://azure.microsoft.com/pricing/details/virtual-machines/) ([SQL-hez](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és [Windowshoz](https://azure.microsoft.com/pricing/details/virtual-machines/#windows))
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Az SQL Server funkcióinak egy szűk köre nem használható az SQL Database-ben. További információk: [SQL Database General Limitations and Guidelines](sql-database-general-limitations.md) (Az SQL Database-re vonatkozó általános korlátozások és útmutatások) és [SQL Database Transact-SQL information](sql-database-transact-sql-information.md) (SQL Database Transact-SQL információk). Ha meglévő SQL Server-megoldást szeretne áttelepíteni a felhőbe, olvassa el a következő témakört: [Migrating a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (SQL Server-adatbázis áttelepítése az Azure SQL Database alá). Amikor meglévő helyszíni SQL Server-alkalmazást telepít át az SQL Database alá, fontolja meg az alkalmazás frissítését, hogy az kihasználhassa a felhőszolgáltatások által kínált előnyöket. Az alkalmazás üzemeltetésére használhatja például az [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) vagy az [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) szolgáltatást, így további költségelőnyökhöz juthat.

### Adminisztráció

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az **SQL Database** használata esetén a Microsoft felel az alkalmazás mögött álló hardverek felügyeletéért, az adatok magas rendelkezésre állás érdekében történő automatikus replikálásáért, az adatbázisszoftver konfigurálásáért és frissítéséért, a terheléselosztás kezeléséért, valamint a kiszolgálóhiba esetén történő transzparens feladatátvételért. Az adatbázis kezelését továbbra is Ön végzi, de az adatbázismotor, valamint a kiszolgáló-operációsrendszer és -hardver felügyeletével többé nem kell foglalkoznia.  Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság.

Előfordulhat azonban, hogy a vállalatnál rendelkezésre áll a szükséges szakértelem, és szeretné egy Ön által kontrollált helyen található lemezen tudni az adatbázist. Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is.  Az Azure-ban például igény szerint módosíthatja a virtuális gép méretét. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

### Szolgáltatói szerződés (SLA)

Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

Az **SQL Database** Alapszintű, Standard és Prémium szolgáltatáscsomagja esetében a Microsoft 99,99%-os SLA-elérhetőséget garantál. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/). Az SQL Database szolgáltatáscsomagjaival és az általuk támogatott üzletmenet-folytonossági csomagokkal kapcsolatban lásd: [Service Tiers](sql-database-service-tiers.md) (Szolgáltatáscsomagok).

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). A virtuális gépeken üzemeltetett adatbázisok magas rendelkezésre állása érdekében használja az SQL Server által biztosított magas rendelkezésre állási funkciók egyikét, például az [AlwaysOn rendelkezésre állási csoportokat](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Piacra jutás

Az **SQL Database** ideális megoldás az olyan felhőalapú alkalmazásokhoz, amelyek esetében rendkívül fontos szempont a gyors fejlesztés és a rövid piacra jutási idő. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt. A [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) és a [PowerShell-parancsmagok](http://msdn.microsoft.com/library/azure/dn546726.aspx) segítségével például akár több ezer adatbázis felügyeleti műveleteit automatizálhatja és felügyelheti. A [rugalmas adatbáziskészlet](sql-database-elastic-pool.md) és más hasonló funkcióknak köszönhetően Ön az alkalmazási rétegre összpontosíthat, így gyorsabban piacra juttathatja a terméket.

Az **Azure virtuális gépeken futó SQL Server** tökéletes megoldás, ha meglévő vagy új alkalmazásainak az SQL Server-példány összes funkciójához hozzá kell férnie. Akkor is érdemes ezt a verziót választani, ha meglévő helyszíni alkalmazásokat és adatbázisokat szeretne változtatás nélkül az Azure-ra áttelepíteni. Mivel így nincs szükség a prezentációs, alkalmazási és adatréteg módosítására, időt és pénzt takarít meg, amit nem kell meglévő termékeinek egy új architektúrára való áthelyezésére fordítania. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## Összefoglalás

Ebben a cikkben bemutattuk az SQL Database-t és az Azure virtuális gépeken futó SQL Servert, és szót ejtettünk azokról az üzleti szempontokról, amelyek hatással lehetnek választására. Az alábbiakban a megfontolásra érdemes tényeket foglaljuk össze:

Válassza az **Azure SQL Database-t**, ha:

- Új felhőalapú alkalmazásokat fejleszt, vagy szeretné áttelepíteni meglévő SQL Server-megoldását, hogy igénybe vehesse a felhőszolgáltatások által biztosított költségmegtakarításokat és optimalizált teljesítményt. Ezzel a verzióval a teljes körűen felügyelt felhőszolgáltatások minden előnyét élvezheti, ezenfelül csökkentheti a piacra jutási időt, illetve hosszú távon is optimalizálhatja kiadásait.

- E megoldás választása esetén érdemes a Microsoftra bízni az adatbázisok általános felügyeleti műveleteinek elvégzését, és erősebb rendelkezésre állási SLA-kat igényelni az adatbázisokhoz.



Válassza az **Azure virtuális gépeken futó SQL Servert**, ha:

- Meglévő helyszíni alkalmazásokkal rendelkezik, de nem szeretne foglalkozni a hardverek karbantartásával, vagy hibrid megoldások bevezetését fontolgatja. Ezzel a megoldással gyorsabban juthat magas adatbázis-kapacitáshoz, miközben biztonságos alagúton alakíthatja ki a kapcsolatot helyszíni alkalmazásaihoz.

- Ha rendelkezésre állnak számítástechnikai erőforrásai, és teljes körű rendszergazdai jogokat szeretne az SQL Server fölött, illetve teljes körű kompatibilitásra van szüksége a helyszíni SQL Serverrel. Ez a verzió segít a lehető legalacsonyabbra csökkenteni a meglévő alkalmazások fejlesztési vagy átalakítási költségeit, és rugalmas megoldást kínál, amelyen a legtöbb alkalmazás képes futni. Ezenfelül így teljes mértékben Ön konfigurálhatja a virtuális gépet, az operációs rendszert, valamint az adatbázist.



> [AZURE.NOTE] Szeretné kipróbálni az SQL Server 2016 CTP2-t? Regisztráljon a Microsoft Azure-ba, majd lépjen [ide](http://aka.ms/sql2016vm "ide"), és hozzon létre egy virtuális gépet. A gépen előre telepítve van az SQL Server 2016 CTP2.

## Következő lépések
- Az SQL Database használatának megkezdésével kapcsolatban lásd: [SQL Database tutorial: Create a SQL database in minutes using the Azure portal](sql-database-get-started.md) (SQL Database oktatóanyag: SQL-adatbázis létrehozása néhány perc alatt az Azure portálon).
- Lásd: [SQL-adatbázis – Díjszabás] (https://azure.microsoft.com/pricing/details/sql-database/)
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).



<!--HONumber=Jun16_HO2-->


