---
title: Elosztott tranzakciók több felhőalapú adatbázisban
description: Rugalmas adatbázis-tranzakciók áttekintése az Azure SQL-adatbázissal
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68568286"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Elosztott tranzakciók több felhőalapú adatbázisban

Rugalmas adatbázis-tranzakciók az Azure SQL Database (SQL DB) lehetővé teszi, hogy több adatbázist az SQL DB-ben átnyúló tranzakciók futtatásához. Az SQL DB rugalmas adatbázis-tranzakciói az ADO .NET használatával érhetők el a .NET alkalmazásokhoz, és integrálhatók a [rendszer.tranzakció](https://msdn.microsoft.com/library/system.transactions.aspx) osztályokat használó ismerős programozási környezettel. A tár beolvassa a [.](https://www.microsoft.com/download/details.aspx?id=49981)

A helyszínen egy ilyen forgatókönyv általában a Microsoft Distributed Transaction Coordinator (MSDTC) futtatását igényeli. Mivel az MSDTC nem érhető el a Platform-as-a-Service alkalmazás hoz az Azure-ban, az elosztott tranzakciók koordinálásának lehetősége mostmár közvetlenül integrálva van az SQL DB-be. Az alkalmazások bármely SQL-adatbázishoz csatlakozhatnak az elosztott tranzakciók elindításához, és az adatbázisok egyike transzparens módon koordinálja az elosztott tranzakciót, ahogy az az alábbi ábrán látható. 

  ![Elosztott tranzakciók az Azure SQL Database-lel rugalmas adatbázis-tranzakciók használatával ][1]

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Az SQL DB rugalmas adatbázis-tranzakciói lehetővé teszik, hogy az alkalmazások atomi módosításokat hajtsanak végre a különböző SQL-adatbázisokban tárolt adatokon. Az előzetes verzió a C# és a .NET ügyféloldali fejlesztési élményére összpontosít. A T-SQL kiszolgálóoldali élményét egy későbbi időpontra tervezik.  
A rugalmas adatbázis-tranzakciók a következő eseteket célozzák meg:

* Többadatbázisos alkalmazások az Azure-ban: Ebben a forgatókönyvben az adatok függőlegesen particionálva több adatbázis sql DB úgy, hogy a különböző típusú adatok különböző adatbázisokban található. Egyes műveletek két vagy több adatbázisban tárolt adatok módosítását igénylik. Az alkalmazás rugalmas adatbázis-tranzakciókat használ a változások adatbázisok közötti koordinálásához és az atomiság biztosításához.
* Sharded database applications in Azure: With this scenario, the data tier uses the [Elastic Database client library](sql-database-elastic-database-client-library.md) or self-sharding to horizontally partition the data across many databases in SQL DB. Az egyik kiemelkedő használati eset az atomi módosítások végrehajtása egy szilánkos több-bérlős alkalmazás, ha a módosítások span bérlők. Gondoljon például egy átaz egyik bérlőről a másikra, mindkettő különböző adatbázisokban található. A második eset a részletes skálázás, hogy egy nagy bérlő kapacitásigényét, ami viszont általában azt jelenti, hogy egyes atomi műveleteket kell nyújtania az ugyanazon bérlő több adatbázison keresztül. A harmadik eset az adatbázisok között replikált referenciaadatok atomi frissítése. Atomi, tranzakciós, az ilyen vonalak mentén végzett műveletek mostantól több adatbázisban is koordinálhatók az előzetes verzió használatával.
  Rugalmas adatbázis-tranzakciók használata kétfázisú véglegesítése, hogy biztosítsa a tranzakció atomi adatbázisai között. Ez egy jó illeszkedés tanuskodik olyan tranzakciókhoz, amelyek egyetlen tranzakción belül egyszerre kevesebb mint 100 adatbázist foglalnak magukban. Ezek a korlátok nem vannak érvényesítve, de a rugalmas adatbázis-tranzakciók teljesítmény- és sikerességi aránya a korlátok túllépése esetén várható.

## <a name="installation-and-migration"></a>Telepítés és áttelepítés

Az SQL DB rugalmas adatbázis-tranzakcióinak képességeit a .NET libraries System.Data.dll és system.Transactions.dll frissítései biztosítják. A DL-ek biztosítják, hogy szükség esetén kétfázisú véglegesítést használjanak az atomiság biztosítása érdekében. A [.NET Framework 4.6.1-es](https://www.microsoft.com/download/details.aspx?id=49981) vagy újabb verzióval történő alkalmazások fejlesztésének megkezdéséhez telepítse a . Ha a .

A telepítés után használhatja az elosztott tranzakcióAPI-k System.Transactions kapcsolatokkal SQL DB. Ha már vannak MSDTC-alkalmazások ezen API-k használatával, egyszerűen építse újra a .NET 4.6 meglévő alkalmazásait a 4.6.1-es keretrendszer telepítése után. Ha a projektek a .NET 4.6-ot célozzák meg, akkor automatikusan az új keretrendszer verzióból származó frissített DEL-eket és az elosztott tranzakciós API-hívásokat és az SQL DB-hez való csatlakozással együtt sikeresek lesznek.

Ne feledje, hogy a rugalmas adatbázis-tranzakciók nem igénylik az MSDTC telepítését. Ehelyett a rugalmas adatbázis-tranzakciókat közvetlenül kezeli az SQL DB, és azSQL DB-n belül. Ez jelentősen leegyszerűsíti a felhőbeli forgatókönyveket, mivel az MSDTC telepítése nem szükséges az SQL DB-vel elosztott tranzakciók használatához. szakasz részletesebben ismerteti, hogyan telepítheti a rugalmas adatbázis-tranzakciókat és a szükséges .NET keretrendszert a felhőalapú alkalmazásokkal együtt az Azure-ba.

## <a name="development-experience"></a>Fejlesztési tapasztalat

### <a name="multi-database-applications"></a>Többadatbázisos alkalmazások

A következő mintakód a .NET System.Transactions programokkal kapcsolatos ismerős programozási élményt használja. A TransactionScope osztály környezeti tranzakciót hoz létre a .NET-ben. (A "környezeti tranzakció" az, amely az aktuális szálban él.) A TransactionScope-on belül megnyitott összes kapcsolat részt vesz a tranzakcióban. Ha különböző adatbázisok vesznek részt, a tranzakció automatikusan egy elosztott tranzakcióra emelkedik. A tranzakció kimenetelét úgy szabályozzák, hogy a véglegesítést jelző hatókört állítja be.

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

### <a name="sharded-database-applications"></a>Szilánkos adatbázis-alkalmazások

Az SQL DB rugalmas adatbázis-tranzakciói is támogatják az elosztott tranzakciók koordinálását, ahol a rugalmas adatbázis-ügyfélkódtár OpenConnectionForKey metódusával kapcsolatokat nyithat meg egy kibővített adatréteghez. Vegye figyelembe azokat az eseteket, amikor több különböző nagyítási kulcsérték-változási módosítások tranzakciós konzisztenciát kell garantálnia. A különböző szegmensek a különböző szegmensek kulcsértékeit tároló szegmensek kapcsolata az OpenConnectionForKey használatával történik. Az általános esetben a kapcsolatok különböző szegmensek, így a tranzakciós garanciák biztosítása esetén elosztott tranzakciót igényel. A következő kódminta ezt a megközelítést mutatja be. Feltételezi, hogy a shardmap nevű változó a rugalmas adatbázis-ügyfélkódtár shard leképezésének ábrázolására szolgál:

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


## <a name="net-installation-for-azure-cloud-services"></a>.NET telepítés az Azure Cloud Services-hez

Az Azure számos ajánlatot kínál a .NET alkalmazások üzemeltetéséhez. A különböző ajánlatok összehasonlítása az [Azure App Service, a Cloud Services és](/azure/architecture/guide/technology-choices/compute-decision-tree)a Virtuális gépek összehasonlításában érhető el. Ha az ajánlat vendég operációs rendszer kisebb, mint a .NET 4.6.1 rugalmas tranzakciók, frissítenie kell a vendég operációs rendszer 4.6.1. 

Az Azure App Services esetében a vendég operációs rendszer frissítései jelenleg nem támogatottak. Az Azure virtuális gépek, egyszerűen jelentkezzen be a virtuális gép, és futtassa a telepítőt a legújabb .NET keretrendszer. Az Azure Cloud Services esetében egy újabb .NET verzió telepítését kell tartalmaznia a központi telepítés indítási feladataiba. A fogalmakat és lépéseket a [.NET telepítése felhőkiszolgáló szerepkörre című](../cloud-services/cloud-services-dotnet-install-dotnet.md)témakörben ismerteti.  

Vegye figyelembe, hogy a .NET 4.6.1 telepítője több ideiglenes tárolást igényelhet az Azure felhőszolgáltatások rendszerindítási folyamata során, mint a .NET 4.6 telepítője. A sikeres telepítés biztosításához növelnie kell az Azure felhőszolgáltatás ideiglenes tárhelyét a ServiceDefinition.csdef fájlban a LocalResources szakaszban, valamint az indítási feladat környezeti beállításait, amint az az alábbi mintában látható:

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

## <a name="transactions-across-multiple-servers"></a>Több kiszolgálón végrehajtott tranzakciók

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Rugalmas adatbázis-tranzakciók az Azure SQL Database különböző SQL-adatbázis-kiszolgálókon támogatottak. Amikor a tranzakciók átlépik az SQL Database-kiszolgáló határait, a részt vevő kiszolgálókat először kölcsönös kommunikációs kapcsolatba kell lépni. A kommunikációs kapcsolat létrejötte után a két kiszolgáló bármelyikének bármely adatbázisa részt vehet rugalmas tranzakciókban a másik kiszolgáló adatbázisaival. Ha a tranzakciók kettőnél több SQL Database-kiszolgálót is felölelnek, kommunikációs kapcsolatnak kell lennie az SQL Database kiszolgálók bármely párja számára.

A következő PowerShell-parancsmagokkal kezelheti a kiszolgálók közötti kommunikációs kapcsolatokat rugalmas adatbázis-tranzakciókhoz:

* **New-AzSqlServerCommunicationLink**: Ezzel a parancsmagmal új kommunikációs kapcsolatot hozhat létre az Azure SQL Database két SQL Database-kiszolgálója között. A kapcsolat szimmetrikus, ami azt jelenti, hogy mindkét kiszolgáló kezdeményezhet tranzakciókat a másik kiszolgálóval.
* **Get-AzSqlServerCommunicationLink**: Ezzel a parancsmaggal lekérheti a meglévő kommunikációs kapcsolatokat és azok tulajdonságait.
* **Remove-AzSqlServerCommunicationLink**: Ezzel a parancsmagmal eltávolíthat egy meglévő kommunikációs kapcsolatot. 

## <a name="monitoring-transaction-status"></a>Tranzakció állapotának figyelése

Az SQL DB dinamikus felügyeleti nézeteivel (DMV- k) figyelheti a folyamatban lévő rugalmas adatbázis-tranzakciók állapotát és előrehaladását. A tranzakciókhoz kapcsolódó összes DMV releváns az SQL DB elosztott tranzakcióihoz. A DMV-k megfelelő listáját itt találja: [Tranzakciós dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Ezek a DMV-k különösen hasznosak:

* **sys.dm\_\_tran\_aktív tranzakciók**: A jelenleg aktív tranzakciók és állapotuk listázása. Az UOW (Munkaegység) oszlop azonosíthatja azokat a különböző alárendelt tranzakciókat, amelyek ugyanahhoz az elosztott tranzakcióhoz tartoznak. Az ugyanazon elosztott tranzakción belüli összes tranzakció ugyanazt az UOW értéket hordozza. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms174302.aspx) talál.
* **sys.dm\_\_tran\_adatbázis-tranzakciók:** További információkat tartalmaz a tranzakciókról, például a tranzakció elhelyezéséről a naplóban. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms186957.aspx) talál.
* **tran\_\_zárolások sys.dm:** A folyamatban lévő tranzakciók által jelenleg birtokolt zárolásokról nyújt tájékoztatást. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms190345.aspx) talál.

