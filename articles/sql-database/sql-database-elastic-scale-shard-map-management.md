---
title: "Azure SQL-adatbázis kibővítési |} Microsoft Docs"
description: "A ShardMapManager elastic database ügyféloldali kódtár használata"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 0e9d647a-9ba9-4875-aa22-662d01283439
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: ddove
ms.openlocfilehash: 03e7a3612e1cfcfaee2084db0d2eadb72e8a5f9d
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>A szilánkok térkép manager adatbázisokkal kiterjesztése
Könnyen horizontális felskálázás az SQL Azure adatbázisokat, használja a shard térkép kezelőjét. A szilánkok térkép manager shard csoportban lévő összes szilánkok (adatbázisok) globális hozzárendelés információt egy különleges adatbázis. A metaadatok lehetővé teszi, hogy egy alkalmazás értéke alapján a megfelelő adatbázishoz való kapcsolódáshoz a **horizontális kulcs**. Emellett minden shard készletében tartalmazza, amelyek nyomon követik a helyi részekre bonthatók az adatok (úgynevezett **shardlets**). 

![A shard leképezés kezelése](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Hogyan össze ezeket a maps elengedhetetlen megérteni a shard térkép kezelési. Ebben az esetben a ShardMapManager osztály használatával ([Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)), a tényleges a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) shard leképezések kezelésére.  

## <a name="shard-maps-and-shard-mappings"></a>A shard leképezések és shard hozzárendelések
Az egyes shard ki kell választani a típusát, a shard térkép létrehozásához. A választott attól függ, hogy az adatbázis-architektúra: 

1. Adatbázisonként egybérlős  
2. Több bérlő adatbázisonként (két típus):
   1. Lista leképezése
   2. Tartomány leképezése

Single-bérlő modell, hozzon létre egy **lista leképezési** shard leképezés. A single-bérlős modell bérlőnként egy adatbázis rendeli hozzá. Ez megegyezik egy hatékony modell Szolgáltatottszoftver-fejlesztőknek egyszerűbbé teszi a felügyeleti.

![Lista leképezése][1]

A több-bérlős modell több bérlő rendel hozzá egy adatbázist (és a bérlő csoportok szét több adatbázis). Ezt a modellt használja, ha várhatóan mindegyik bérlő kisméretű kell rendelkeznie. Ebben a modellben bérlők számos hozzárendelése egy adatbázis használatával **tartomány leképezése**. 

![Tartomány leképezése][2]

Megvalósíthat egy adatbázis több-bérlős modell használatával vagy egy *lista leképezési* több bérlő hozzárendelése egy adatbázist. Például a D1 bérlői azonosító 1 és 5 információ tárolására szolgál, és DB2 eltárolja 7 bérlői és bérlői 10. 

![Az egyetlen DB több bérlő][3] 

### <a name="supported-types-for-sharding-keys"></a>Támogatott típusok a horizontális kulcsok
Rugalmasan méretezhető horizontális kulcsok a következő típusok támogatják:

| .NET | Java |
| --- | --- |
| egész szám |egész szám |
| hosszú |hosszú |
| GUID |UUID |
| Byte]  |Byte] |
| Dátum és idő | időbélyeg |
| A TimeSpan | Időtartam|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lista és a tartomány shard leképezések
A shard maps használatával lehet létrehozni **listák az egyes horizontális kulcsértékek**, vagy azok lehet létrehozni használatával **horizontális tartományok kulcsértékek**. 

### <a name="list-shard-maps"></a>Lista shard maps
**Szilánkok** tartalmazhat **shardlets** és shardlets a szilánkok leképezése shard térképre tartja fenn. A **lista shard térkép** egyedi értékek, amelyek azonosítják a shardlets és szilánkok szolgálhat adatbázisok közötti társítás.  **Hozzárendelések listában** explicit és más alapvető értékek ugyanarra az adatbázisra kell hozzárendelni. Például adatbázis A kulcs 1 van leképezve, és 3. és 6 mindkét kulcsértékei hivatkozást adatbázis a b kiszolgálóra.

| Kulcs | A shard helye |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Tartomány shard maps
Az egy **tartomány shard térkép**, két legfontosabb tartomány le **[érték alacsony, magas érték)** ahol a *alacsony értéket* a tartomány minimális kulcs és a *értékes* az első érték nagyobb, mint a tartomány. 

