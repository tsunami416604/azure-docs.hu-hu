---
title: Elastic database-ügyfélkódtár használata a dapperrel |} A Microsoft Docs
description: Elastic database-ügyfélkódtár használata a dapperrel.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 14eb92141a9d27d9f8978abb6d5c9a738c821ead
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866304"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Elastic database-ügyfélkódtár használata a dapperrel
Alkalmazásokat hozhat létre Dapper támaszkodik, de is szeretné kihasználni a fejlesztők számára van ez a dokumentum [elastic database tooling](sql-database-elastic-scale-introduction.md) , alkalmazzon horizontális skálázási horizontális felskálázási méretezhetik alkalmazások létrehozásához.  Ez a dokumentum a Dapper-alapú alkalmazások integrálása az elastic database-eszközökkel való szükséges változásokat mutatja be. A fókusz a rugalmas adatbázis szilánkkezelési és Adatfüggő útválasztásnak dapperrel összeállítása be van kapcsolva. 

**Mintakód**: [Elastic database-eszközök az Azure SQL Database – Dapper integrációs](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrálás **Dapper** és **DapperExtensions** az elastic database-ügyfélkódtár az Azure SQL Database sem ördöngösség. Az alkalmazások használhatják Adatfüggő útválasztásnak létrehozását és az új megnyitása [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumokat, amelyeket a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) , hívja meg a [ügyféloldali kódtár ](https://msdn.microsoft.com/library/azure/dn765902.aspx). Ez korlátozza a módosítások csak az alkalmazásban, ahol az új kapcsolatok létrehozása és megnyitott. 

## <a name="dapper-overview"></a>Dapper áttekintése
**Dapper** egy objektumrelációs leképező van. .NET-objektumokat az alkalmazásból, egy relációs adatbázisban (és fordítva) vannak leképezve. A mintakód első része azt mutatja be, hogyan integrálható az elastic database ügyfélkódtár Dapper-alapú alkalmazások. A második rész mintakódot azt ábrázolja, hogyan integrálhatja a Dapper és a DapperExtensions használatakor.  

A Dapper eseményleképező funkciókat nyújt a bővítő metódusokat, amelyek megkönnyítik a küldő a T-SQL utasítások végrehajtása vagy az adatbázis lekérdezése az adatbázis-kapcsolatok. Például Dapper megkönnyíti a .NET-objektumokat és az SQL-utasítások paramétereinek közötti megfeleltethetőségének **Execute** hívások, vagy az SQL-lekérdezések eredményeinek felhasználásához használata .NET-objektumokká **lekérdezés** a Dapper hívásokat. 

DapperExtensions használatakor nem kell többé biztosít az SQL-utasításokkal. Bővítmények módszerek, például **GetList** vagy **beszúrása** keresztül az adatbázis-kapcsolat létrehozása az SQL-utasítások a háttérben.

Egy másik Dapper, és DapperExtensions előnye, hogy az alkalmazás szabályozza-e az adatbázis-kapcsolat létrehozását. Ez segít az elastic database-ügyfélkódtár közvetítők adatbázis-kapcsolatok alapján shardlet adatbázisokhoz való hozzárendelését, amely együttműködik.

A Dapper szerelvényeket lekéréséhez lásd: [Dapper dot net](http://www.nuget.org/packages/Dapper/). A Dapper bővítmények, lásd: [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Az elastic database ügyfélkódtár gyors áttekintése
Az elastic database-ügyfélkódtár a partíciók az alkalmazásadatok nevű definiálása *shardlet*, megfeleltet az adatbázisokat, és azonosítsa azokat a *horizontális skálázási kulcsok*. Tetszőleges számú adatbázist kell, és ezeknek az adatbázisoknak a shardlet szét rendelkezhet. Horizontális skálázási kulcs értékeit az adatbázisokhoz való hozzárendelését az erőforrástár-API-k által biztosított szegmenstérképek által tárolódik. Ez a funkció neve **szilánkleképezés-kezelés**. A horizontális skálázási térképet az adatbázis-kapcsolatok a kéréseket, amelyek olyan szegmenskulcsot átvitelszervezőként is szolgál. Ez a funkció nevezzük **Adatfüggő útválasztásnak**.

![Szegmenstérképet és Adatfüggő Útválasztás][1]

A szilánkleképezés-kezelő felhasználók megvédi az inkonzisztens nézetek shardlet adatait, amely akkor fordulhat elő, amikor párhuzamos shardlet felügyeleti műveletek történik az adatbázis. Ehhez a szegmenstérképet közvetíteni az alkalmazás és a beépített adatbázis-kapcsolatok. Szilánkleképezés felügyeleti műveletek hatással lehet a shardlet, amikor ez lehetővé teszi a szegmensek térkép funkció automatikusan leállítani az adatbázis-kapcsolat. 

A hagyományos módon Dapper-kapcsolatok létrehozása helyett kell használnia a [OpenConnectionForKey metódus](https://msdn.microsoft.com/library/azure/dn824099.aspx). Ez biztosítja, hogy az összes ellenőrzés végrehajtása és a kapcsolatok megfelelően vannak kezelése, ha az adatokat a szegmensek között helyezi át.

### <a name="requirements-for-dapper-integration"></a>Dapper integrációs követelményei
A rugalmas adatbázis-klienskódtár és a Dapper API-k is működik, ha meg szeretné őrizni az alábbi tulajdonságokat:

* **Horizontális felskálázás**: hozzáadása, illetve eltávolíthat adatbázisokat a szilánkos alkalmazás kapacitásigény szükséges az alkalmazás adatrétegének szeretnénk. 
* **Konzisztencia**: mivel az alkalmazás horizontális skálázási használatával horizontálisan, kell elvégeznie a Adatfüggő útválasztásnak. Ehhez a tár Adatfüggő útválasztás képességeit használva szeretnénk. Különösen meg szeretné őrizni az érvényesítés, és a konzisztenciára vonatkozó garanciákat sérülés vagy hibás lekérdezési eredmények elkerülése érdekében a szilánkleképezés-kezelővel keresztül vannak közvetítőalapú kapcsolatok által biztosított. Ez biztosítja, hogy a kapcsolatok egy adott shardlet el lett utasítva vagy leállt, ha (például) a shardlet jelenleg helyezik át egy másik adatszilánkba író felosztó/egyesítő API-k használatával.
* **Az objektumok leképezése**: megőrzi a kényelem érdekében a Dapper az osztályok, az alkalmazás és az alapul szolgáló adatbázis struktúrák közötti átalakításra által biztosított megfeleltetéseket szeretnénk. 

A következő szakaszban ezek a követelmények alapján alkalmazások útmutatást nyújt **Dapper** és **DapperExtensions**.

## <a name="technical-guidance"></a>Műszaki útmutató
### <a name="data-dependent-routing-with-dapper"></a>Adatfüggő útválasztás dapperrel
Dapperrel, az alkalmazás feladata általában létrehozásához és megnyitásához az alapul szolgáló adatbázishoz való csatlakozás. A típus T az alkalmazás által, Dapper ad vissza, lekérdezési eredményeket, T. Dapper típusú .NET-gyűjteményekre hozzárendelését végzi a T-SQL-eredményt sorok T. típusú objektumokhoz Ehhez hasonlóan Dapper SQL értékeket, vagy adatokat adatkezelési (DML) nyelv utasításaival paramétereinek .NET-objektumokká képezi le. Dapper kínál ennek a funkciónak a használatával a szokásos a bővítő metódusokat [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) az ADO .NET SQL-ügyfélkódtárak objektumot. Az SQL-kapcsolatot az adatfelderítési rekordok a rugalmas, méretezhető API-k által visszaadott egyúttal rendszeres [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumokat. Ez lehetővé teszi számunkra, hogy közvetlenül használja Dapper bővítmények az ügyféloldali kódtár adatfelderítési rekordok API által visszaadott típus-en keresztül, mert egyben egyszerű SQL-ügyfél kapcsolat.

E megfigyelések ellenőrizze a Dapper az elastic database ügyfélkódtár által felügyelt kapcsolatok használata egyszerű.

Ez a Kódpélda (kísérő mintából) a módszert, ahol a szegmenskulcs megfelelő megválasztása közvetítse a kapcsolatot a megfelelő szegmensre az erőforrástárhoz az alkalmazás által biztosított mutatja be.   

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

A hívást a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API helyettesíti az alapértelmezett létrehozása és a egy SQL-ügyfél kapcsolat megnyitása. A [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívás által megkövetelt Adatfüggő útválasztásnak argumentumokat veszi fel: 

* Adatfüggő útválasztás kapcsolódási eléréséhez a szegmenstérkép
* A horizontális skálázási kulcs a shardlet azonosításához
* A hitelesítő adatok (felhasználónév és jelszó) a szegmenshez való kapcsolódáshoz

A szegmens térkép objektum, amely a megadott horizontális skálázási kulcs a shardlet tárol a szegmenshez kapcsolatot hoz létre. A rugalmas adatbázis-ügyfél API-k is címkével a kapcsolatot a konzisztenciagaranciákat megvalósításához. Hívása [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) adja vissza egy rendszeres SQL-ügyfél kapcsolat objektumot, a következő hívást a **Execute** metódust a Dapper követi a szokásos Dapper gyakorlat.

Lekérdezések nagyon ugyanúgy működnek – először megnyitja a kapcsolatot használó [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) az ügyfél API-t. A normál Dapper bővítő metódusok majd képezze le az SQL-lekérdezés eredményeit a .NET-objektumokká használhatja:

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

Vegye figyelembe, hogy a **használatával** adatfelderítési rekordok kapcsolat hatókörökkel letiltása az egyik adatszilánkba író tenantId1 hol tartják a blokkon belül minden adatbázis-műveletek. A lekérdezés csak akkor adja vissza, blogok, a jelenlegi szegmens tárolja, de nem bármely más szilánkokat tárolja azokat. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>A Dapper és DapperExtensions Adatfüggő Útválasztás
További kiterjesztések, megadhat további kényelmi célokat szolgál, és absztrakciós az adatbázisból, adatbázis-alkalmazások fejlesztése során ökoszisztémája Dapper tartalmaz. DapperExtensions csak példaként szolgál. 

Az alkalmazásban használt DapperExtensions nem változik hogyan adatbázis-kapcsolatok létrehozása és felügyelete. Még mindig az alkalmazás feladata, hogy nyissa meg a kapcsolatokat, és rendszeres SQL-ügyfél kapcsolatobjektumok bővítmény módszerek által várt. Hogy hagyatkozhat a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) ajánlásait. A következő Kódminták látható, a az egyetlen változás az, hogy már nincs a T-SQL-utasítások írása:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Ez pedig a kódminta található, a lekérdezés: 

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

### <a name="handling-transient-faults"></a>Átmeneti hibák kezeléséhez
A Microsoft Patterns és gyakorlatokkal foglalkozó csoportja közzé a [átmeneti-kezelési Alkalmazásblokk](https://msdn.microsoft.com/library/hh680934.aspx) alkalmazásfejlesztők észlelt, amikor a felhőben futó gyakori átmeneti hibák feltételek csökkentése érdekében. További információkért lásd: [Perseverance, minden Triumphs titkos kulcs: az átmeneti-kezelési Alkalmazásblokk használatával](https://msdn.microsoft.com/library/dn440719.aspx).

A kódminta átmeneti hibák elleni védelem érdekében az átmeneti hibák könyvtár támaszkodik. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** a fenti kód számít, ha egy **SqlDatabaseTransientErrorDetectionStrategy** újrapróbálkozás-számot 5 másodperc és 10 várjon az újrapróbálkozások között eltelt idő. Ha tranzakciók használ, ügyeljen arra, hogy az újrapróbálkozási hatókört visszatér az átmeneti hibák esetén a tranzakció kezdete.

## <a name="limitations"></a>Korlátozások
Az a jelen dokumentumban vázolt módszerek jár néhány korlátozás:

* A mintakód a dokumentum nem bemutatják, hogyan lehet séma kezelése a szegmensek között.
* Adja meg a kérést, feltételezzük, hogy az adatbázis-feldolgozási szerepel egyetlen horizontális skálázáson belül, a horizontális skálázási kulcs a kérés által megadott által azonosított. Azonban ezt a feltételezést nem mindig tartalmaz, például, ha az nem lehet olyan szegmenskulcsot elérhetővé. Ennek az elastic database-ügyfélkódtár magában foglalja a [MultiShardQuery osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Az osztály a lekérdezés a több szegmensben egy kapcsolat absztrakciós valósítja meg. Az túlmutat a jelen dokumentum MultiShardQuery használata dapperrel van.

## <a name="conclusion"></a>Összegzés
Dapper és DapperExtensions használó alkalmazások könnyen az Azure SQL Database előnyeit az elastic database-eszközökkel. Ebben a dokumentumban leírt lépések, ezeket az alkalmazásokat az eszköz funkció használható Adatfüggő útválasztás létrehozása és az új megnyitása [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumokat, amelyeket a [ OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) elastic database ügyfélkódtár hívás. Ez korlátozza az új kapcsolatok létrehozása és megnyitni ezeket helyeken szükséges alkalmazások módosítására. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