## <a name="limitations"></a>Korlátozások

A következő korlátozások jelenleg az SQL DB rugalmas adatbázis-tranzakcióira vonatkoznak:

* Csak az SQL DB adatbázisai közötti tranzakciók támogatottak. Más [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) erőforrás-szolgáltatók és az SQL DB-n kívüli adatbázisok nem vehetnek részt rugalmas adatbázis-tranzakciókban. Ez azt jelenti, hogy a rugalmas adatbázis-tranzakciók nem terjednek át a helyszíni SQL Server és az Azure SQL Database. A helyszíni elosztott tranzakciók esetén továbbra is használja az MSDTC-t. 
* Csak a .NET alkalmazás ügyféláltal koordinált tranzakciói támogatottak. A T-SQL kiszolgálóoldali támogatása, például a BEGIN DISTRIBUTED TRANSACTION, tervezett, de még nem érhető el. 
* A WCF-szolgáltatások közötti tranzakciók nem támogatottak. Például van egy WCF szolgáltatás metódusa, amely végrehajtja a tranzakciót. A tranzakcióhatókören belüli hívás lezárása [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>További lépések

Ha kérdése van, kérjük, forduljon hozzánk az [SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a funkció kéri, kérjük, add hozzá [azOKAT](https://feedback.azure.com/forums/217321-sql-database/)az SQL Database visszajelzés fórum .

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
