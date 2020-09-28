---
title: Elosztott tranzakciók felhőalapú adatbázisok között (előzetes verzió)
description: A Azure SQL Databaseokkal Elastic Database tranzakciók áttekintése.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 60f6863bbe051338308c30e22c6969d84670dc64
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409731"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Elosztott tranzakciók felhőalapú adatbázisok között (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database rugalmas adatbázis-tranzakciói lehetővé teszik, hogy olyan tranzakciókat futtasson, amelyek SQL Database több adatbázisára is kiterjednek. A SQL Database rugalmas adatbázis-tranzakciói elérhetők a ADO.NET-t használó .NET-alkalmazások számára, és a [rendszer. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) osztályok használatával integrálva vannak a megszokott programozási felülettel. A könyvtár beszerzéséhez tekintse meg a [.NET-keretrendszer 4.6.1 (web Installer)](https://www.microsoft.com/download/details.aspx?id=49981)című témakört.

A helyszínen egy ilyen forgatókönyvhöz általában a Microsoft Elosztott tranzakciók koordinátora (MSDTC) futtatására van szükség. Mivel az MSDTC nem érhető el az Azure-beli platform-szolgáltatás alkalmazáshoz, az elosztott tranzakciók koordinálásának lehetősége mostantól közvetlenül a SQL Databaseba integrálható. Az alkalmazások a SQL Database bármely adatbázisához csatlakozhatnak az elosztott tranzakciók elindításához, és az egyik adatbázis transzparens módon koordinálja az elosztott tranzakciót, ahogy az az alábbi ábrán is látható.

  ![Elosztott tranzakciók Azure SQL Database rugalmas adatbázis-tranzakciók használatával ][1]

## <a name="common-scenarios"></a>Gyakori helyzetek

A SQL Database rugalmas adatbázis-tranzakciói lehetővé teszik az alkalmazások számára, hogy az SQL Database számos különböző adatbázisában tárolt adatváltozások atomi módosításait. Az előzetes verzió a C# és a .NET ügyféloldali fejlesztési tapasztalataira koncentrál. A T-SQL-T használó kiszolgálóoldali élmény egy későbbi időpontra van tervezve.  
A rugalmas adatbázis-tranzakciók a következő forgatókönyveket célozzák meg:

* Több adatbázisból álló alkalmazások az Azure-ban: ebben a forgatókönyvben az adat függőlegesen több adatbázisba van particionálva SQL Database úgy, hogy a különböző adattípusok különböző adatbázisokban találhatók. Néhány művelethez szükség van az adatok módosítására, amelyet két vagy több adatbázisban tartanak. Az alkalmazás rugalmas adatbázis-tranzakciókat használ az adatbázisok változásainak koordinálására és az atomenergia biztosítására.
* Az Azure-ban felhasználható adatbázis-alkalmazások: ebben a forgatókönyvben az adatréteg az [Elastic Database ügyféloldali kódtárat](elastic-database-client-library.md) használja, vagy az önálló horizontálisan particionálja az adatmennyiséget a SQL Database számos adatbázisa között. Az egyik kiemelt használati eset az, hogy szükség van egy több-bérlős, több-bérlős alkalmazásra vonatkozó atomi módosítások végrehajtására, ha a változások a bérlőket használják. Gondolja át, hogy az egyik bérlőről a másikra, a különböző adatbázisokban lévő példányokra is vonatkozik. Egy második eset a nagy bérlők kapacitási igényeinek kielégítésére szolgáló részletes horizontális felskálázás, ami általában azt jelenti, hogy egyes atomi műveleteknek több adatbázisra kell kiterjeszteniük az ugyanahhoz a bérlőhöz használt adatbázisok között. A harmadik esetben az olyan atomi frissítések, amelyek az adatbázisok között replikált információkra vonatkoznak. Az Atomic, a transacted, a következő vonalak mentén végzett műveletek mostantól több adatbázison is összehangolható az előzetes verzió használatával.
  A rugalmas adatbázis-tranzakciók kétfázisú végrehajtással biztosítják a tranzakciók atomi használatát az adatbázisok között. Ez egy olyan tranzakció, amely egy adott tranzakción belül egyszerre kevesebb mint 100 adatbázist tartalmaz. Ezek a korlátok nem érvényesülnek, de az egyiknek a teljesítmény és a sikerességi arányt kell várnia ahhoz, hogy a rugalmas adatbázis-tranzakciók a határértékek meghaladása esetén

## <a name="installation-and-migration"></a>Telepítés és áttelepítés

A SQL Database rugalmas adatbázis-tranzakcióinak képességei a .NET-kódtárak System.Data.dll és System.Transactions.dll frissítésein keresztül érhetők el. A DLL-ek biztosítják, hogy a kétfázisú véglegesítés szükség esetén legyen használatban az atomenergia biztosításához. A rugalmas adatbázis-tranzakciókat használó alkalmazások fejlesztésének megkezdéséhez telepítse a [.NET-keretrendszer 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) -es vagy újabb verzióját. Ha a .NET-keretrendszer egy korábbi verzióját futtatja, a tranzakciókat nem lehet előléptetni egy elosztott tranzakcióra, és a rendszer kivételt eredményez.

