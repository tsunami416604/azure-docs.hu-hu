---
title: Elosztott tranzakciók több felhőalapú adatbázisban
description: Az Azure SQL Database rugalmas adatbázis-tranzakciók áttekintése
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: article
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 37de2a6918012707ae171415ac8f043d6f9cb1ae
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="distributed-transactions-across-cloud-databases"></a>Elosztott tranzakciók több felhőalapú adatbázisban
Az Azure SQL Database (SQL-adatbázis a) rugalmas adatbázis-tranzakciók teszik tranzakciók, amelyek több adatbázisok az SQL-adatbázis futtatását. Az SQL-adatbázis a rugalmas adatbázis-tranzakciók érhetők el a .NET-alkalmazásokban ADO .NET használatával, és a megszokott programozási élmény segítségével integrálja a [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) osztályok. Ahhoz, hogy a könyvtárban, lásd: [.NET-keretrendszer 4.6.1 (webes telepítő)](https://www.microsoft.com/download/details.aspx?id=49981).

A helyszíni ilyen esetben általában szükséges fut a Microsoft elosztott tranzakciók koordinátora (MSDTC). Azure Platform,--szolgáltatás alkalmazásának MSDTC nem érhető el, mert képes koordinálni elosztott tranzakciók mostantól közvetlenül integrálva lett SQL-adatbázis. Alkalmazások is csatlakoznak bármely SQL-adatbázis indítsa el az elosztott tranzakciók, és egy adatbázis transzparens módon összehangolják elosztott tranzakció, az alábbi ábrán látható módon. 

  ![Az elosztott tranzakciók az Azure SQL Database rugalmas adatbázis-tranzakció használatával ][1]

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
Az SQL Database rugalmas adatbázis tranzakciók lehetővé teszik az alkalmazások számos különböző SQL-adatbázisban tárolt adatok atomi módosítja. Az előzetes ügyféloldali fejlesztés élmény a C# és .NET összpontosít. A T-SQL használatával kiszolgálóoldali élmény tervezünk-e egy későbbi időpontra.  
A rugalmas adatbázis-tranzakció célozza az alábbi esetekben:

* Több adatbázis-alkalmazások az Azure-ban: Ebben a forgatókönyvben az adatok függőleges particionálása SQL DB több adatbázisok között úgy, hogy a különböző típusú adatok legyen elhelyezve a különböző adatbázisokhoz. Egyes műveletek szükséges adatok, amelyet a két vagy több adatbázis módosításait. Az alkalmazás rugalmas adatbázis-tranzakció koordinálja a módosításokat az adatbázisok közötti, és atomicity használja.
* Horizontálisan skálázott adatbázis-alkalmazások az Azure-ban: Ebben a forgatókönyvben az adatréteg használ a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) vagy önkiszolgáló-horizontális vízszintesen particionálásához az adatokat az Adatbázisba az SQL több adatbázis közötti. Egy jól láthatóan elhelyezett használati eset atomi változtatások végrehajtani szilánkos több-bérlős alkalmazáshoz, ha módosítások span bérlők szükség. Egy bérlő átvitelét egy másikra, mind a különböző adatbázisokhoz elhelyezkedhet gondol példányhoz. A második esetben az részletes horizontális lemezkapacitási igényekről, ami viszont általában azt jelenti, hogy néhány atomi műveletek között használt ugyanannak a bérlőnek több adatbázisok Nyújtás kell-e nagy bérlőhöz befogadásához. Egy harmadik esetben atomi frissítéseit az adatbázisok közötti replikált adatokra hivatkoztak. Ezek a sorok mentén atomi, a tranzakciós, műveletek most már az előzetes több adatbázis közötti kell koordinált.
  A rugalmas adatbázis-tranzakció használja kétfázisú végrehajtási tranzakció atomicity adatbázisok között. Remekül beválik, ha egy tranzakción belül egyszerre legfeljebb 100 adatbázisok tranzakciók is. Ezek a korlátozások nem lépnek érvénybe, de egy számíthat teljesítményt és rugalmas adatbázis tranzakciók a működés felső korlátjának túllépésekor küzdenek sikerességéről.

## <a name="installation-and-migration"></a>Telepítés és áttelepítés
Az SQL Database rugalmas adatbázis-tranzakciókhoz képességeket keresztül a .NET-kódtárakra System.Data.dll és System.Transactions.dll frissítések állnak rendelkezésre. A DLL-ek győződjön meg arról, hogy kétfázisú végrehajtási használatos, ahol atomicity biztosításához szükséges. Rugalmas adatbázis-tranzakció használatával alkalmazások fejlesztésének megkezdéséhez telepítse [.NET-keretrendszer 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) vagy újabb verziója. A .NET-keretrendszer egy korábbi verzióját futtatja, ha tranzakció elosztott tranzakcióvá történő előléptetése sikertelen lesz, és kivételt generál.

