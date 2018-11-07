---
title: Elosztott tranzakciók több felhőalapú adatbázisban
description: Az Azure SQL Database rugalmas adatbázis-tranzakciók áttekintése
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 02cf72bf9fe06993ef859d1789983b7611c8472e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257469"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Elosztott tranzakciók több felhőalapú adatbázisban
Rugalmas adatbázis-tranzakciók az Azure SQL Database (SQL-adatbázis) teszik lehetővé, amelyek több adatbázist az SQL DB tranzakciókat futtatni. Az SQL Database rugalmas adatbázis-tranzakciók forrásoszlopokat használó .NET-alkalmazások érhetők el, valamint integrálhatja a szolgáltatást a megszokott programozási felület használatával a [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) osztályokat. A szalagtár lekéréséhez lásd: [.NET-keretrendszer 4.6.1-es verziója (webes telepítő)](https://www.microsoft.com/download/details.aspx?id=49981).

A helyszínen ilyen esetben általában szükséges a Microsoft elosztott tranzakciók koordinátora (MSDTC) futtatása. Azure Platform –-szolgáltatásként alkalmazásokhoz MSDTC nem érhető el, mivel lehetővé teszi az elosztott tranzakciók koordinálja most közvetlenül integrálva van az SQL DB-be. Alkalmazások bármely SQL Database-indítsa el az elosztott tranzakciók csatlakozhat, és a egy adatbázis transzparens módon összehangolják elosztott tranzakció, az alábbi ábrán látható módon. 

  ![Az Azure SQL Database rugalmas adatbázis-tranzakciók használatával az elosztott tranzakciók ][1]

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
Az SQL Database rugalmas adatbázis-tranzakciók lehetővé teszik az alkalmazások számos különböző SQL-adatbázisokban tárolt adatok atomi módosítja. Az előzetes verzió a C# és a .NET ügyféloldali fejlesztési környezeteket összpontosít. A T-SQL használatával kiszolgálóoldali élmény tervezett későbbi időpontra.  
Rugalmas adatbázis-tranzakciók célozza meg benne a következő esetekben:

* Több adatbázis-alkalmazások az Azure-ban: Ebben a forgatókönyvben a adatok vertikális particionálása az SQL dB-ben több adatbázis közötti úgy, hogy a különböző adatfajták által a különböző adatbázisok találhatók. Egyes műveletekhez szükséges adatokat, amelyet a két vagy több adatbázis módosításait. Az alkalmazás használ a rugalmas adatbázis-tranzakciók koordinálja a módosításokat adatbázis között, és biztosítja az atomitást.
* Szilánkokra osztott adatbázis-alkalmazások az Azure-ban: Ebben a forgatókönyvben az adatréteg használja a [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md) és önkiszolgáló-horizontális particionálás az adatok horizontális particionálásához az SQL dB-ben több adatbázis közötti. Egy neves használati eset szükség, ha módosításokat span bérlők atomi módosításokat végrehajtani a horizontálisan skálázott több-bérlős alkalmazás. Úgy gondolja, hogy például a másikra, két különböző adatbázisok elhelyezkedhet egyetlen bérlő át. Egy másik eset, minden részletre kiterjedő horizontális skálázási egy nagy-bérlőjéhez, amely viszont általában azt jelenti, hogy néhány atomi művelet kell használni a ugyanazt bérlőhöz tartozó több adatbázis között a Stretch kapacitásszükségletének befogadásához. Egy harmadik funkcióban atomi frissítései adatbázisok közötti replikált adatokra hivatkoznak. Ezek a sorok mentén atomi, tranzakciós, művelet most is koordinált az előzetes verziójának több adatbázis között.
  Rugalmas adatbázis-tranzakciók használja kétfázisú végrehajtási tranzakció atomitást adatbázis között. Egy jó megoldás lehet kisebb, mint 100 adatbázisnak egyszerre egy tranzakción belül érintő tranzakciók. Ezek a korlátok nem kényszerítettek, de az egyik teljesítmény- és a sikerességi arányokat, ha ezek a korlátok túllépése esetén csökkenhet a rugalmas tranzakciók számíthat.

## <a name="installation-and-migration"></a>Telepítés és áttelepítés
SQL DB rugalmas adatbázis-tranzakciók képességeit, a .NET-kódtárakra System.Data.dll és System.Transactions.dll frissítések állnak rendelkezésre. A DLL-ek győződjön meg arról, hogy kétfázisú végrehajtási atomitást ellenőrizze, hogy szükség esetén használja. Indítsa el a rugalmas adatbázis-tranzakciók használata alkalmazások fejlesztéséhez, telepítse a [.NET-keretrendszer 4.6.1-es](https://www.microsoft.com/download/details.aspx?id=49981) vagy újabb verziója. Ha a .NET-keretrendszer korábbi verzióját futtatja, tranzakciók előléptetni egy elosztott tranzakció sikertelen lesz, és kivételt a rendszer generál.

A telepítés után is használhatja az elosztott tranzakció API-k System.Transactions a kapcsolatokkal az SQL DB-hez. Ha rendelkezik meglévő API-k használatával MSDTC-alkalmazások, egyszerűen építse újra a meglévő .NET 4.6-alkalmazások a 4.6.1 telepítése után keretrendszer. Ha a projektek .NET 4.6, automatikusan fogja használni a frissített DLL-eket az új keretrendszer verziója, és API-hívások és az SQL DB kapcsolatok együttes alkalmazásával elosztott tranzakció már sikeres lesz.

Ne feledje, hogy a rugalmas adatbázis-tranzakciók nem igényelnek MSDTC telepítése. Rugalmas adatbázis-tranzakciók ehelyett közvetlenül kezelt által és az SQL DB belül. Ez jelentősen leegyszerűsíti a felhőbeli forgatókönyvek mivel MSDTC üzembe helyezése nem szükséges, az elosztott tranzakciók és az SQL Database. 4. szakasz részletesen ismerteti a rugalmas adatbázis-tranzakciók és a szükséges .NET-keretrendszer és a felhőalapú alkalmazások Azure-ban üzembe helyezése.

## <a name="development-experience"></a>Fejlesztői élmény
### <a name="multi-database-applications"></a>Több adatbázis-alkalmazások
Az alábbi mintakód .NET System.Transactions a megszokott programozási környezetet használ. Objekt TransactionScope není osztály hozza létre a .NET-ben egy környezeti tranzakció. ("Környezeti tranzakció" egyike az aktuális szál.) Összes belül a TransactionScope megnyitott kapcsolatainak száma a tranzakcióban részt. Ha a különböző adatbázisok részt venni, a tranzakció automatikusan át elosztott tranzakcióra emelt szintű. A tranzakció eredményét jelzi a véglegesítés befejeződik a hatókör beállításáról vezérli.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Szilánkokra osztott adatbázis-alkalmazások
Az SQL Database rugalmas adatbázis-tranzakciók is támogatja az elosztott tranzakciók, ahol a OpenConnectionForKey módszert használja, az elastic database-ügyfélkódtár nyissa meg a kapcsolatok egy bővítő adatok koordinációt szint. Fontolja meg az esetekben, ahol módosításokat tranzakció-konzisztencia biztosításához között számos különböző horizontális skálázási kulcs értékét. A szegmensek a különböző horizontális skálázási kulcs értékeit üzemeltetési kapcsolat van közvetítőalapú OpenConnectionForKey használatával. A kapcsolatok az általános esetben lehet a különböző szegmensekhez úgy, hogy a tranzakciós garanciát biztosító elosztott tranzakciót igényel. Az alábbi kódmintában ezt a megközelítést mutatja be. Feltételezi, hogy shardmap nevű változóra, amely képviseli horizontálispartíció-térkép a rugalmas adatbázis ügyfélkönyvtárának szolgál:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>A .NET telepítése az Azure Cloud Services
Az Azure .NET-alkalmazások üzemeltetése több ajánlatok biztosít. A különböző ajánlatok összehasonlítása megtalálható [Azure App Service-ben, Cloud Services és Virtual Machines összevetése](../app-service/choose-web-site-cloud-service-vm.md). Ha a vendég operációs rendszer az ajánlat kisebb, mint a .NET 4.6.1 rugalmas tranzakciók szükséges, a vendég operációs rendszer frissítése a 4.6.1 szüksége. 

Az Azure App Services a vendég operációs rendszer frissítése jelenleg nem támogatottak. Az Azure Virtual Machines, egyszerűen csak jelentkezzen be a virtuális Gépre, és futtassa a telepítőt a legújabb .NET-keretrendszer. Az Azure Cloud Services az üzembe helyezés az indítási feladatok, például újabb verzióját kell. A fogalmakat és lépéseket vannak dokumentálva [.NET telepítése egy Cloud Service szerepkör](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Vegye figyelembe, hogy a .NET 4.6.1 a telepítő az Azure cloud services, mint a telepítő .NET 4.6 a bootstrapping során előfordulhat, hogy szüksége további ideiglenes tárhely. A sikeres telepítés érdekében szüksége az Azure-felhőszolgáltatásban a LocalResources szakaszban és az indítási feladat a környezeti beállítások a ServiceDefinition.csdef fájlban az ideiglenes tárhely bővítése érdekében az alábbi mintában látható módon:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>Tranzakciók több kiszolgáló között
Rugalmas adatbázis-tranzakciók több különböző logikai kiszolgálók az Azure SQL Database-ben is támogatott. Tranzakciók közötti logikai kiszolgáló határokat, amikor a programban részt vevő kiszolgálók először kölcsönös kommunikációs kapcsolat adható meg. A kommunikációs kapcsolat létrejöttét követően a két kiszolgáló bármelyik adatbázisához rugalmas tranzakciók az adatbázis a kiszolgálótól vehet részt. A tranzakciók több mint két logikai kiszolgálók átfedés kommunikációs kapcsolat kell lennie minden olyan logikai kiszolgálók két helyen.

A következő PowerShell-parancsmagok segítségével kezelheti a kiszolgálók közötti kommunikáció kapcsolatokat az a rugalmas adatbázis-tranzakciók:

* **Új AzureRmSqlServerCommunicationLink**: Ez a parancsmag segítségével hozzon létre egy új kommunikációs kapcsolatot két Azure SQL Database logikai kiszolgáló között. A kapcsolat a szimmetrikus ami azt jelenti, hogy mindkét kiszolgálón is kezdeményezhető a tranzakciók a kiszolgálóval.
* **Get-AzureRmSqlServerCommunicationLink**: Ez a parancsmag használatával lekérheti az meglévő kommunikációs kapcsolatok és azok tulajdonságait.
* **Remove-AzureRmSqlServerCommunicationLink**: Ez a parancsmag segítségével távolítsa el a meglévő kommunikációs kapcsolat. 

## <a name="monitoring-transaction-status"></a>Tranzakció állapotának figyelése
Az SQL DB dinamikus felügyeleti nézetekkel (DMV-kkel) használatával a figyelő és a rugalmas adatbázisban folyamatban lévő tranzakciók állapotáról. Az SQL dB-ben az elosztott tranzakciók összes DMV-vel kapcsolatos tranzakciók szükségesek. Dinamikus felügyeleti nézetek megfelelő listáját itt találja: [tranzakció kapcsolódó dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

A dinamikus felügyeleti nézetek különösen hasznosak:

* **sys.DM\_tran\_aktív\_tranzakciók**: a jelenleg aktív tranzakciók és állapotukat sorolja fel. A UOW-Értékkel (munkaegység) oszlop azonosíthatja a különböző alárendelt tranzakciók, amely ugyanazon az elosztott tranzakció tartozik. Az azonos, elosztott tranzakción belül minden tranzakció végrehajtása UOW-Értékkel azonos értéket. Tekintse meg a [DMV-dokumentáció](https://msdn.microsoft.com/library/ms174302.aspx) további információt.
* **sys.DM\_tran\_adatbázis\_tranzakciók**: tranzakció, például a naplóban lévő tranzakció elhelyezését további információkat tartalmaz. Tekintse meg a [DMV-dokumentáció](https://msdn.microsoft.com/library/ms186957.aspx) további információt.
* **sys.DM\_tran\_zárolások**: azt ismerteti, amely jelenleg nincsen lekötve folyamatban lévő tranzakciók által a zárolása. Tekintse meg a [DMV-dokumentáció](https://msdn.microsoft.com/library/ms190345.aspx) további információt.

## <a name="limitations"></a>Korlátozások
Jelenleg a következő korlátozások vonatkoznak az SQL DB rugalmas adatbázis-tranzakciók:

* Csak a tranzakciók több adatbázisban az SQL dB-ben támogatottak. Más [X / nyílt XA](https://en.wikipedia.org/wiki/X/Open_XA) erőforrás-szolgáltatókat és adatbázisok kívül az SQL DB rugalmas adatbázis-tranzakciók nem szerepelhet. Ez azt jelenti, hogy a rugalmas adatbázis-tranzakciók nem terjeszthet ki keresztül a helyszíni SQL Server és az Azure SQL Database. Az elosztott tranzakciók a helyszínen továbbra is használhatja az MSDTC. 
* Csak ügyfél koordinált .NET-alkalmazásból tranzakciók használata támogatott. T-SQL, például az ELOSZTOTT TRANZAKCIÓ megkezdése kiszolgálóoldali támogatása érhető el a tervezett, de még nem érhető el. 
* WCF-szolgáltatások közötti tranzakciók nem támogatottak. Például hogy egy WCF-szolgáltatás metódus, amely végrehajtja a tranzakciót. A hívás a tranzakció hatókörén belül befoglaló sikertelen lesz egy [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>További lépések
Ha kérdése van, vegye fel velünk a kapcsolatot a a [SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a funkciókérések, adja hozzá őket a [SQL Database-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