A telepítés után az elosztott tranzakciós API-kat használhatja a System. Transactions szolgáltatásban, SQL Database kapcsolatokkal. Ha ezen API-kat használó meglévő MSDTC-alkalmazásokkal rendelkezik, egyszerűen építse újra a meglévő alkalmazásokat a .NET 4,6-es verzióra a 4.6.1-keretrendszer telepítése után. Ha a projektek a .NET 4,6-es verzióját célozzák meg, akkor a rendszer automatikusan a frissített DLL-eket használja az új keretrendszer-verzió és az elosztott tranzakciók API-hívásai között, és a SQL Database kapcsolatokkal együtt sikeres lesz.

Ne feledje, hogy a rugalmas adatbázis-tranzakciók nem igénylik az MSDTC telepítését. Ehelyett a rugalmas adatbázis-tranzakciókat közvetlenül a és a SQL Database felügyeli. Ez jelentősen leegyszerűsíti a Felhőbeli forgatókönyveket, mivel az MSDTC telepítése nem szükséges az elosztott tranzakciók SQL Database használatával történő használatához. A 4. szakasz részletesen ismerteti, hogyan helyezhetők üzembe rugalmas adatbázis-tranzakciók és a szükséges .NET-keretrendszer a felhőalapú alkalmazásokkal együtt az Azure-ban.

## <a name="development-experience"></a>Fejlesztői élmény

### <a name="multi-database-applications"></a>Több adatbázisból álló alkalmazások

A következő mintakód a megszokott programozási felületet használja a .NET System. Transactions használatával. A TransactionScope osztály egy környezeti tranzakciót hoz létre a .NET-ben. (A "környezeti tranzakció" olyan, amely az aktuális szálban él.) A TransactionScope belül megnyitott összes kapcsolat részt vesz a tranzakcióban. Ha a különböző adatbázisok részt vesznek, a tranzakció automatikusan egy elosztott tranzakcióhoz lesz felemelve. A tranzakció eredményét úgy szabályozza, hogy a hatókört a Befejezés értékre állítja, hogy véglegesítse a műveletet.

```csharp
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
```

### <a name="sharded-database-applications"></a>Szilánkokra osztott adatbázis-alkalmazások

A SQL Database rugalmas adatbázis-tranzakciói olyan elosztott tranzakciók koordinálását is támogatják, amelyekben a rugalmas adatbázis-ügyféloldali kódtár OpenConnectionForKey metódusát használja a kibővíthető adatcsomag kapcsolatainak megnyitására. Tekintse át azokat az eseteket, ahol a több különböző horizontális Felskálázási kulcsban végrehajtott változások tranzakciós konzisztenciájának garantálása szükséges. A különböző horizontális Felskálázási kulcsokat üzemeltető szegmensekkel létesített kapcsolatok a OpenConnectionForKey használatával vannak közvetítve. Az általános esetben a kapcsolatok különböző szegmensekben lehetnek, így a tranzakciós garanciák biztosításához elosztott tranzakció szükséges.
A következő mintakód ezt a megközelítést mutatja be. Azt feltételezi, hogy egy shardmap nevű változót használ a rugalmas adatbázis ügyféloldali könyvtárának egy szegmenses térképének ábrázolására:

```csharp
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
```

## <a name="net-installation-for-azure-cloud-services"></a>.NET-telepítés az Azure Cloud Services

Az Azure számos ajánlatot biztosít a .NET-alkalmazások üzemeltetéséhez. A különböző ajánlatok összehasonlítása [Azure app Service, Cloud Services és Virtual Machines összehasonlításban](/azure/architecture/guide/technology-choices/compute-decision-tree)érhető el. Ha az ajánlat vendég operációs rendszere kisebb, mint a rugalmas tranzakciók esetében szükséges .NET 4.6.1, a vendég operációs rendszert a 4.6.1-es verzióra kell frissíteni.

Azure App Service a vendég operációs rendszerre való frissítés jelenleg nem támogatott. Az Azure Virtual Machines esetében egyszerűen jelentkezzen be a virtuális gépre, és futtassa a telepítőt a legújabb .NET-keretrendszerhez. Az Azure Cloud Services esetében telepítenie kell egy újabb .NET-verziót az üzemelő példány indítási feladataiba. A fogalmakat és a lépéseket a [.NET telepítése Cloud Service-szerepkörre](../../cloud-services/cloud-services-dotnet-install-dotnet.md)című témakör ismerteti.  

