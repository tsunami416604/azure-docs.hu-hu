---
title: Horizontális felskálázás az Azure SQL database |} A Microsoft Docs
description: A ShardMapManager, az elastic database-ügyfélkódtár használatával
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
ms.date: 03/16/2018
ms.openlocfilehash: 28387c1487c506173cba2eaaf3364dab36c7f70f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865852"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Horizontális felskálázás a szilánkleképezés-kezelővel rendelkező adatbázisok
Könnyen felskálázhatják horizontálisan az SQL Azure adatbázis, a szilánkleképezés-kezelő használata. A szilánkleképezés-kezelő egy speciális adatbázis, amely fenntartja a globális hozzárendelés információ minden szegmensre (adatbázisok) egy szegmens csoportban. A metaadatok, lehetővé teszi olyan alkalmazások értéke alapján a megfelelő adatbázishoz való csatlakozáshoz a **horizontális skálázási kulcs**. Emellett a készletben lévő minden szegmens tartalmazza, amelyek nyomon követik az adatok helyi horizontális skálázását (más néven **shardlet**). 

![Szilánkleképezés-kezelés](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Ismertetése, hogyan ezeket a térképeket tevődnek elengedhetetlen a szilánkleképezés-kezelés. Ebben az esetben a ShardMapManager osztállyal ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), található az a [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md) szegmenstérképet kezeléséhez.  

## <a name="shard-maps-and-shard-mappings"></a>Szegmenstérképet és szegmens-leképezések
Az egyes szegmensek választania kell a szegmenstérkép létrehozása típusát. A választás attól függ, hogy az adatbázis-architektúra: 

1. Az adatbázisonkénti egyetlen bérlőt  
2. Több bérlő adatbázisonként (két típusa):
   1. Lista leképezés
   2. Tartomány leképezése

Hozzon létre egy egybérlős modellbe a **list-hozzárendelési** horizontális skálázási térképet. Az egybérlős adatbázismodell bérlőnként egy adatbázis rendeli hozzá. Ez a lehetőség SaaS-fejlesztők számára modellje egyszerűbbé téve a felügyelet.

![Lista leképezés][1]

A több-bérlős modell egy önálló adatbázis rendel hozzá a több bérlő (és a bérlők csoportok juttathatja el több adatbázis között). Ezt a modellt használja, ha várhatóan minden bérlő, kis méretű adatokat kell rendelkeznie. Ebben a modellben bérlők számos hozzárendelése egy adatbázis használatával **tartomány hozzárendelési**. 

![Tartomány leképezése][2]

Megvalósíthat egy több-bérlős adatbázis modellt használja, vagy egy *lista leképezés* több bérlő hozzárendelése egy önálló adatbázis. Ha például DB1 bérlői azonosító 1 és 5 kapcsolatos információk tárolására szolgál, és DB2 7 bérlői és bérlői 10 adatait tárolja. 

![Több bérlő egyetlen DB][3] 

### <a name="supported-types-for-sharding-keys"></a>A horizontális skálázás kulcsok támogatott típusai
Rugalmas méretezés a következő típusú horizontális skálázási kulcsokként támogatja:

| .NET | Java |
| --- | --- |
| egész szám |egész szám |
| hosszú |hosszú |
| GUID azonosítója |uuid |
| byte]  |byte] |
| dátum/idő | időbélyeg |
| Időtartam | időtartam|
| Datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lista és a tartomány szilánkleképezések
Szegmenstérképet használatával lehet létrehozni **listák az egyes horizontális skálázási kulcsértékek**, vagy azok gyűjteménynévmintája használatával **horizontális skálázási adattartományokat kulcsértékek**. 

### <a name="list-shard-maps"></a>Listás szegmenstérképet
**Szegmensek** tartalmazhat **shardlet** és szegmenstérképek által karbantartott shardlet szegmenseken való hozzárendelését. A **listás szegmenstérkép** egyedi értékek, amelyek azonosítják a shardlet és az adatbázisok, melyektől a szegmensek közötti társítás.  **Hozzárendelések listázása** lekérdezésfuttatás explicit és különböző értékeket is le lehet képezni ugyanabban az adatbázisban. Például kulcs értéke 1 képez le egy adatbázist, és a kulcs értékeit, 3. és 6 mindkét maps adatbázis b.

| Kulcs | Szilánkleképezés-helye |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Tartomány szilánkleképezések
Az egy **tartomány-szegmenstérkép**, a tartományok le egy pár **[alacsony érték, a magas érték)** ahol a *alacsony érték* a tartományban minimum kulcsa és a *magas Érték* magasabb, mint a tartomány első érték. 

