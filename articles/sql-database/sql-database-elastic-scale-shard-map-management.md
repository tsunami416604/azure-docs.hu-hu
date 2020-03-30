---
title: Adatbázis kiszélesedése
description: A ShardMapManager, rugalmas adatbázis-ügyfélkódtár használata
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256262"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Adatbázisok horizontális felskálázása a shard térképkezelővel

Az SQL Azure-adatbázisok egyszerű horizontális felskálázásához használjon shard map managert. A shard map manager egy speciális adatbázis, amely egy szegmensek (adatbázisok) globális leképezési adatait egy szegmenskészletben tartja karban. A metaadatok lehetővé teszik, hogy egy alkalmazás a **szegmenskulcs**értéke alapján csatlakozzon a megfelelő adatbázishoz. Emellett a készlet minden szegmense olyan térképeket tartalmaz, amelyek nyomon követik a helyi szegmensadatokat (más néven **shardlets).**

![A szilánkleképezés kezelése](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

A térképek létrehozásának megértése elengedhetetlen a szegmenstérkép-kezeléshez. Ez a ShardMapManager osztály ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) használatával történik, amely a [rugalmas adatbázis-ügyféltárban](sql-database-elastic-database-client-library.md) található a szegmensleképezések kezeléséhez.  

## <a name="shard-maps-and-shard-mappings"></a>Shard térképek és shard leképezések

Minden shard, ki kell választania a létrehozását hozandó shard térkép típusát. A választás az adatbázis architektúrájáttól függ:

1. Egyetlen bérlő adatbázisonként  
2. Adatbázisonként több bérlő (két típus):
   1. Lista leképezése
   2. Tartomány leképezése

Egybérlős modell esetén hozzon létre egy **listaleképezési** szegmensleképezési térképet. Az egybérlős modell bérlőnként egy adatbázist rendel hozzá. Ez egy hatékony modell a SaaS-fejlesztők számára, mivel leegyszerűsíti a felügyeletet.

![Lista leképezése][1]

A több-bérlős modell több bérlőt rendel egy adott adatbázishoz (és a bérlők csoportjait több adatbázis között is eloszthatja). Akkor használja ezt a modellt, ha azt várja, hogy minden bérlőnek kis adatigénye legyen. Ebben a modellben rendeljen bérlői tartományt egy adatbázishoz **tartományleképezés**használatával.

![Tartomány leképezése][2]

Vagy valósíthat meg egy több-bérlős adatbázis-modell egy *lista leképezés* segítségével több bérlő hozzárendelése egy adott adatbázishoz. A DB1 például az 1-es és 5-ös bérlői azonosító adatainak tárolására szolgál, a DB2 pedig a 7-es és a 10-es bérlő adatait tárolja.

![Több bérlő egyetlen adatbázison][3]

### <a name="supported-types-for-sharding-keys"></a>Támogatott típusú szilánkok kulcsok

A Rugalmas méretezés a következő típusokat támogatja horizontális kulcsként:

| .NET | Java |
| --- | --- |
| egész szám |egész szám |
| long |long |
| Guid |uuid |
| bájt[]  |bájt[] |
| dátum/idő | időbélyeg |
| időtartomány | duration|
| datetimeoffset |eltolásdátumidő |

### <a name="list-and-range-shard-maps"></a>Shard térképek listázása és tartománytérképei

A szegmenstérképek az **egyes skálázási kulcsértékek listáival,** illetve **a skálázási kulcsértékek tartományai**val is létrehozhatók.

### <a name="list-shard-maps"></a>Szilánktérképek listázása

**A szegmensek** **shardlets-t tartalmaznak,** és a szegmensek shards leképezését egy shard térkép tartja karban. A **lista szegmensleképezés** egy társítás a shardlets azonosító egyes kulcsértékek és a szilánkokként szolgáló adatbázisok közötti társítás.  **A listaleképezések** explicitek, és különböző kulcsértékek et lehet leképezni ugyanahhoz az adatbázishoz. Például az 1-es kulcsérték leképezi az A adatbázist, a 3-as és a 6-os kulcsértékek pedig a B adatbázist.

| Kulcs | Shard helye |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Tartományszilánk-térképek

A **tartomány shard térképen**a kulcstartományt egy **[Alacsony érték, Magas érték)** pár írja le, ahol az *alacsony érték* a tartomány minimális kulcsa, a *Magas érték* pedig az első érték magasabb, mint a tartomány.