Például **[0, 100)** nagyobb vagy egyenlő 0 és 100-nál kisebb összes egész számokat tartalmaz. Vegye figyelembe, hogy több tartomány is mutathat ugyanarra az adatbázisra, és a különálló tartományok támogatottak (például [100,200) és a [400,600) is pont adatbázis-C az alábbi példában.)

| Kulcs | A shard helye |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

A fenti táblák egy általános példát egy **ShardMap** objektum. Minden egyes sorára egy adott egyszerűsített példát **PointMapping** (az a lista shard leképezés) vagy **RangeMapping** (a tartomány shard térkép) objektum.

## <a name="shard-map-manager"></a>A shard térkép manager
Az ügyféloldali kódtára a shard térkép manager gyűjteménye shard maps. Kezeli az adatokat egy **ShardMapManager** példány tartják három helyen: 

1. **Globális Shard térkép (GSM)**: azt adja meg, melyik tárháza összes shard leképezéseket és leképezéseket szolgál. Különleges táblák és tárolt eljárások automatikusan létrejönnek a információinak kezeléséhez. Ez általában egy kis adatbázist, és könnyebb érhető el, és azt nem használható más az alkalmazás igényeinek. A táblák vannak különleges a séma nevű **__ShardManagement**. 
2. **Helyi Shard térkép (LSM)**: minden adatbázis kell lennie a shard úgy módosul, hogy több kis táblák és speciális tárolt eljárások, melyek és shard térkép adatokat, hogy a shard kezelése. Ezt az információt a globális szolgáltatásfigyelő szolgáltatás adatait a redundáns, és lehetővé teszi a gyorsítótárazott shard térkép információinak ellenőrzésére nélkül bármely terhelés helyezi a GSM; az alkalmazás az alkalmazás a LSM alapján állapítja meg, ha egy gyorsítótárazott leképezése továbbra is érvényes. A táblázatok minden shard a LSM megfelelő is szerepelnek a séma **__ShardManagement**.
3. **Alkalmazás-gyorsítótárának**: minden egyes alkalmazás példány elérése a **ShardMapManager** objektum a leképezéseket egy helyi memóriabeli gyorsítótárában megtalálhatók. Útvonal-információkat, amelyek a legutóbb beolvasott tárol. 

## <a name="constructing-a-shardmapmanager"></a>Egy ShardMapManager létrehozása
A **ShardMapManager** objektum gyár segítségével jön létre ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) mintát. A **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metódus hitelesítő adatait (beleértve a kiszolgáló és adatbázis nevét a GSM okozó) veszi a az űrlap egy **ConnectionString** és egy példányát adja vissza egy **ShardMapManager**.  

**Ne feledje:** a **ShardMapManager** kell példányosítani csak egyszer app tartományonként, az inicializálási kód egy alkalmazáshoz. ShardMapManager alkalmazás ugyanabban a tartományban további példányának létrehozása nagyobb memória és CPU-felhasználás az alkalmazás eredményez. A **ShardMapManager** shard maps tetszőleges számú tartalmazhat. Lehet, hogy elegendő-e számos alkalmazás egyetlen shard térképre, amíg nincsenek alkalommal, ha az adatbázisok más-más részhalmazához használt különböző séma vagy egyedi célokra; azokban az esetekben érdemes lehet több shard maps. 

