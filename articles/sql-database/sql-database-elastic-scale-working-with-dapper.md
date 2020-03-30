---
title: Rugalmas adatbázis-ügyfélkódtár használata a Dapper rel
description: Rugalmas adatbázis-ügyfélkódtár használata a Dapper használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823476"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Rugalmas adatbázis-ügyfélkódtár használata a Dapper rel
Ez a dokumentum a fejlesztők számára, amelyek támaszkodnak Dapper alkalmazások létrehozásához, hanem szeretné, hogy a [rugalmas adatbázis-eszközök](sql-database-elastic-scale-introduction.md) létrehozása alkalmazások, amelyek megvalósítják horizontális horizontális horizontális skálázatlanaz adatréteg.  Ez a dokumentum a Dapper-alapú alkalmazások azon változásait mutatja be, amelyek a rugalmas adatbázis-eszközökkel való integrációhoz szükségesek. A hangsúly a rugalmas adatbázis shard kezelése és az adatoktól függő útválasztás a Dapper. 

**Mintakód:** [Rugalmas adatbázis-eszközök az Azure SQL Database – Dapper integrációhoz.](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f)

A **Dapper** és a **DapperExtensions integrálása** az Azure SQL Database rugalmas adatbázis-ügyfélkódtárjával egyszerű. Az alkalmazások adatfüggő útválasztást használhatnak az új [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok létrehozásának és megnyitásának módosításával az [ügyféltárból](https://msdn.microsoft.com/library/azure/dn765902.aspx)érkező [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívás használatához. Ez korlátozza az alkalmazás módosításait arra, hogy hol jönnek létre és nyitnak meg új kapcsolatokat. 

## <a name="dapper-overview"></a>Dapper – áttekintés
**Dapper** egy objektum-relációs leképező. A .NET objektumokat az alkalmazásból egy relációs adatbázisba képezi le (és fordítva). A mintakód első része bemutatja, hogyan integrálhatja a rugalmas adatbázis-ügyfélkódtár Dapper-alapú alkalmazásokkal. A mintakód második része bemutatja, hogyan integrálható a Dapper és a DapperExtensions használata esetén.  

A Dapper leképező funkciója olyan adatbázis-kapcsolatok kiterjesztési módszereit biztosítja, amelyek egyszerűsítik a T-SQL utasítások elküldését a végrehajtáshoz vagy az adatbázis lekérdezéséhez. A Dapper például megkönnyíti a .NET-objektumok és a **Hívások végrehajtása** SQL-utasítások paramétereinek leképezését, illetve az SQL-lekérdezések eredményét .NET-objektumokba való felhasználásával a Dapper **lekérdezési** hívásai használatával. 

DapperExtensions használatakor már nem kell megadnia az SQL-utasításokat. A bővítmények módszerei, például a **GetList** vagy a **Beszúrás** az adatbázis-kapcsolaton keresztül létrehozzák az SQL-utasításokat a színfalak mögött.

A Dapper és a DapperExtensions másik előnye, hogy az alkalmazás szabályozza az adatbázis-kapcsolat létrehozását. Ez segít a rugalmas adatbázis-ügyfélkódtár, amely brókerek adatbázis-kapcsolatok alapján a shardlets adatbázisokhoz való hozzárendelése.

A Dapper szerelvények beérkezéséhez lásd: [Dapper dot net](https://www.nuget.org/packages/Dapper/). A Dapper kiterjesztések, lásd: [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>A rugalmas adatbázis-ügyféltár gyors átnézése
A rugalmas adatbázis-ügyfélkódtár segítségével definiálhatja az alkalmazásadatok *shardlets*nevű partícióit, leképezheti őket adatbázisokhoz, és *a kulcsok szilánkosításával*azonosíthatja őket. Annyi adatbázist használhat, amennyire szüksége van, és terjesztheti a shardleteket ezekközött az adatbázisokközött. A szegmenskulcs-értékek leképezése az adatbázisok tárolja a tár API-k által biztosított shard térkép. Ezt a képességet **shard térképkezelésnek nevezzük.** A shard térkép is szolgál, mint a közvetítő az adatbázis-kapcsolatok a szilánkos kulcsot tartalmazó kérelmek. Ezt a funkciót **adatfüggő útválasztásnak nevezzük.**

![Shard térképek és adatfüggő útválasztás][1]

A shard map manager védi a felhasználókat az inkonzisztens nézetek shardlet adatok, amelyek akkor fordulhatelő, ha egyidejű shardlet felügyeleti műveletek történnek az adatbázisokban. Ehhez a szegmens leképezések közvetítői az adatbázis-kapcsolatok egy alkalmazás a tárral készült. Ha a szegmenskezelési műveletek hatással lehetnek a shardlet, ez lehetővé teszi, hogy a shard térkép funkció automatikusan leáll egy adatbázis-kapcsolat. 

Ahelyett, hogy a Dapper hagyományos módon hozna létre kapcsolatokat, az [OpenConnectionForKey metódust kell használnia.](https://msdn.microsoft.com/library/azure/dn824099.aspx) Ez biztosítja, hogy az összes érvényesítés történik, és a kapcsolatok megfelelően kezelik, ha bármilyen adat között mozog szegmensek.

### <a name="requirements-for-dapper-integration"></a>A Dapper-integráció követelményei
Ha a rugalmas adatbázis-ügyfélkódtárral és a Dapper API-kkal is dolgozik, a következő tulajdonságokat szeretné megőrizni:

* **Horizontális felskálázás**: Szeretnénk hozzáadni vagy eltávolítani az adatbázisokat a szilánkos alkalmazás adatrétegéből, ha szükséges az alkalmazás kapacitásigényeihez. 
* **Konzisztencia:** Mivel az alkalmazás horizontális skálázásával horizontális skálázható, adatfüggő útválasztást kell végrehajtania. Ehhez a tár adatfüggő útválasztási képességeit szeretnénk használni. Különösen meg szeretné őrizni a shard map manageren keresztül közvetített kapcsolatok által biztosított érvényesítési és konzisztencia-garanciákat a sérülés vagy a rossz lekérdezési eredmények elkerülése érdekében. Ez biztosítja, hogy egy adott shardlet-kapcsolat elutasítása vagy leállítása, ha (például) a shardlet jelenleg átkerül egy másik szegmensbe split/merge API-k használatával.
* **Objektumleképezés**: Meg akarjuk őrizni a Dapper által biztosított leképezések kényelmét az alkalmazás és az alapul szolgáló adatbázisstruktúrák osztályai közötti fordításhoz. 

A következő szakasz útmutatást ad ezekhez a követelményekhez a **Dapper** és **DapperExtensions**alapú alkalmazásokhoz.

## <a name="technical-guidance"></a>Műszaki útmutató
### <a name="data-dependent-routing-with-dapper"></a>Adatfüggő útválasztás a Dapper rel
A Dapper, az alkalmazás általában felelős a kapcsolatok létrehozásáért és megnyitásáért az alapul szolgáló adatbázis. Az alkalmazás T típusú értéket ad vissza a Dapper lekérdezési eredményeknek, mivel a T. Dapper típusú .NET gyűjtemények végrehajtják a T-SQL eredménysorokból a T típusú objektumokra történő leképezést. Hasonlóképpen a Dapper is leképezi a .NET objektumokat SQL-értékekre vagy adatkezelési nyelvi (DML) utasítások paramétereire. A Dapper ezt a funkciót az ADO .NET SQL-ügyfélkódtárakból származó [sqlconnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektum bővítménymetódusain keresztül kínálja. A DDR rugalmas méretezési API-k által visszaadott SQL-kapcsolat is rendszeres [SqlConnection-objektumok.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) Ez lehetővé teszi számunkra, hogy közvetlenül használja a Dapper kiterjesztések felett a típus által visszaadott ügyfélkönyvtár DDR API, mivel ez is egy egyszerű SQL Client kapcsolat.

Ezek a megfigyelések egyszerűvé teszik a Dapper rugalmas adatbázis-ügyfélkódtár által felügyelt kapcsolatok használatát.

Ez a kódpélda (a kísérő mintából) azt a megközelítést mutatja be, amelyben az alkalmazás biztosítja a szilánkos kulcsot a könyvtárnak a jobb szegmenshez való kapcsolat közvetítéséhez.   

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

Az [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API hívása felváltja az SQL-ügyfélkapcsolat alapértelmezett létrehozását és megnyitását. Az [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívás az adatfüggő útválasztáshoz szükséges argumentumokat veszi fel: 

* A shard térkép az adatfüggő útválasztási összeköttetések eléréséhez
* A shardlet azonosítására szolgáló szilánkos kulcs
* A hitelesítő adatok (felhasználónév és jelszó) a szegmenshez való csatlakozáshoz

A shard térkép objektum kapcsolatot hoz létre a szegmens, amely tartalmazza a shardlet a megadott szilánkos kulcs. A rugalmas adatbázis-ügyfél API-k is tag a kapcsolatot a konzisztencia-garanciák megvalósításához. Mivel az [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívása egy normál SQL-ügyfél kapcsolatobjektumot ad vissza, a Dapper-ből a **Kiterjesztés végrehajtása** metódus későbbi hívása a szokásos Dapper gyakorlatot követi.

A lekérdezések ugyanúgy működnek – először az ügyfél API-ból nyitja meg a kapcsolatot az [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) használatával. Ezután a szokásos Dapper bővítmény módszerekkel leképezheti az SQL-lekérdezés eredményeit .NET objektumokba:

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

Vegye figyelembe, hogy a ddr-kapcsolattal való **használatával** a blokkon belüli összes adatbázis-műveletet a tenantId1-et tartalmazó szegmensre összeshatókörrel letiltja. A lekérdezés csak az aktuális szegmensben tárolt blogokat adja vissza, de a más szegmenseken tároltakat nem. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Adatfüggő útválasztás a Dapper és DapperExtensions segítségével
A Dapper további bővítmények ökoszisztémájával rendelkezik, amelyek további kényelmet és absztrakciót biztosítanak az adatbázisból az adatbázis-alkalmazások fejlesztése során. DapperExtensions egy példa. 

A DapperExtensions használata az alkalmazásban nem módosítja az adatbázis-kapcsolatok létrehozásának és kezelésének módját. Továbbra is az alkalmazás felelőssége a kapcsolatok megnyitása, és a bővítmény metódusok rendszeres SQL-ügyfél kapcsolat objektumok várható. Támaszkodhatunk az [OpenConnectionForKey-ra](https://msdn.microsoft.com/library/azure/dn807226.aspx) a fentiek szerint. Ahogy a következő kódminták mutatják, az egyetlen változás az, hogy már nem kell írni a T-SQL utasítások:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

És itt van a kód minta részére a kérdés: 

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

### <a name="handling-transient-faults"></a>Átmeneti hibák kezelése
A Microsoft Patterns & Practices csapata közzétette az [Átmeneti hibakezelési alkalmazásblokkot,](https://msdn.microsoft.com/library/hh680934.aspx) hogy az alkalmazásfejlesztők mérsékeljék a felhőben való futtatás során előforduló gyakori átmeneti hibafeltételeket. További információ: [Kitartás, Minden diadal titka: Az átmeneti hibakezelési alkalmazásblokk használata.](https://msdn.microsoft.com/library/dn440719.aspx)

A kódminta az átmeneti hibakódtárra támaszkodik az átmeneti hibák elleni védelem érdekében. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

A fenti kódban található **SqlDatabaseDatabaseUtils.SqlRetryPolicy** egy **SqlDatabaseTransientErrorDetectionStrategy** definíciója, amelynek újrapróbálkozásszáma 10, és 5 másodperces várakozási idő az újrapróbálkozások között. Ha tranzakciókat használ, győződjön meg arról, hogy az újrapróbálkozási hatókör átmeneti hiba esetén a tranzakció elejére nyúlik vissza.

## <a name="limitations"></a>Korlátozások
Az e dokumentumban vázolt megközelítések néhány korlátozással járnak:

* A dokumentum mintakódja nem mutatja be a séma szegmensek közötti kezelését.
* Egy kérés, feltételezzük, hogy az összes adatbázis-feldolgozás itália i a kérelem által megadott egy shard található. Ez a feltételezés azonban nem mindig rendelkezik, például, ha nem lehet egy szilánkos kulcs elérhetővé tenni. Ennek megoldásához a rugalmas adatbázis-ügyfélkódtár tartalmazza a [MultiShardQuery osztályt.](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx) Az osztály megvalósítja a kapcsolat absztrakció több szegmensek lekérdezése. A MultiShardQuery dapperrel való együttes használata nem tartozik a dokumentum hatálya alá.

## <a name="conclusion"></a>Összegzés
A Dapper és DapperExtensions alkalmazást használó alkalmazások könnyen kiélvezhetik az Azure SQL Database rugalmas adatbázis-eszközeinek előnyeit. A jelen dokumentumban ismertetett lépések révén ezek az alkalmazások az eszköz adatfüggő útválasztásra való képességét használhatják az új [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok létrehozásának és megnyitásának módosításával a rugalmas adatbázis-ügyfélkódtár [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívásának használatához. Ez korlátozza az alkalmazás szükséges módosításait azokra a helyekre, ahol új kapcsolatokjönnek létre és nyitnak. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