A **[0, 100)** például minden 0-nál nagyobb vagy annál kisebb és 100-nál kisebb egész szám. Ne feledje, hogy több tartomány is ugyanarra az adatbázisra mutathat, és a különálló tartományok támogatottak (például [100 200) és [400 600) mindkettő a C adatbázisra mutat a következő példában.)

| Kulcs | Shard helye |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

A fenti táblák mindegyike egy **fogalmi** példa egy ShardMap-objektumra. Minden sor egy egyszerűsített példa egy egyedi **PointMapping** (a lista shard térkép) vagy **RangeMapping** (a tartomány shard térkép) objektum.

## <a name="shard-map-manager"></a>Szilánkleképezés-kezelő

Az ügyfélkönyvtárban a shard map manager shard térképek gyűjteménye. A **ShardMapManager-példány** által kezelt adatok három helyen tárolódnak:

1. **Globális shard térkép (GSM)**: Meg kell adnia egy adatbázist, amely az összes shard leképezések és leképezések tárházaként szolgál. Az adatok kezeléséhez automatikusan létrejönnek speciális táblák és tárolt eljárások. Ez általában egy kis adatbázis, és enyhén elérhető, és nem használható az alkalmazás más igényeinek. A táblák egy speciális sémában, **a __ShardManagement.**
2. **Helyi shard térkép (LSM)**: Minden adatbázis, amely a shard határozza meg, hogy több kis táblák és speciális tárolt eljárások, amelyek tartalmazzák és kezelik a szegmensre jellemző szegmenstérkép-információkat. Ez az információ felesleges a GSM-ben lévő információkkal, és lehetővé teszi az alkalmazás számára, hogy érvényesítse a gyorsítótárazott shard térképadatokat anélkül, hogy bármilyen terhelést helyezne el a GSM-en; az alkalmazás az LSM segítségével határozza meg, hogy a gyorsítótárazott leképezés érvényes-e még. Az egyes szegmensek LSM-jének megfelelő táblák is a __ShardManagement sémában **találhatók.**
3. **Alkalmazás-gyorsítótár:** A **ShardMapManager** objektumot elérő minden alkalmazáspéldány a leképezések helyi memórián belüli gyorsítótárát tartja fenn. A közelmúltban lekért útválasztási információkat tárolja.

## <a name="constructing-a-shardmapmanager"></a>ShardMapManager összeállítása

A **ShardMapManager** objektum gyári[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) minta felhasználásával készült. A **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metódus a hitelesítő adatokat (beleértve a GSM-et tartalmazó kiszolgálónevet és adatbázisnevet) **ConnectionString** formájában veszi fel, és egy **ShardMapManager**példányt ad vissza.  

**Kérjük, vegye figyelembe:** A **ShardMapManager** kell példányosítást csak egyszer egy alkalmazás tartományban, az alkalmazás inicializálási kódját. A ShardMapManager további példányainak létrehozása ugyanabban az alkalmazástartományban az alkalmazás megnövekedett memóriáját és processzorkihasználtságát eredményezi. A **ShardMapManager** tetszőleges számú shard leképezést tartalmazhat. Bár egyetlen shard térkép elegendő lehet számos alkalmazás, vannak esetek, amikor különböző adatbáziskészletek különböző séma vagy egyedi célokra használják; ezekben az esetekben több shard térképek előnyösebb lehet.

Ebben a kódban egy alkalmazás megpróbál megnyitni egy meglévő **ShardMapManager-t** a TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metódussal. Ha a globális **ShardMapManager** (GSM) képviselő objektumok még nem léteznek az adatbázison belül, az ügyfélkódtár a CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metódussal hozza létre őket.

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

A .NET verzió, a PowerShell segítségével hozzon létre egy új Shard Map Manager. Egy példa [itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)érhető el .

## <a name="get-a-rangeshardmap-or-listshardmap"></a>RangeShardMap vagy ListShardMap beszerezni

Shard map manager létrehozása után a RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) vagy a ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) a TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), a TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) vagy a GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metódushasználatával szerezhető be.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Shard térkép felügyeleti hitelesítő adatok

A szegmenstérképeket felügyelő és kezelő alkalmazások eltérnek azoktól, amelyek a shard térképeket használják a kapcsolatok irányításához.

Shard leképezések felügyeletéhez (hozzáadása vagy módosítása szilánkok, shard térképek, shard leképezések, stb) példányosítania kell a **ShardMapManager** **hitelesítő adatokhasználatával, amelyek olvasási/írási jogosultsággal mind a GSM-adatbázis és minden adatbázis, amely shard szolgál.** A hitelesítő adatoknak lehetővé kell tenniük a GSM és az LSM tábláira vonatkozó írásokat, mivel a szegmenstérkép-adatok at beírják vagy módosítják, valamint lsm-táblákat kell létrehozni az új szegmenseken.  