Ha például **[0, 100)** nagyobb vagy egyenlő 0 és 100-nál kisebb összes egész számokat tartalmaz. Vegye figyelembe, hogy több tartományt mutathat ugyanarra az adatbázisra, és a különálló tartományok támogatottak (például [100,200) és a [400,600) egyaránt pont adatbázist a c az alábbi példában.)

| Kulcs | Szilánkleképezés-helye |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

A fenti táblázat az általános példát egy **ShardMap** objektum. Minden sor egy egyszerű példa egy egyéni **PointMapping** (a listás szegmenstérkép) vagy **RangeMapping** (az a tartomány-szegmenstérkép) objektum.

## <a name="shard-map-manager"></a>Szilánkleképezés-kezelővel
Az ügyféloldali kódtár a szilánkleképezés-kezelővel szegmenstérképet gyűjteménye. Kezeli az adatokat egy **ShardMapManager** három helyen tárolt példány: 

1. **Globális Szegmenstérkép-térkép (GSM)**: megad egy adatbázist a tárházban az összes szegmenstérképet és leképezések szolgál. Kezelheti az adatokat automatikusan létrejönnek a speciális táblák és tárolt eljárásokat. Ez általában egy kis adatbázist, és könnyedén elérhető, és azt nem használható más az alkalmazás igényeinek megfelelően. Nevű speciális sémában végzett **__ShardManagement**. 
2. **Helyi szegmens térkép (LSM)**: minden adatbázis megadott szilánk kell úgy módosul, hogy több kis méretű táblák és speciális tárolt eljárások, amelyek tartalmaznak, és kezelheti a szegmens térkép szegmensben vonatkozó információkat tartalmaz. Ezt az információt a GSM adatait a redundáns, és lehetővé teszi az alkalmazás gyorsítótárazott szilánkleképezés-megfeleltetési adatokat ellenőrzése nélkül tetszőleges terhelés helyezi a GSM; az alkalmazás a LSM használ annak megállapításához, hogy a gyorsítótárazott leképezés továbbra is érvényes. A táblák a LSM az egyes szegmensek megfelelő is megtalálhatók a séma **__ShardManagement**.
3. **Alkalmazás-gyorsítótár**: minden alkalmazás példány elérése a **ShardMapManager** objektum fenntart egy helyi memórián belüli gyorsítótár, a leképezések. Legutóbb beolvasott útválasztási információkat tárolja. 

## <a name="constructing-a-shardmapmanager"></a>Hozhat létre, amely egy ShardMapManager
A **ShardMapManager** objektum segítségével a gyári jön létre ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) minta. A **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metódushoz hitelesítő adatait (beleértve a kiszolgáló nevét és a GSM tároló adatbázis neve) a az űrlap egy **ConnectionString** és a egy példányát adja vissza egy **ShardMapManager**.  

**Ne feledje:** a **ShardMapManager** kell példányosítani csak egyszer alkalmazás tartományonként, az inicializálási kódot az alkalmazás belül. Alkalmazás ugyanabban a tartományban ShardMapManager további példányait létrehozása nagyobb memória és az alkalmazás CPU-felhasználását eredményezi. A **ShardMapManager** tetszőleges számú szegmenstérképet is tartalmazhat. Egyetlen horizontálispartíció-térkép sok alkalmazás esetén elegendő lehet, amíg nincsenek alkalommal, amikor más-más részhalmazához adatbázisok különböző sémákkal vagy egyedi célokra szolgálnak Ezekben az esetekben több szegmenstérképet előnyösebb lehet. 

Ebben a kódban, egy alkalmazást próbál megnyitni egy meglévő **ShardMapManager** együtt a TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metódust. Ha egy globális jelölő objektumok **ShardMapManager** (GSM) létezik az adatbázisban még nem, az ügyféloldali kódtár hoz létre, azokat a CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) a metódus.

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

A .NET-verzió a PowerShell használatával hozzon létre egy új Szilánkleképezés-kezelővel. Példa érhető el [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Egy RangeShardMap vagy ListShardMap lekérése
Miután létrehozta a szegmensek kezelő, beszerezheti a RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) vagy ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) használatával a TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), a TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), vagy a GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metódust.

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