Ezzel a kóddal, az alkalmazás megpróbálja megnyitni egy meglévő **ShardMapManager** a TryGetSqlShardMapManager rendelkező ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)) metódust.  Ha egy globális objektumokból **ShardMapManager** (GSM) létezhet az adatbázis még nem, az ügyféloldali kódtár létrehozásuk van a CreateSqlShardMapManager használatával ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET ](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metódust.

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

A .NET-verziója, a PowerShell segítségével hozzon létre egy új Shard térkép Manager. Példa: elérhető [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Szerezze be a RangeShardMap vagy ListShardMap
Miután létrehozta a shard térkép manager, a RangeShardMap kaphat ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) vagy ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) használatával a TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), a TryGetListShardMap ([Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), vagy a GetShardMap ([ Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metódust.

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

### <a name="shard-map-administration-credentials"></a>A shard térkép felügyeleti hitelesítő adatok
Felügyelheti és kezelheti a shard maps alkalmazások különböznek, amelyek útvonal kapcsolatok shard leképezve. 

Shard maps felügyeletéhez (hozzáadása vagy módosítása a szilánkok, shard maps, shard hozzárendelések, stb.) létre kell hozni a **ShardMapManager** használatával **hitelesítő adatokat, amelyek olvasási/írási jogosultsággal mindkét a GSM adatbázis és az összes adatbázisra, amelyet a shard funkcionál**. A hitelesítő adatok lehetővé kell tennie a globális szolgáltatásfigyelő szolgáltatás és a LSM lévő táblák írására shard megfeleltetési adatokat a megadott, vagy megváltozott, és mint LSM-táblázatok létrehozása az új szilánkok.  

Lásd: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Csak az érintett metaadatok
Feltöltése vagy módosítása használt módszerek a **ShardMapManager** adatok nem módosítja a szilánkok maguk tárolt felhasználói adatokat. Többek között például módszerek **CreateShard**, **DeleteShard**, **UpdateMapping**stb csak a shard térkép metaadatok hatással. Ezek nem távolítható el, adja hozzá vagy alter a szilánkok szereplő felhasználói adatokat. Ehelyett ezen módszerek úgy tervezték, hogy a parancs futtatásával hozzon létre külön műveletekkel együtt használható, vagy távolítsa el a tényleges adatbázisok, illetve hogy áthelyezése sorok egy shard másik egyensúlyba szilánkos környezet.  (A **vegyes egyesítéses** rugalmas adatbáziseszközöket eszköz lehetővé teszi, hogy ezek API-k között szilánkok adatok tényleges mozgatását koordinálása együtt használja.) Lásd: [méretezés, a rugalmas adatbázis vegyes egyesítéses eszközzel](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Adatfüggő útválasztás
A szilánkok térkép Managert használja a Helyadatbázis-kapcsolatot az alkalmazás-specifikus adatok műveletek elvégzését igénylő alkalmazások. Ezeket a kapcsolatokat a megfelelő adatbázishoz társítva kell lennie. Ez más néven **adatok függő útválasztási**. Az alkalmazás példányosítható származó a gyár csak olvasási hozzáféréssel rendelkezik a GSM adatbázishoz megadott hitelesítő adatokat használó egyik shard manager objektum. Egyes kérelmeket a későbbi kapcsolatok adja meg a megfelelő shard adatbázishoz való kapcsolódáshoz szükséges hitelesítő adatokat.

Vegye figyelembe, hogy ezek az alkalmazások (használatával **ShardMapManager** olvasási jogosultságokkal megnyitni) nem lehet módosítani a leképezéseket és leképezéseket. Hozzon létre felügyeleti-specifikus alkalmazások vagy a PowerShell-parancsfájlok, amelyek magasabb jogosultsági szintű hitelesítő adatokat a fentiekben taglaltak ezeket az igényeket. Lásd: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

További információkért lásd: [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>A szilánkok térkép módosítása
A shard térképre különböző módokon lehet megváltoztatni. Minden, az alábbi eljárások közül módosítani a szilánkok és a hozzájuk tartozó leképezések leíró metaadatok, de a szilánkok adatainak fizikailag nem módosíthatják, és tegye azokat létrehozása vagy törlése a tényleges adatbázisok.  Az alábbiakban a shard térképen műveleteket kell össze kell hangolni, amely fizikailag helyezi át adatokat, vagy hozzáadására és eltávolítására szolgáló szilánkok adatbázisok, amelyek a felügyeleti műveleteket.

Ezek a módszerek együttesen érhető el a szilánkos adatbázis környezetében adatok teljes terjesztési módosítását építőelemeiként.  

* Szeretne felvenni vagy eltávolítani a szilánkok: használja **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) és **DeleteShard** ([Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)), a Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) osztály. 
  
    A kiszolgáló és a cél shard képviselő adatbázis már léteznie kell ezeket a műveleteket végrehajtani. Ezek a módszerek gyakorolt hatás nem rendelkezik, az adatbázisok csak a metaadatok a shard a térképen.
* Létrehozandó vagy eltávolítandó pontokat vagy tartományt a szilánkok van leképezve: használja **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)), a RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) osztályt, és **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)), a ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) osztály.
  
    Számos különböző pontok vagy tartományok ugyanazt a shard rendelhetők. Ezek a módszerek csak hatással vannak a metaadat - adatokat, amelyek esetleg már szerepel a szilánkok nem befolyásolják. Ha adatok eltávolítva az adatbázisból ahhoz, hogy a konzisztens **DeleteMapping** műveletek, ezeket a műveleteket hajt végre, külön-külön, de ezekkel a módszerekkel együtt.  