Lásd: [A rugalmas adatbázis-ügyfélkódtár eléréséhez használt hitelesítő adatok.](sql-database-elastic-scale-manage-credentials.md)

### <a name="only-metadata-affected"></a>Csak az érintett metaadatok

A **ShardMapManager-adatok** feltöltéséhez vagy módosításához használt módszerek nem módosítják a szegmensekben tárolt felhasználói adatokat. Például a **CreateShard**, **DeleteShard**, **UpdateMapping**stb. Nem távolítják el, nem adják hozzá vagy módosítják a szegmensekben található felhasználói adatokat. Ehelyett ezeket a módszereket úgy tervezték, hogy a tényleges adatbázisok létrehozásához vagy eltávolításához végzett külön műveletekkel együtt használhatók, vagy amelyek sorokat helyeznek át az egyik szegmensből a másikba a szilánkos környezet egyensúlyának helyreállításához.  (A rugalmas adatbázis-eszközökben található **felosztásos egyesítési** eszköz ezeket az API-kat használja a szegmensek közötti tényleges adatmozgás vezénylésével együtt.) Lásd: [Méretezés a Rugalmas adatbázis felosztásos egyesítése eszközzel.](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="data-dependent-routing"></a>Adatfüggő útválasztás

A shard map manager olyan alkalmazásokban használatos, amelyek az alkalmazásspecifikus adatműveletek végrehajtásához adatbázis-kapcsolatokat igényelnek. Ezeket a kapcsolatokat a megfelelő adatbázishoz kell társosítani. Ezt **adatfüggő útválasztásnak nevezzük.** Ezekhez az alkalmazásokhoz példányosítani egy shard map manager objektumot a gyárból olyan hitelesítő adatokhasználatával, amelyek csak olvasható hozzáféréssel rendelkeznek a GSM-adatbázisban. A későbbi kapcsolatok egyéni kérelmei a megfelelő shard-adatbázishoz való csatlakozáshoz szükséges hitelesítő adatokat szolgáltatnak.

Vegye figyelembe, hogy ezek az alkalmazások (a **ShardMapManager** írásvédett hitelesítő adatokkal megnyitott) nem módosíthatják a leképezéseket vagy a hozzárendeléseket. Ezekre az igényekre, hozzon létre rendszergazdai adott alkalmazások vagy PowerShell-parancsfájlok, amelyek magasabb szintű jogosultsággal rendelkező hitelesítő adatokat, korábban tárgyalt. Lásd: [A rugalmas adatbázis-ügyfélkódtár eléréséhez használt hitelesítő adatok.](sql-database-elastic-scale-manage-credentials.md)

További információ: [Data dependent routing](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Shard térkép módosítása

A shard térkép különböző módokon módosítható. Az alábbi módszerek mindegyike módosítja a metaadatokat, amelyek leírják a szegmenseket és azok leképezését, de fizikailag nem módosítják az adatokat a szegmenseken belül, és nem hoznak létre vagy törölnek a tényleges adatbázisokat.  Az alábbiakban ismertetett shard térképen végrehajtott műveletek egy részét össze kell hangolni olyan felügyeleti műveletekkel, amelyek fizikailag áthelyezik az adatokat, vagy amelyek szegmensként szolgáló adatbázisokat adnak hozzá és távolítanak el.

Ezek a módszerek együtt működnek, mint a szilánkos adatbázis-környezetben az adatok általános eloszlásának módosításához rendelkezésre álló építőelemek.  

* Szegmensek hozzáadásához vagy eltávolításához: használja a **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) és **deleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) a shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)) osztály.
  
    A célszegmenst képviselő kiszolgálónak és adatbázisnak már léteznie kell ezeknek a műveleteknek a végrehajtásához. Ezek a módszerek nincsenek hatással magukra az adatbázisokra, csak a shard térkép metaadataira.