Vegye figyelembe, hogy a .NET 4.6.1-es verziójának az Azure Cloud Services rendszerindítási folyamata során több átmeneti tárterületre lehet szüksége, mint a .NET 4,6-es telepítő. A sikeres telepítés érdekében növelje az Azure Cloud Service ideiglenes tárhelyét a ServiceDefinition. csdef fájlban a LocalResources szakaszban, valamint az indítási feladat környezeti beállításait, ahogy az az alábbi példában is látható:

```xml
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
```

## <a name="transactions-across-multiple-servers"></a>Tranzakciók több kiszolgáló között

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A rugalmas adatbázis-tranzakciók a Azure SQL Database különböző kiszolgálóin is támogatottak. Ha a tranzakciók átlépik a kiszolgálók határait, a résztvevő kiszolgálókat először kölcsönös kommunikációs kapcsolatba kell beírni. A kommunikációs kapcsolat létrejötte után a két kiszolgáló bármelyik adatbázisa részt vehet a másik kiszolgáló adatbázisaival rendelkező rugalmas tranzakciókban. A kettőnél több kiszolgálót érintő tranzakciókkal a kiszolgálók bármelyike esetében kommunikációs kapcsolat szükséges.

Az alábbi PowerShell-parancsmagok segítségével kezelheti a többkiszolgálós kommunikációs kapcsolatokat a rugalmas adatbázis-tranzakciók esetében:

* **New-AzSqlServerCommunicationLink**: ezzel a parancsmaggal új kommunikációs kapcsolatot hozhat létre Azure SQL Database két kiszolgálója között. A kapcsolat szimmetrikus, ami azt jelenti, hogy mindkét kiszolgáló kezdeményezheti a tranzakciókat a másik kiszolgálóval.
* **Get-AzSqlServerCommunicationLink**: ezzel a parancsmaggal kérheti le a meglévő kommunikációs kapcsolatokat és azok tulajdonságait.
* **Remove-AzSqlServerCommunicationLink**: ezzel a parancsmaggal távolíthatja el a meglévő kommunikációs kapcsolatokat.

## <a name="monitoring-transaction-status"></a>Tranzakció állapotának figyelése

A folyamatban lévő rugalmas adatbázis-tranzakciók állapotának és előrehaladásának figyeléséhez SQL Database a dinamikus felügyeleti nézetek (DMV) használatát. A tranzakciókkal kapcsolatos összes DMV releváns a SQL Database elosztott tranzakciói esetében. Itt megtalálja a DMV megfelelő listáját: [tranzakcióval kapcsolatos dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Ezek a DMV különösen hasznosak:

* **sys.DM \_ Tran \_ aktív \_ tranzakciók**: felsorolja a jelenleg aktív tranzakciókat és azok állapotát. A UOW (munkaegység) oszlopban azonosíthatók azok a különböző alárendelt tranzakciók, amelyek ugyanahhoz az elosztott tranzakcióhoz tartoznak. Az azonos elosztott tranzakción belüli összes tranzakció ugyanazt a UOW-értéket hajtja végre. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms174302.aspx) talál.
* **sys.DM \_ Tran \_ adatbázis- \_ tranzakciói**: további információkat nyújt a tranzakciókkal kapcsolatban, például a tranzakció elhelyezéséről a naplóban. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms186957.aspx) talál.
* **sys.DM \_ Tran \_ zárolásai**: a folyamatban lévő tranzakciók által jelenleg tárolt zárolásokról nyújt információt. További információt a [DMV dokumentációjában](https://msdn.microsoft.com/library/ms190345.aspx) talál.

## <a name="limitations"></a>Korlátozások

A következő korlátozások jelenleg a rugalmas adatbázis-tranzakciókra vonatkoznak SQL Databaseban:

* Csak SQL Database adatbázisok közötti tranzakciók támogatottak. A SQL Databaseon kívüli egyéb [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) erőforrás-szolgáltatók és adatbázisok nem vehetnek részt rugalmas adatbázis-tranzakciókban. Ez azt jelenti, hogy a rugalmas adatbázis-tranzakciók nem terjedhetnek át a helyszíni SQL Serverra és Azure SQL Databasera. A helyszíni elosztott tranzakciók esetében folytassa az MSDTC használatát.
* Csak az ügyfél által koordinált tranzakciók támogatottak a .NET-alkalmazásokból. A T-SQL kiszolgálóoldali támogatása, például a BEGIN DISTRIBUTed TRANSACTION terv, de még nem érhető el.
* A WCF-szolgáltatások közötti tranzakciók nem támogatottak. Tegyük fel, hogy van egy WCF szolgáltatási metódusa, amely tranzakciót hajt végre. A hívás tranzakciós hatókörön belüli befoglalása sikertelen lesz, mint a [System. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Következő lépések

Ha kérdése van, kérjük, lépjen kapcsolatba velünk a [Microsoft Q&a SQL Database vonatkozó kérdés oldalán](https://docs.microsoft.com/answers/topics/azure-sql-database.html). A szolgáltatási kérelmek esetében vegye fel őket a [SQL Database visszajelzési fórumba](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 