* Meglévő tartományok felosztása két, vagy egyik szomszédos tartományok egyesítése: használja **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) és **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Vegye figyelembe, hogy ossza fel, és egyesíti az műveletek **ne változtassa meg a shard, amelyhez a értékeit leképezett**. Valószínűségét egy meglévő tartományt bontja két részből áll, de hagyja is, mivel ugyanazt a shard leképezve. Az egyesítés két szomszédos tartományt már hozzárendelt ugyanazt a shard egyesítése őket egy egyetlen tartományba működik.  Pontok vagy a szilánkok közötti maguk tartományok használatával össze kell hangolni kell **UpdateMapping** adatok tényleges mozgatását együtt.  Használhatja a **vegyes/Egyesítés** szolgáltatáshoz, amely rugalmas adatbáziseszközöket koordinálására shard térkép módosításokat adatmozgás, amikor szükség van a mozgás részét. 
* Újbóli hozzárendelését (vagy áthelyezése) vagy különböző szilánkok vagy egyedi mutat: használja **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Mivel előfordulhat, hogy szükséges adatokat áthelyezni a shard egy másik ahhoz, hogy a konzisztens **UpdateMapping** műveletek, végre kell hajtania, hogy a mozgás külön-külön, de ezekkel a módszerekkel együtt.
* Online és offline hozzárendelések érvénybe: használja **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) és **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) leképezéseket online állapotának vezérlésére. 
  
    A shard hozzárendelések bizonyos műveletek csak vannak engedélyezve, ha a leképezés nem "offline" állapotú, beleértve a **UpdateMapping** és **DeleteMapping**. Amikor egy offline állapotban, a kulcs szerepel a leképezéseket alapuló adatok függő kérelem hibát ad vissza. Emellett egy tartomány első offline állapotba kerül, ha az érintett shard létesített összes kapcsolat vannak automatikusan el leállítja, nehogy szemben az módosítás alatt tartományok lekérdezések inkonzisztens vagy hiányos eredményeket. 

Hozzárendelések nem módosítható objektumokat a .net.  Az összes módszerek fenti leképezések módosításához el minden hivatkozást a kódban is érvénytelenné válnak. Révén könnyebben feladatütemezések, amelyek a hozzárendelés állapotváltozáshoz műveletek végrehajtásához, minden módosítása, leképezés mód adja vissza új leképezés hivatkozást, így műveletek elérése érdekében. Például egy meglévő leképezés az shardmap sm 25 kulcsot tartalmazó törléséhez hajthat végre a következő: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>A szilánkok hozzáadása
Alkalmazások hozzáadása új kulcsokat vagy egy már létező shard térkép kulcstartományokkal várt adatok kezelésének új szilánkok gyakran kell. Például egy bérlő-azonosító szerint szilánkos alkalmazás egy új shard létrehozni egy új bérlőt kell, vagy adatok szilánkos havi esetleg egy új shard kiépített új havonta megkezdése előtt. 

Az új kulcs értékek tartományán már nem része egy meglévő hozzárendelést, és nem adatmozgás szükség, ha használata egyszerű, adja hozzá az új shard, és rendelje hozzá az új kulcs vagy az és, hogy a shard közé. Új szilánkok hozzáadásával kapcsolatos részletekért lásd: [hozzáadása egy új shard](sql-database-elastic-scale-add-a-shard.md).

Adatátvitel igénylő forgatókönyvek esetén azonban a felosztás egyesítéses eszköz van szükség az adatátvitelt jelölik a között, valamint a szükséges shard leképezési frissítések szilánkok vezénylését. A felosztott egyesítéses eszköz részletekért lásd: [vegyes egyesítéses áttekintése](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