### <a name="shard-map-administration-credentials"></a>Szilánkleképezés térkép rendszergazdai hitelesítő adatai
Az alkalmazásokat, amelyek felügyeletéhez és kezelik a szegmenstérképet nem azokat, amelyek az útvonal-kapcsolatokra a szegmenstérképet azonosak. 

Ahhoz, hogy adminisztrálja a szegmenstérképet (hozzáadása vagy módosítása a szegmenseket, szegmenstérképet, szegmens leképezések, stb.) elindításához példányosítania kell a **ShardMapManager** használatával **hitelesítő adatokat, amelyek olvasási/írási jogosultsággal, mind a GSM-adatbázis és az egyes adatbázis, amely egy szegmens**. A hitelesítő adatok lehetővé kell tennie a táblák a GSM és a LSM írására szilánkleképezés-megfeleltetési adatokat a megadott, vagy megváltozott, valamint meghajtóbetűjeleket LSM táblák létrehozását az új szegmensekre.  

Lásd: [az Elastic Database ügyfélkódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Csak az érintett metaadatok
Feltöltése vagy módosítására használt módszerek a **ShardMapManager** adatok nem módosítják a felhasználói adatokat a szegmensek egymás tárolja. Például, például metódusok **CreateShard**, **DeleteShard**, **UpdateMapping**használatához és így tovább hatással csak szegmenshez térkép metaadatait. Ezek nem távolítható el, adja hozzá, vagy felhasználói adatokat a szegmensek szerepel az alter. Ehelyett tervezték, hogy ezek a metódusok végezhet hozzon létre külön műveletnek együtt használandó, vagy tényleges remove-adatbázisok, illetve amelyeket áthelyezése sorok egyik adatszilánkba író másik újraegyensúlyozására horizontálisan skálázott környezetben.  (A **felosztási-egyesítési** rugalmas adatbáziseszközöket eszköz felhasznál ezen API-k, valamint az adatok tényleges áthelyezését a szegmensek közötti replikálásával segít a vállalatnak.) Lásd: [az Elastic Database felosztási-egyesítési eszközének használatával végzett skálázást bemutató](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Adatfüggő útválasztás
A szilánkleképezés-kezelő alkalmazásban, amely az adatbázis-kapcsolatokat az alkalmazás-specifikus adatok műveletek végrehajtására szolgál. Ezeket a kapcsolatokat a megfelelő adatbázishoz kell tartoznia. Ez az úgynevezett **Adatfüggő útválasztás**. Ezekhez az alkalmazásokhoz hozza létre a hitelesítő adatokkal, amelyeket a csak olvasási hozzáféréssel rendelkezik a GSM-adatbázishoz az előállító egy szegmens kezelő objektumot. A későbbi kapcsolatok az egyes kérések adja meg a megfelelő szegmensre adatbázishoz való csatlakozáshoz szükséges hitelesítő adatokat.

Vegye figyelembe, hogy ezek az alkalmazások (használatával **ShardMapManager** csak olvasási jogosultságokkal megnyitott) nem lehet módosítani a leképezéseket és leképezéseket. Ezeket az igényeket hozzon létre felügyeleti-specifikus alkalmazások vagy a PowerShell-parancsfájlok, amelyek magasabb jogosultsági szintű hitelesítő adatok megadására, ahogy arra már korábban. Lásd: [az Elastic Database ügyfélkódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

További információkért lásd: [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Horizontálispartíció-térkép módosítása
Horizontálispartíció-térkép többféleképpen is módosítható. Az alábbi módszerek mindegyike módosítása a szegmensek és a hozzájuk tartozó leképezések leíró metaadatok, de azok fizikailag nem módosítják a szegmensekben lévő adatokat, és nem tegye azokat létrehozása vagy törlése a tényleges adatbázisok.  Előfordulhat, hogy a horizontális skálázási térképet alább leírt műveleteket kell össze kell hangolni a felügyeleti műveletek, amelyek fizikailag helyezi át adatokat, vagy, hozzáadhat és eltávolíthat a szegmensek szolgáló adatbázisok.

Ezek a metódusok együttműködve kielégítésének a rendelkezésre álló adatok szilánkokra osztott adatbázis környezetében általános elosztásának módosítása.  

* Adja hozzá vagy távolíthat szegmenseket: használata **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) és **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)), a shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) osztály. 
  
    A kiszolgáló és a cél szegmens jelölő adatbázis már léteznie kell ezeket a műveleteket végrehajtani. Ezek a metódusok nincs hatással, az adatbázisok csak a metaadatokat a horizontális skálázási térképet.