A telepítés után az elosztott tranzakció API-k használhatja a System.Transactions kapcsolatokkal az SQL-Adatbázishoz. Ha ezen API-k használata meglévő MSDTC alkalmazásai, egyszerűen építse újra a meglévő .NET 4.6-alkalmazások a 4.6.1 telepítése után keretrendszer. A projektek .NET 4.6 célként, ha azok automatikusan használja az új keretrendszer verzióját a frissített DLL-eket, és API és az SQL Adatbázishoz kapcsolatok együttes meghívja az elosztott tranzakciók mostantól sikeres lesz.

Ne feledje, hogy a rugalmas adatbázis-tranzakció szükséges MSDTC telepítése. Rugalmas adatbázis-tranzakció ehelyett közvetlenül kezelt által és az SQL DB belül. Ez jelentősen leegyszerűsíti az felhős rendszerekben mivel MSDTC telepítése nem szükséges az elosztott tranzakciók használata az SQL-adatbázis. A szakasz 4 részletesebben ismerteti, hogyan rugalmas adatbázis-tranzakció és a kötelező .NET-keretrendszer együtt az Azure felhőalapú alkalmazások központi telepítését.

## <a name="development-experience"></a>Fejlesztési felület
### <a name="multi-database-applications"></a>Több adatbázis-alkalmazások
Az alábbi példakód .NET System.Transactions a megszokott programozási környezetet használ. A TransactionScope-objektum osztály a .NET környezeti tranzakció hoz létre. ("Környezeti tranzakció" egyike az aktuális szál.) A TransactionScope-objektum belül megnyitott összes kapcsolat a tranzakció részt. Ha a különböző adatbázisokhoz, a tranzakció automatikusan át elosztott tranzakcióra emelt szintű. A tranzakció eredményét jelzi egy véglegesítési befejezéséhez hatókör beállítása szabályozza.

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

### <a name="sharded-database-applications"></a>Horizontálisan skálázott adatbázis-alkalmazások
Az SQL Database rugalmas adatbázis tranzakciók is támogatja az elosztott tranzakciók, ahol a OpenConnectionForKey a módszert használja az elastic database ügyféloldali kódtár nyitott kapcsolatot méretezett kimenő adatok koordinációs réteg. Vegye figyelembe a esetben, amikor kell között számos különböző horizontális kulcsértékei módosítások tranzakciós konzisztencia biztosításához. A szilánkok különböző horizontális értékek üzemeltetési kapcsolat van közvetítőalapú OpenConnectionForKey használatával. A kapcsolatok az általános esetben lehet a különböző szilánkok úgy, hogy tranzakciós garanciák biztosítása elosztott tranzakciót igényel. A következő példakód azt szemlélteti, hogy ezt a módszert használja. Feltételezi, hogy használja-e shardmap nevű változó, amely képviseli az elastic database ügyféloldali kódtár shard térképre:

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


## <a name="net-installation-for-azure-cloud-services"></a>A .NET telepítése Azure Cloud Services csomag
Azure biztosít a .NET-alkalmazások üzemeltetését több ajánlatokat. A különböző ajánlatok összehasonlítása érhető el [Azure App Service, a Cloud Services és a virtuális gépek összevetése](../app-service/choose-web-site-cloud-service-vm.md). Ha a vendég operációs rendszer, az elérhető .NET 4.6.1 rugalmas tranzakciók szükséges kisebb, a vendég operációs rendszer frissítéséhez 4.6.1 szüksége. 

