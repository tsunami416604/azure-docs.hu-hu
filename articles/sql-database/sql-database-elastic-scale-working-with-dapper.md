---
title: "A rugalmas adatbázis ügyféloldali kódtár használata Dapper |} Microsoft Docs"
description: "A rugalmas adatbázis ügyféloldali kódtár Dapper használja."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 463d2676-3b19-47c2-83df-f8c50492c9d2
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: f0efd37a39c1a60eee7b47304483c27727ca8833
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="using-elastic-database-client-library-with-dapper"></a>A rugalmas adatbázis ügyféloldali kódtár használata Dapper
Ez a dokumentum a fejlesztők olyan alkalmazásokat készíthetnek Dapper alapulnak, de is kívánja bevezetni a van [elastic database tooling](sql-database-elastic-scale-introduction.md) alkalmazásokat hozhat létre, hogy megvalósítása horizontális kibővített az adatréteg számára.  Ez a dokumentum Dapper-alapú alkalmazásokat, amelyek szükségesek a rugalmas adatbáziseszközöket integrálása változásait mutatja be. A fókusz értéke a rugalmas adatbázis shard felügyelete és az adatok függő összeállítása a útválasztással Dapper. 

**Példakód**: [rugalmas adatbáziseszközöket az Azure SQL Database - Dapper integrációs](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrálása **Dapper** és **DapperExtensions** a rugalmas adatbáziskészlettel könnyen az Azure SQL Database ügyféloldali kódtárára. Az alkalmazások használhatják a függő útválasztási módosítása a létrehozása és megnyitásakor az új adatok [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok használata a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) felelnek meg, és a [ügyféloldalikódtár](http://msdn.microsoft.com/library/azure/dn765902.aspx). Ezzel a módosítások csak az alkalmazás korlátozza, ahol az új kapcsolatok létrehozása és megnyitni. 

## <a name="dapper-overview"></a>Dapper áttekintése
**Dapper** egy objektum relációs leképező van. Leképezi a .NET-objektumokat az alkalmazás egy relációs adatbázisban (és fordítva). A mintakód első része azt mutatja be, hogyan integrálható a rugalmas adatbázis ügyféloldali kódtár Dapper-alapú alkalmazások. Második része a mintakód bemutatja, hogyan integrálásához Dapper és a DapperExtensions használatakor.  

Eseményleképező a Dapper biztosít a kiterjesztésmetódusok adatbázis kapcsolatok használata esetén, amelyek megkönnyítik a küldő T-SQL utasítás végrehajtása, vagy az adatbázis lekérdezése. Például Dapper megkönnyíti, hogy a .NET-objektumokat és az SQL-utasítás paramétereit közötti **Execute** hívások, vagy az SQL-lekérdezések eredményének felhasználásához .NET objektumokba **lekérdezés** Dapper hívásait. 

Ha DapperExtensions, már nincs szüksége arra, hogy az SQL-utasításokat. Bővítmények módszerek **GetList** vagy **beszúrása** keresztül az adatbázis-kapcsolat létrehozása az SQL-utasítások a háttérben.

Egy másik Dapper, és DapperExtensions előnye, hogy az alkalmazás szabályozza-e az adatbázis-kapcsolat létrehozását. Ez segít a brókerek adatbázis-kapcsolatok az adatbázisok shardlets a leképezésen alapuló rugalmas adatbázis ügyfél tár kommunikál.

Ahhoz, hogy a Dapper szerelvényeket, lásd: [Dapper pont nettó](http://www.nuget.org/packages/Dapper/). A Dapper bővítmények, lásd: [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>A rugalmas adatbázis ügyféloldali kódtár gyors áttekintése
A rugalmas adatbázis ügyféloldali kódtára a szolgáltatás megadhatja az alkalmazásadatok nevű partíciója *shardlets* adatbázisok való hozzárendelése és azonosításához által *horizontális kulcsok*. Tetszőleges számú adatbázist kell, és a shardlets szét ezeket az adatbázisokat is. Az adatbázisok horizontális kulcsértékei leképezése a szalagtár API-k által biztosított shard térkép tárolja. Ez a funkció neve **shard térkép felügyeleti**. A szilánkok térkép is szolgál az átvitelszervező adatbázis-kapcsolatok a kéréseket, amelyben egy horizontális skálázási kulcsot. Ez a funkció nevezzük **adatok függő útválasztási**.

![A shard leképezések és az adatok függő útválasztási][1]

A szilánkok térkép-kezelő felhasználók védi a inkonzisztens nézetek, amely akkor fordulhat elő, ha az adatbázisok hogy egyidejű shardlet felügyeleti műveletek shardlet adatokká. Ehhez a shard maps replikaszervező az adatbázis-kapcsolatok a szalagtár-val készült alkalmazás. A shard felügyeleti műveletek jelentős hatással lehet a shardlet, a shard leképezés funkció automatikusan leállítani az adatbázis-kapcsolat lehetővé teszi. 

A hagyományos módon Dapper kapcsolatok létrehozása helyett igazolnia kell használni a [OpenConnectionForKey metódus](http://msdn.microsoft.com/library/azure/dn824099.aspx). Ez biztosítja, hogy az ellenőrzés akkor történik meg, és kapcsolatok felügyelt megfelelően Ha adatokat szilánkok között helyezi.

### <a name="requirements-for-dapper-integration"></a>Dapper integrációs követelményei
A rugalmas adatbázis ügyféloldali kódtár és a a Dapper API-k használata, ha azt szeretnénk tartsa meg az alábbi tulajdonságokat:

* **Scaleout**: hozzáadhat vagy eltávolíthat adatbázisok a szilánkos az alkalmazás a kapacitás iránti igények kielégítése érdekében a megfelelő, az alkalmazás az adatréteg szeretnénk. 
* **Konzisztencia**: mivel az alkalmazás horizontálisan horizontális használatával, hajtsa végre az adatok függő útválasztási kell. Azt szeretnénk, ehhez az függő útválasztási képességek a szalagtár használatára. Különösen szeretnénk tartsa meg az ellenőrzést, és konzisztencia biztosítja, hogy sérült vagy helytelen lekérdezési eredmények elkerülése érdekében a shard térkép keresztül van közvetítőalapú kapcsolatok számát biztosítja. Ez biztosítja, hogy egy adott shardlet kapcsolatok elutasították vagy leállt, ha (például) a shardlet jelenleg átkerül egy másik shard vegyes/egyesítés API-k használatával.
* **Objektum leképezésének**: tartsa meg a megfeleltetéseket, az alkalmazásban osztályok és az alapjául szolgáló adatbázis felépítését közötti lefordítani Dapper által biztosított kényelmi szeretnénk. 

A következő szakaszban ezek a követelmények alapján alkalmazások útmutatást nyújt **Dapper** és **DapperExtensions**.

## <a name="technical-guidance"></a>Műszaki útmutató
### <a name="data-dependent-routing-with-dapper"></a>Függő Dapper az útválasztási adatok
Dapper az alkalmazás általában létrehozásáért és a kapcsolatok adatbázis megnyitása. Az alkalmazás által T típus adott, Dapper eredményeket ad vissza lekérdezés, T. Dapper típusú .NET gyűjteményekre leképezése végez a T-SQL sorok T. típusú objektumokhoz Dapper hasonlóan SQL értékek vagy adatok adatkezelési nyelv utasítások paramétereinek leképezi a .NET-objektumokat. Dapper nyújt ez a funkció a normál a kiterjesztésmetódusok keresztül [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) ADO .NET SQL ügyfél függvénytárak objektum. Az SQL-kapcsolatot az adatfelderítési rekordok a rugalmas, méretezhető API-k által visszaadott megtalálhatók rendszeres [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok. Ez lehetővé teszi, hogy közvetlenül keresztül használja a Dapper bővítmények az ügyféloldali kódtár DDR API-t által visszaadott típussal, mert is egyszerű SQL ügyfél kapcsolat.

E megfigyelések ellenőrizze a rugalmas adatbázis ügyféloldali kódtár által Dapper a(z) közvetítőalapú kapcsolatok használata egyszerű.

Ez a Kódpélda (a mintából kísérő) a módszert, ahol a horizontális kulcs biztosítja az alkalmazás a könyvtárhoz a kapcsolatot a megfelelő shard replikaszervező mutatja be.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A hívás a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API a felváltja az alapértelmezett létrehozása és egy ügyfél SQL-kapcsolat megnyitása. A [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) hívás a argumentummal szükséges adatok függő útválasztás: 

* A szilánkok térkép elérni az adatok függő útválasztási felületek
* A horizontális kulcs a shardlet azonosításához
* A hitelesítő adatok (felhasználónév és jelszó) a shard való kapcsolódáshoz

A szilánkok térkép objektum kapcsolatot hoz létre, amely tárolja a megadott horizontális skálázási kulcs shardlet a szilánkcímtárban. A rugalmas adatbázis-ügyfél API-kat is címke valósítja meg a konzisztencia biztosítja a kapcsolatot. Hívása [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) rendszeres SQL ügyfélkapcsolati objektumot, a későbbi hívásakor adja vissza a **Execute** kiterjesztésmetódus a Dapper követi a szabványos Dapper eljárás.

Lekérdezések használhatók nagyon hasonlóan – először nyitja meg a kapcsolat használatával [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) az ügyfél API-t. Akkor használja a rendszeres Dapper kiterjesztésmetódusok az SQL-lekérdezés eredményei hozzárendelését a .NET-objektumokba:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Vegye figyelembe, hogy a **használatával** a DDR-kapcsolat hatókörökhöz letiltása a egy shard, ahol az tenantId1 tárolni a letiltás belül az összes művelet. A lekérdezés csak az aktuális shard tárolt blogok, de nem tárolt bármely más szilánkok megfelelően adja vissza. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Függő Dapper és DapperExtensions útválasztási adatok
További kiterjesztések biztosíthat további kényelmi és absztrakciós az adatbázisból, adatbázis-alkalmazások fejlesztése során az ökoszisztéma Dapper tartalmaz. DapperExtensions csak példaként szolgál. 

Az alkalmazásban használt DapperExtensions nem változik, hogyan adatbázis-kapcsolatok létrehozása és kezelése. Feladata továbbra is az alkalmazást nyitott kapcsolatot, és rendszeres SQL ügyfél kapcsolatobjektumok által a kiterjesztésmetódusok várható. Azt is használhat a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) fent leírt módon. A következő mintakódok látható, a mindössze annyi a változás, hogy már nem tudunk a T-SQL-utasítások írása:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

És ez a kódminta, a lekérdezés: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Átmeneti kezelése
A Microsoft Patterns & eljárások csapat közzé a [átmeneti hiba kezelési alkalmazás blokk](http://msdn.microsoft.com/library/hh680934.aspx) alkalmazásfejlesztők észlelt, amikor a felhőben futó közös átmeneti hiba feltételek csökkentése érdekében. További információkért lásd: [Perseverance, a titkos kulcs az összes Triumphs: az átmeneti hiba kezelési alkalmazás-blokk használata](http://msdn.microsoft.com/library/dn440719.aspx).

A mintakód átmeneti hibák elleni védelem érdekében az átmeneti hiba könyvtár támaszkodik. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** a kódban van definiálva, egy **SqlDatabaseTransientErrorDetectionStrategy** újrapróbálkozás-szám 10-es és 5 másodperc várjon az újrapróbálkozások között eltelt idő. Ha tranzakciók használ, győződjön meg arról, hogy az újra gombra hatókör visszatér egy átmeneti hiba esetén a tranzakció kezdete.

## <a name="limitations"></a>Korlátozások
A jelen dokumentumban ismertetett módszerekkel néhány korlátozásokkal jár:

* A mintakód a dokumentum nem bemutatják, hogyan kezeli a séma szilánkok.
* A kérelemben megadott, feltételezzük, hogy az adatbázis-feldolgozási tartalmazza egyetlen shard a kérelem által biztosított horizontális kulcs által meghatározott. Azonban ez a feltételezés nem mindig tartsa, például ha nincs lehetőség egy horizontális skálázási kulcs elérhetővé. A rugalmas adatbázis ügyféloldali kódtára tartalmaz orvoslása érdekében a [MultiShardQuery osztály](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Az osztály megvalósít egy kapcsolat absztrakciós több szilánkok keresztül lekérdezése. Ez a dokumentum nem MultiShardQuery Dapper együttes alkalmazásával van.

## <a name="conclusion"></a>Összegzés
Alkalmazás Dapper és DapperExtensions könnyen is kihasználhatja a rugalmas adatbázis-eszközt az Azure SQL Database. Az ebben a dokumentumban leírt lépések, ezeket az alkalmazásokat is használhat az eszköz funkció függő útválasztási módosítása a létrehozása és megnyitásakor az új adatok [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok használata a [ OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) hívás a rugalmas adatbázis ügyféloldali kódtár. Ez korlátozza az alkalmazás módosítások új kapcsolatok létrehozása és megnyitni ezeket helyeken szükséges. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