* A szegmensekhez leképezett pontok vagy tartományok létrehozásához vagy eltávolításához használja a ListShardMap[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping)( [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)Java[,](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping) [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))) (ListShardMap ( Java , .NET ) ClassShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) és **a** ListShardMap ( Java , [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) csoport CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), .NET ) osztályának **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) osztályát. **DeleteMapping**
  
    Számos különböző pont vagy tartomány leképezhető ugyanahhoz a szegmenshez. Ezek a módszerek csak a metaadatokat érintik – nem befolyásolják azokat az adatokat, amelyek már megtalálhatók a szegmensekben. Ha az adatokat el kell távolítani az adatbázisból, hogy konzisztensek legyenek a **DeleteMapping** műveletekkel, ezeket a műveleteket külön kell végrehajtani, de ezekkel a módszerekkel együtt.  
* Ha a meglévő tartományokat két részre szeretné osztani, vagy a szomszédos tartományokat egybe szeretné egyesíteni: használja a **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) és **a MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)) mezőt.  
  
    Ne feledje, hogy a felosztási és egyesítési műveletek **nem módosítják azt a szegmenst, amelyre a kulcsértékek le vannak képezve.** A felosztás egy meglévő tartományt két részre bont, de mindkettőt ugyanahhoz a szegmenshez rendeli. Az egyesítés két szomszédos tartományon működik, amelyek már ugyanahhoz a szegmenshez vannak leképezve, és egyetlen tartományba egyesítik őket.  A pontok vagy tartományok áthelyezése a szegmensek között kell koordinálni **az UpdateMapping** használatával együtt a tényleges adatmozgás.  Használhatja a **Split/Merge** szolgáltatás, amely része a rugalmas adatbázis-eszközök a shard térkép változások koordinálása az adatok mozgatása, ha mozgásra van szükség.
* Az egyes pontok vagy tartományok különböző szegmensekhez való újraleképezéséhez (vagy áthelyezéséhez) használja az **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) használatát.  
  
    Mivel az adatokat át kell helyezni az egyik szegmensből a másikba, hogy konzisztensek legyenek **az UpdateMapping-műveletekkel,** ezt a mozgást külön kell végrehajtani, de ezekkel a módszerekkel együtt.

* A leképezések online és offline használatához: használja a **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) és a **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) hozzárendelések online állapotát.
  
    A szegmensleképezések bizonyos műveletei csak akkor engedélyezettek, ha a leképezés "offline" állapotban van, beleértve az **UpdateMapping** és **a DeleteMapping állapotot.** Ha egy leképezés offline állapotban van, a leképezésben szereplő kulcson alapuló adatfüggő kérelem hibát ad vissza. Emellett, amikor egy tartomány először offline állapotba kerül, az érintett szegmenshez való összes kapcsolat automatikusan leáll, hogy megakadályozza a tartományok ellen irányuló lekérdezések inkonzisztens vagy hiányos eredményeit.

A leképezések nem módosítható objektumok a .Net objektumban.  A fenti összes módszer, hogy a változás leképezések is érvényteleníti a rájuk mutató hivatkozásokat a kódot. A leképezés állapotát módosító műveletek sorozatainak könnyebb végrehajtása érdekében a leképezést módosító összes módszer új leképezési hivatkozást ad vissza, így a műveletek láncolhatók. Ha például törölni szeretne egy meglévő leképezést a shardmap sm-ben, amely a 25-ös kulcsot tartalmazza, a következőket hajthatja végre:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Shard hozzáadása

Az alkalmazásoknak gyakran új szegmenseket kell hozzáadniuk az új kulcsoktól vagy kulcstartományoktól elvárt adatok kezeléséhez, egy már létező szegmensleképezéshez. Például egy bérlői azonosító által skálázott alkalmazás előfordulhat, hogy egy új shard egy új bérlő, vagy a havonta skálázott adatok szükség lehet egy új shard kiépített kezdete előtt minden új hónap.

Ha a kulcsértékek új tartománya még nem része egy meglévő leképezésnek, és nincs szükség adatáthelyezésre, egyszerűen hozzáadhatja az új szegmenst, és társíthatja az új kulcsot vagy tartományt a szegmenshez. Az új szegmensek hozzáadásáról további információt az [Új szegmens hozzáadása (Adding a new shard) (Új szegmens hozzáadása) (Új szegmens hozzáadása) (Új szegmens hozzáadása) (Új szegmens hozzáadása) témakörben](sql-database-elastic-scale-add-a-shard.md)talál.

Az adatáthelyezést igénylő forgatókönyvek esetében azonban a felosztásos egyesítési eszköz re van szükség a szegmensek közötti adatáthelyezés és a szükséges szegmenstérkép-frissítések együttes vezényléséhez. A felosztott egyesítési eszköz használatáról a [Felosztott egyesítés áttekintése című témakörben olvashat részletesen.](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