Az Azure App Service szolgáltatások frissítéseket a vendég operációs rendszer jelenleg nem támogatottak. Az Azure Virtual Machines, egyszerűen jelentkezzen be a virtuális Gépet, majd futtassa a telepítőt a legújabb .NET-keretrendszer. Azure-szolgáltatásokhoz meg kell adnia azokat a központi telepítés indítási feladatainak egy újabb .NET telepítése. A fogalmakat és lépéseket ismertetett [felhőalapú szolgáltatás szerepkör telepítése .NET](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Vegye figyelembe, hogy a telepítő a .NET 4.6.1 során szüksége lehet további ideiglenes tárolási a bootstrapping az Azure felhőszolgáltatások mint a telepítő a .NET 4.6. A sikeres telepítés érdekében növelnie kell ideiglenes tárhely az Azure felhőalapú szolgáltatás a LocalResources szakaszban és a környezet beállításokat az indítási feladat a ServiceDefinition.csdef fájlban a következő mintában látható módon:

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
A rugalmas adatbázis-tranzakció támogatottak az Azure SQL Database különböző logikai kiszolgáló között. Ha tranzakciók logikai kiszolgáló határokat, a programban részt vevő kiszolgálók először kell kölcsönös kommunikációs kapcsolatot kell adni. A kommunikációs kapcsolat létrehozása után a másik kiszolgáló az adatbázisok rugalmas tranzakciók egyetlen adatbázis sem a két kiszolgáló részt. Több mint két logikai kiszolgáló átfedés tranzakciókkal egy kommunikációs viszonyt kell biztosítani a logikai kiszolgáló bármely két.

A következő PowerShell-parancsmagok segítségével kezelheti a kiszolgálók közötti kommunikációhoz kapcsolatok rugalmas adatbázis-tranzakciókhoz:

* **Új AzureRmSqlServerCommunicationLink**: Ez a parancsmag segítségével hozzon létre egy új kommunikációs kapcsolat Azure SQL Database logikai két kiszolgálója között. A kapcsolat szimmetrikus ami azt jelenti, hogy mindkét kiszolgáló a másik kiszolgálóval tranzakciók is kezdeményezhető.
* **Get-AzureRmSqlServerCommunicationLink**: ezt a parancsmagot használja a meglévő kommunikációs kapcsolatok és azok tulajdonságaival lekérdezéséhez.
* **Remove-AzureRmSqlServerCommunicationLink**: Ez a parancsmag segítségével távolítsa el a meglévő kommunikációs kapcsolat. 

## <a name="monitoring-transaction-status"></a>Tranzakció állapotának figyelése
Az SQL-adatbázis a dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek) segítségével a figyelő állapotának és előrehaladtának a folyamatban lévő rugalmas adatbázis-tranzakció. Az SQL-adatbázis az elosztott tranzakciók tranzakciókhoz kapcsolódó összes dinamikus felügyeleti nézetek szükségesek. Dinamikus felügyeleti nézetek megfelelő listáját itt találja: [tranzakció kapcsolódó dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

A dinamikus felügyeleti nézetek különösen hasznosak:

* **sys.DM\_tran\_aktív\_tranzakciók**: a jelenleg aktív tranzakciók és azok állapotát tartalmazza. A UOW-Értékkel (munkaegység) oszlop azonosíthatja a különböző alárendelt tranzakciók, amelyek azonos elosztott tranzakció. Ugyanazon elosztott tranzakción belül minden tranzakciók lebonyolítására UOW ugyanazt az értéket. Tekintse meg a [DMV dokumentáció](https://msdn.microsoft.com/library/ms174302.aspx) további információt.
* **sys.DM\_tran\_adatbázis\_tranzakciók**: tranzakciók, például a naplóban a tranzakció elhelyezésének további információkkal szolgál. Tekintse meg a [DMV dokumentáció](https://msdn.microsoft.com/library/ms186957.aspx) további információt.
* **sys.DM\_tran\_zárolások**: információkat nyújt azokról a zárolásokat, amelyek jelenleg folyamatban lévő tranzakció tartja fenn. Tekintse meg a [DMV dokumentáció](https://msdn.microsoft.com/library/ms190345.aspx) további információt.

## <a name="limitations"></a>Korlátozások
SQL Database rugalmas adatbázis-tranzakciók jelenleg alkalmazása a következő korlátozások vonatkoznak:

* Csak az SQL-adatbázis az adatbázisok közötti tranzakciók támogatottak. Más [X / nyissa meg a XA](https://en.wikipedia.org/wiki/X/Open_XA) rugalmas adatbázis-tranzakció erőforrás-szolgáltatók és az adatbázisok SQL DB kívül nem szerepelhet. Ez azt jelenti, hogy a rugalmas adatbázis-tranzakció nem a a helyszíni SQL Server és az Azure SQL-adatbázisok között stretch. Az elosztott tranzakciók a helyszíni továbbra is használhatja az MSDTC. 
* Csak ügyfél egyeztetett .NET-alkalmazás tranzakciók használata támogatott. T-SQL, például a BEGIN TRANSACTION ELOSZTOTT kiszolgálóoldali támogatása, tervezett, de még nem érhető el. 
* WCF-szolgáltatások között nem támogatottak. Például hogy egy WCF-szolgáltatás metódus, amely végrehajtja a tranzakciót. A hívás a tranzakció hatókörén belül befoglaló meghiúsul, mint egy [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>További lépések
A kérdésekhez, lépjen kapcsolatba velünk a a [SQL-adatbázis fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a szolgáltatás kéréseket, adja hozzá őket a [SQL adatbázis-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



