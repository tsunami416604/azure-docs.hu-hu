---
title: Rugalmas adatbázis-ügyféloldali kódtár használata jól öltözött
description: Rugalmas adatbázis-ügyféloldali kódtár használata jól öltözött eszközzel.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823476"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Rugalmas adatbázis-ügyféloldali kódtár használata jól öltözött
Ez a dokumentum olyan fejlesztők számára készült, akik a kitalált alkalmazások létrehozására támaszkodnak, de a [rugalmas adatbázis-eszközöket](sql-database-elastic-scale-introduction.md) is szeretnék biztosítani, hogy olyan alkalmazásokat hozzanak létre, amelyek horizontális felskálázást hajtanak végre az adatszinten.  Ez a dokumentum bemutatja a rugalmas adatbázis-eszközökkel való integráláshoz szükséges, jól látható alkalmazások változásait. Célunk, hogy a rugalmas adatbázis-szegmensek kezelését és az Adatfüggő útválasztást takarja. 

**Mintakód**: [rugalmas adatbázis-eszközök Azure SQL Database által jól látható integrációhoz](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

A **jól** használható és **DapperExtensions** integrálása a rugalmas adatbázis-ügyféloldali kódtár Azure SQL Database egyszerű. Az alkalmazások az Adatfüggő útválasztást úgy használhatják, hogy megváltoztatják az új [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) -objektumok létrehozását és megnyitását, hogy az [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívást használják az [ügyfél könyvtárából](https://msdn.microsoft.com/library/azure/dn765902.aspx). Ez korlátozza az alkalmazás változásait, hogy csak az új kapcsolatokat hozza létre és nyissa meg. 

## <a name="dapper-overview"></a>Takaros áttekintés
A **takart** objektum-viszonyítási mapper. A .NET-objektumokat az alkalmazásból egy rokon adatbázisba képezi le (és fordítva). A mintakód első része azt szemlélteti, hogyan integrálható a rugalmas adatbázis ügyféloldali könyvtára a jól használható alkalmazásokkal. A mintakód második része azt szemlélteti, hogyan integrálható a jól látható és a DapperExtensions.  

Az jól látható Mapper funkció olyan bővítményi metódusokat biztosít az adatbázis-kapcsolatokon, amelyek egyszerűbbé teszik a T-SQL-utasítások elküldését az adatbázis végrehajtásához vagy lekérdezéséhez. A jól öltözött például megkönnyíti a .NET-objektumok és az SQL-utasítások végrehajtása a hívások **végrehajtásához** , vagy az SQL-lekérdezések eredményének a .net-objektumokra való felhasználását a takaros **lekérdezési** hívásokkal. 

A DapperExtensions használatakor már nem kell megadnia az SQL-utasításokat. A bővítmények módszerei, például a **GetList** vagy az adatbázis-kapcsolat **beillesztése** az SQL-utasításokat a színfalak mögött hozza létre.

A jól öltözött és a DapperExtensions egy másik előnye, hogy az alkalmazás az adatbázis-kapcsolatok létrehozását vezérli. Ez segít a rugalmas adatbázis-ügyfél függvénytárának kezelésében, amely az adatbázis-kapcsolatokat a shardletek adatbázisokra való leképezése alapján közvetíti.

A takaros szerelvények lekéréséhez lásd: [takart pont háló](https://www.nuget.org/packages/Dapper/). A jól öltözött bővítmények esetében lásd: [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>A rugalmas adatbázis-ügyféloldali kódtár gyors áttekintése
A rugalmas adatbázis-ügyféloldali kódtár segítségével meghatározhatja az alkalmazásadatok *shardletek*nevű partícióit, leképezheti őket az adatbázisokra, és azonosíthatja őket a *kulcsok*horizontális felskálázásával. Annyi adatbázist használhat, amennyit csak szeretne, és terjesztheti a shardletek az adatbázisok között. A skálázási kulcs értékének az adatbázisokra való hozzárendelését a könyvtár API-jai által biztosított szegmenses Térkép tárolja. Ezt a képességet nevezik a szegmenses **leképezések felügyeletének**. A szegmenses Térkép az adatbázis-kapcsolatok közvetítőjét is szolgálja a horizontális Felskálázási kulcsot tartalmazó kérelmek esetében. Ezt a képességet az **Adatfüggő útválasztásnak**nevezzük.

![Szegmens térképek és Adatfüggő Útválasztás][1]

A szegmensek közötti Térkép kezelője védi a felhasználókat abban, hogy inkonzisztens nézeteket shardletbe adatokba, amelyek akkor fordulhatnak elő, ha egyidejű shardletbe-kezelési műveletek történnek az adatbázisokon. Ehhez a szegmens térképek a könyvtárral létrehozott alkalmazás adatbázis-kapcsolatait közvetítik. Ha a szegmens-felügyeleti műveletek befolyásolhatják a shardletbe, ez lehetővé teszi, hogy a szegmens Térkép funkció automatikusan ölje meg az adatbázis-kapcsolatokat. 

Ahelyett, hogy hagyományos módon hozzon létre kapcsolatokat a takaros kapcsolathoz, a [OpenConnectionForKey metódust](https://msdn.microsoft.com/library/azure/dn824099.aspx)kell használnia. Ezzel biztosítható, hogy az összes ellenőrzés megtörténjen, és a kapcsolatok megfelelően legyenek kezelve, ha bármilyen adatszegmens között mozog.

### <a name="requirements-for-dapper-integration"></a>A jól öltözött integráció követelményei
Ha a rugalmas adatbázis ügyféloldali függvénytárával és a jól öltözött API-kkal is dolgozik, a következő tulajdonságokat szeretné megőrizni:

* Horizontális **felskálázás**: a többrészes alkalmazás adatszintjéből az alkalmazás kapacitási igényeihez szükséges adatbázisokat szeretnénk hozzáadni vagy eltávolítani. 
* **Konzisztencia**: mivel az alkalmazás horizontális felskálázással lett kibővítve, az adatoktól függő útválasztást kell végrehajtania. A könyvtár adatkezelési útválasztási funkcióit szeretnénk használni. Különösen fontos, hogy megőrizze az ellenőrzési és a konzisztencia-garanciákat, amelyeket a rendszer a szegmenses Térkép-kezelőn keresztül felügyelt kapcsolatok által biztosított, hogy elkerülje a sérülést vagy a lekérdezés eredményét. Ez biztosítja, hogy az adott shardletbe létesített kapcsolatok elutasításra kerüljenek, vagy le legyenek állítva, ha (például) a shardletbe jelenleg egy másik szegmensbe helyezi át a Split/Merge API-k használatával.
* **Objektum-hozzárendelés**: szeretnénk megőrizni a jól öltözött leképezések kényelmét az alkalmazás osztályai és a mögöttes adatbázis-struktúrák lefordításához. 

A következő szakasz útmutatást nyújt ezekhez a követelményekhez a **takaros** és **DapperExtensions**alapuló alkalmazásokhoz.

## <a name="technical-guidance"></a>Technikai útmutató
### <a name="data-dependent-routing-with-dapper"></a>Adatfüggő útválasztás jól öltözött
A jól öltözött alkalmazások esetében általában az alkalmazás felelős az alapul szolgáló adatbázissal létesített kapcsolatok létrehozásában és megnyitásában. Az alkalmazás által megadott "T" típus alapján a jól öltözött a lekérdezés eredményét a T típusú .NET-gyűjtemények formájában adja vissza. a takaros végrehajtja a T-SQL result sorok leképezését a T típusú objektumokra. Hasonlóképpen, a jól öltözött térképek .NET-objektumok SQL-értékekre vagy az adatmanipulációs nyelv (DML)-utasítások paramétereinek. A takaros megoldás az ADO .NET SQL ügyféloldali kódtárai által kínált, a normál [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumhoz tartozó bővítményi metódusokkal biztosítja ezt a funkciót. A DDR-hez készült rugalmas skálázási API-k által visszaadott SQL-kapcsolatok szintén rendszeres [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektumok. Ez lehetővé teszi, hogy közvetlenül a kitalált bővítményeket használja az ügyféloldali DDR API által visszaadott típuson, mivel ez egy egyszerű SQL-ügyfélkapcsolat is.

Ezek a megfigyelések egyszerűvé teszik a rugalmas adatbázis-ügyféloldali kódtár által felügyelt kapcsolatok használatát.

Ez a mintakód (a kapcsolódó mintából) azt szemlélteti, hogy az alkalmazás hogyan biztosít a horizontális Felskálázási kulcsot a könyvtárhoz a jobb oldali szegmenshez való kapcsolódáshoz.   

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

A [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API hívása lecseréli egy SQL-ügyfél-kapcsolatok alapértelmezett létrehozását és megnyitását. A [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívása az Adatfüggő útválasztáshoz szükséges argumentumokat veszi igénybe: 

* Az Adatfüggő útválasztási felületek eléréséhez szükséges szegmenses Térkép
* A shardletbe azonosítására szolgáló horizontális Felskálázási kulcs
* A szilánkhoz való kapcsolódáshoz használt hitelesítő adatok (Felhasználónév és jelszó)

A szegmens leképezési objektum létrehoz egy kapcsolódást a szegmenshez, amely az adott horizontális Felskálázási kulcs shardletbe tárolja. A rugalmas adatbázis-ügyfél API-k is címkézik a kapcsolatokat a konzisztencia-garanciák megvalósításához. Mivel a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) hívása egy normál SQL-ügyfél-kapcsolódási objektumot ad vissza, a kitakart **végrehajtási** kiterjesztés metódusának ezt követő meghívása a szabványos jól látható gyakorlatot követi.

A lekérdezések ugyanúgy működnek, mint az ügyféloldali API-val a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) használatával. Ezután az SQL-lekérdezés eredményeinek a .NET-objektumokra való leképezéséhez használja a normál módon kitakarható bővítmény módszereit:

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

Vegye figyelembe, hogy a blokk és a DDR közötti kapcsolatok a blokkon belüli összes adatbázis-műveletet a blokkon belül egy olyan szegmensre **használják** , ahol a tenantId1 tárolva van. A lekérdezés csak az aktuális szegmensen tárolt blogokat adja vissza, a többi szegmensen tárolt fájlokat azonban nem. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Adatfüggő útválasztás takaros és DapperExtensions
A takaros funkciók olyan további bővítmények ökoszisztémája, amelyek további kényelmet és absztrakciót biztosítanak az adatbázisból az adatbázis-alkalmazások fejlesztésekor. A DapperExtensions egy példa. 

Az alkalmazásban a DapperExtensions használata nem változtatja meg az adatbázis-kapcsolatok létrehozását és kezelését. Továbbra is az alkalmazás feladata a kapcsolatok megnyitása, és a szokásos SQL-ügyfélkapcsolati objektumokat a bővítmény módszerei várják. A fentiekben ismertetett módon a [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) támaszkodhat. Ahogy az alábbi mintakód mutatja, az egyetlen változás, hogy már nem kell megírnia a T-SQL-utasításokat:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

És itt látható a lekérdezéshez tartozó mintakód: 

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

### <a name="handling-transient-faults"></a>Átmeneti hibák kezelésére
A Microsoft Patterns & Practices csapata közzétette az [átmeneti hibák kezelésére szolgáló alkalmazás-blokkot](https://msdn.microsoft.com/library/hh680934.aspx) , hogy segítse az alkalmazás-fejlesztőket a felhőben való futás során felmerülő gyakori átmeneti hibák elhárításában. További információkért tekintse meg [a kitartás, az összes diadal titka: az átmeneti hiba-kezelő alkalmazás blokk használata](https://msdn.microsoft.com/library/dn440719.aspx)című témakört.

A kód mintája az átmeneti hibák elleni védelemre támaszkodik, 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

A fenti kódban található **SqlDatabaseUtils. SqlRetryPolicy** egy 10 értékű újrapróbálkozási számmal rendelkező **SqlDatabaseTransientErrorDetectionStrategy** van definiálva, és 5 másodperc várakozási idő az újrapróbálkozások között. Ha tranzakciókat használ, győződjön meg arról, hogy az újrapróbálkozási hatókör a tranzakció elejére kerül vissza átmeneti hiba esetén.

## <a name="limitations"></a>Korlátozások
A jelen dokumentumban ismertetett megközelítések néhány korlátozást foglalnak magukban:

* A dokumentum mintakód nem mutatja be, hogyan kezelhetők a sémák a szegmensek között.
* A kérésnek megfelelően feltételezzük, hogy az adatbázis-feldolgozás egyetlen szegmensen belül található, amelyet a kérelem által biztosított horizontális kulcs azonosít. Ez a feltételezés azonban nem mindig tart fenn például, ha nem lehet elérhetővé tenni egy horizontális Felskálázási kulcsot. Ennek megoldásához a rugalmas adatbázis ügyféloldali könyvtára tartalmazza a [MultiShardQuery osztályt](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Az osztály egy kapcsolati absztrakciót valósít meg több szegmens lekérdezéséhez. A MultiShardQuery és a jól öltözött együttes használata a dokumentum hatókörén kívül esik.

## <a name="conclusion"></a>Összegzés
A jól öltözött és DapperExtensions használó alkalmazások egyszerűen kihasználhatják a rugalmas adatbázis-eszközöket Azure SQL Database számára. Az ebben a dokumentumban ismertetett lépések végrehajtásával ezek az alkalmazások az eszköztől függő útválasztáshoz használhatják az eszközt, hogy megváltoztatják az új [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) -objektumok létrehozását és megnyitását a rugalmas [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) -hívás használatához. adatbázis ügyféloldali könyvtára. Ez korlátozza az alkalmazások módosítását azokon a helyeken, amelyeken új kapcsolatok jönnek létre és nyílnak meg. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