* Hozzon létre vagy pontokat vagy a szegmensek hozzárendelt tartományok eltávolítása: használata **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)), a RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) osztály, és **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)), a ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) osztály.
  
    Sok különböző ponton vagy tartományokat is le lehet képezni ugyanabban a szegmensben. Ezek a metódusok csak hatással vannak a metaadatok - adatokat, amelyek esetleg már szerepel a szegmensek nem befolyásolják. Ha az adatokat az adatbázisból ahhoz, hogy konzisztens az eltávolítandó **DeleteMapping** műveletek esetében külön-külön, de ezekkel a módszerekkel együtt ezeket a műveleteket hajt végre.  
* A meglévő tartományok ketté bontása vagy egy szomszédos címtartományok merge: használata **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) és **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Vegye figyelembe, hogy felosztása és egyesítése operations **ne módosítsa a szegmens, amelyhez key értékek vannak leképezve**. A felosztás bontja a meglévő tartomány két részből áll, de hagyja egyaránt, leképezve ugyanazon a szegmensen. Az egyesítési működik a két egymás melletti tartományt, amely már van leképezve ugyanazon a szegmensen, egyesítése őket egy egyetlen tartományba.  Pontok vagy a szegmensek között maguk tartományok használatával össze kell hangolni kell **UpdateMapping** adatok tényleges mozgatását együtt.  Használhatja a **felosztó/egyesítő** szolgáltatáshoz, amely része az elastic database-eszközök a szegmensek térkép módosításokat az adatmozgás koordinációt, amikor adatátviteli van szükség. 
* Tulajdonságkeresést (vagy áthelyezése) egyes pontokat vagy a különböző szegmensek tartományok: használata **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Mivel előfordulhat, hogy adatokat kell át lehet helyezni egy szegmens a másikra annak érdekében, hogy azok konzisztensek legyenek **UpdateMapping** műveletek kell elvégeznie a mozgás külön-külön, de ezekkel a módszerekkel együtt.
* Online és offline leképezések érvénybe: használata **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) és **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) szabályozhatja a leképezés online állapotát. 
  
    Szilánkleképezés-leképezések bizonyos műveletek csak engedélyezett "offline" állapotban van, leképezés esetén például **UpdateMapping** és **DeleteMapping**. Egy leképezési kapcsolat nélküli üzemmódban, ha a leképezés tartalmazza kulcs alapján Adatfüggő kérelem hibát ad vissza. Emellett egy tartomány első offline állapotba, ha az érintett szegmens felé irányuló összes kapcsolatot is automatikusan leállítani módosításának címtartományok ellen irányulnak lekérdezések inkonzisztens vagy nem teljes eredmények elkerülése érdekében. 

Leképezések a .NET-es nem módosítható objektumokat is.  A fenti módszerek leképezések módosító összes érvényteleníti a kódban hozzájuk hivatkozásokat is. Könnyebben feladatütemezések, amelyek egy leképezési állapotának módosítása műveletek végrehajtásához, minden módosítása, leképezés mód küldhet vissza egy új leképezési hivatkozást, így műveleteket is kapcsolódniuk. Ha például egy meglévő hálózatleképezést a 25-ös kulcsot tartalmazó shardmap sm törléséhez hajthat végre a következő: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Szilánk hozzáadása
Alkalmazások gyakran kell hozzáadni az új kulcsok vagy kulcstartományokkal horizontálispartíció-térkép, amely már létezik a várt adatok kezelésének új szegmensekre. Például előfordulhat, hogy egy új szegmensen üzembe helyezhet egy új bérlőt kell szilánkokra osztott alkalmazás bérlői azonosító alapján, vagy adatok horizontálisan skálázott havi szükség lehet egy új szegmensen üzembe helyezett minden egyes új hónap kezdete előtt. 

Ha a kulcs értékeit az új tartomány még nem részei egy létező hozzárendelés, és nincs szükség, egyszerűen adja hozzá az új szegmensen, és társítsa az új kulcs vagy a tartományt a szegmensben. Új szegmensek hozzáadásával kapcsolatos részletekért lásd: [hozzáadása egy új szegmensen](sql-database-elastic-scale-add-a-shard.md).

Adatáthelyezés igénylő forgatókönyvek esetén azonban a felosztó / egyesítő eszköz van szükség, amellyel a szükséges szegmens térkép frissítésekkel együtt közötti adatáthelyezés. További információ a felosztási-egyesítési eszközének használatával: [szétválasztás és egyesítés áttekintése](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
