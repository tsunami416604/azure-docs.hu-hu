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
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 604690325fd755dcf5c997cc281fe9e5825c51a4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>A szilánkok térkép manager adatbázisokkal kiterjesztése
Könnyen horizontális felskálázás az SQL Azure adatbázisokat, használja a shard térkép kezelőjét. A szilánkok térkép manager shard csoportban lévő összes szilánkok (adatbázisok) globális hozzárendelés információt egy különleges adatbázis. A metaadatok lehetővé teszi, hogy egy alkalmazás értéke alapján a megfelelő adatbázishoz való kapcsolódáshoz a **horizontális kulcs**. Emellett minden shard készletében tartalmazza, amelyek nyomon követik a helyi részekre bonthatók az adatok (úgynevezett **shardlets**). 

![A shard leképezés kezelése](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Hogyan össze ezeket a maps elengedhetetlen megérteni a shard térkép kezelési. Ebben az esetben használja a [ShardMapManager osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx), míg a talált a a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) shard leképezések kezelésére.  

## <a name="shard-maps-and-shard-mappings"></a>A shard leképezések és shard hozzárendelések
Az egyes shard ki kell választani a típusát, a shard térkép létrehozásához. A választott attól függ, hogy az adatbázis-architektúra: 

1. Adatbázisonként egybérlős  
2. Több bérlő adatbázisonként (két típus):
   1. Lista leképezése
   2. Tartomány leképezése

Single-bérlő modell, hozzon létre egy **lista leképezési** shard leképezés. A single-bérlős modell bérlőnként egy adatbázis rendeli hozzá. Ez megegyezik egy hatékony modell Szolgáltatottszoftver-fejlesztőknek egyszerűbbé teszi a felügyeleti.

![Lista leképezése][1]

A több-bérlős modell több bérlő rendel hozzá egy adatbázist (és a bérlő csoportok szét több adatbázis). Ezt a modellt használja, ha várhatóan mindegyik bérlő kisméretű kell rendelkeznie. Ebben a modellben azt hozzárendelése számos különböző bérlők egy adatbázis használatával **tartomány leképezése**. 

![Tartomány leképezése][2]

Megvalósíthat egy adatbázis több-bérlős modell használatával vagy egy *lista leképezési* több bérlő hozzárendelése egy adatbázist. Például a D1 bérlőazonosító 1 és 5 információ tárolására szolgál, és DB2 eltárolja 7 bérlői és bérlői 10. 

![Az egyetlen DB Muliple bérlők][3] 

### <a name="supported-net-types-for-sharding-keys"></a>A horizontális kulcsok támogatott .net-típusok
Rugalmas méretezési támogatást a következő .net-keretrendszer típusok horizontális kulcsként:

* egész szám
* hosszú
* GUID
* Byte]  
* Dátum és idő
* A TimeSpan
* datetimeoffset

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

Például **[0, 100)** nagyobb vagy egyenlő 0 és 100-nál kisebb összes egész számokat tartalmaz. Vegye figyelembe, hogy több tartomány is mutathat ugyanarra az adatbázisra, és a különálló tartományok támogatottak (pl. [100,200) és a [400,600) is mutasson adatbázis C az alábbi példában szereplő.)

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
A **ShardMapManager** objektum segítségével jön létre egy [gyári](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) mintát. A  **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**  metódus hitelesítő adatait (beleértve a kiszolgáló és adatbázis nevét a GSM okozó) formájában időt vesz igénybe. a **ConnectionString** és egy példányát adja vissza egy **ShardMapManager**.  

**Ne feledje:** a **ShardMapManager** kell példányosítani csak egyszer app tartományonként, az inicializálási kód egy alkalmazáshoz. Az AppDomain ShardMapManager további példányának létrehozása nagyobb memória és CPU-felhasználás az alkalmazás eredményez. A **ShardMapManager** shard maps tetszőleges számú tartalmazhat. Lehet, hogy elegendő-e számos alkalmazás egyetlen shard térképre, amíg nincsenek alkalommal, ha az adatbázisok más-más részhalmazához használt különböző séma vagy egyedi célokra; azokban az esetekben érdemes lehet több shard maps. 

Ezzel a kóddal, az alkalmazás megpróbálja megnyitni egy meglévő **ShardMapManager** rendelkező a [TryGetSqlShardMapManager metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).  Ha egy globális objektumokból **ShardMapManager** (GSM) létezhet az adatbázis még nem, az ügyféloldali kódtár létrehozásuk nem használja a [CreateSqlShardMapManager metódus](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx).

    // Try to get a reference to the Shard Map Manager 
     // via the Shard Map Manager database.  
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

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 

Alternatív megoldásként a Powershell segítségével hozzon létre egy új Shard térkép Manager. Példa: elérhető [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Szerezze be a RangeShardMap vagy ListShardMap
Miután létrehozta a shard térkép kezelő, hogy megkaphassa a [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) vagy [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx) használatával a [TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), a [TryGetListShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx), vagy a [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) metódust.

    /// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
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

### <a name="shard-map-administration-credentials"></a>A shard térkép felügyeleti hitelesítő adatok
Felügyelheti és kezelheti a shard maps alkalmazások különböznek, amelyek útvonal kapcsolatok shard leképezve. 

Shard maps felügyeletéhez (hozzáadása vagy módosítása a szilánkok, shard maps, shard hozzárendelések, stb.) létre kell hozni a **ShardMapManager** használatával **hitelesítő adatokat, amelyek olvasási/írási jogosultsággal mindkét a GSM adatbázis és az összes adatbázisra, amelyet a shard funkcionál**. A hitelesítő adatok lehetővé kell tennie a globális szolgáltatásfigyelő szolgáltatás és a LSM lévő táblák írására shard megfeleltetési adatokat a megadott, vagy megváltozott, és mint LSM-táblázatok létrehozása az új szilánkok.  

Lásd: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Csak az érintett metaadatok
Feltöltése vagy módosítása használt módszerek a **ShardMapManager** adatok nem módosítja a szilánkok maguk tárolt felhasználói adatokat. Többek között például módszerek **CreateShard**, **DeleteShard**, **UpdateMapping**stb csak a shard térkép metaadatok hatással. Ezek nem távolítható el, adja hozzá vagy alter a szilánkok szereplő felhasználói adatokat. Ehelyett ezen módszerek úgy tervezték, hogy a parancs futtatásával hozzon létre külön műveletekkel együtt használható, vagy távolítsa el a tényleges adatbázisok, illetve hogy áthelyezése sorok egy shard másik egyensúlyba szilánkos környezet.  (A **vegyes egyesítéses** rugalmas adatbáziseszközöket eszköz lehetővé teszi, hogy ezek API-k között szilánkok adatok tényleges mozgatását koordinálása együtt használja.) Lásd: [méretezés, a rugalmas adatbázis vegyes egyesítéses eszközzel](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="populating-a-shard-map-example"></a>A szilánkok térkép példa feltöltése
Az alábbiakban látható egy parancssorozat-példa egy adott shard térkép feltöltéséhez műveletek. A kód végrehajtja ezeket a lépéseket: 

1. Új shard leképezés egy shard térkép manager belül jön létre. 
2. Két különböző szilánkok metaadatait a shard térkép kerül. 
3. Kulcs tartomány hozzárendelések számos adnak, és a teljes tartalmát a shard térkép jelennek meg. 

A kód írása, hogy a metódus is futtatható, ha hiba történik. Minden egyes kérelem teszteli, hogy a shard, vagy a hozzárendelés már létezik, hozza létre megkísérlése előtt. A kód azt feltételezi, hogy az adatbázisok nevű **sample_shard_0**, **sample_shard_1** és **sample_shard_2** létre lett hozva a kiszolgálón, karakterlánc által hivatkozott **shardServer**. 

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
                                     shardServer, 
                                     "sample_shard_0"), 
                                     out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
                                            shardServer, 
                                            "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
                                    shardServer, 
                                    "sample_shard_1"), 
                                    out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
                                             shardServer, 
                                            "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                          new RangeMappingCreationInfo<long>
                          (new Range<long>(0, 50), 
                          shard0, 
                          MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 

Másik megoldásként a PowerShell-parancsfájlok használatával az azonos eredmények elérése. A minta PowerShell-példák némelyike elérhető [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).     

Miután shard maps vannak-e töltve, adatok alkalmazásokat létrehozott vagy együttműködik a maps igazodó. Feltöltése vagy módosítása a maps kell nem fordulhat elő, amíg újra nem **térkép elrendezés** meg kell változtatnia.  

## <a name="data-dependent-routing"></a>Adatfüggő útválasztás
A szilánkok térkép manager legtöbb használandó adatbázis-kapcsolatok az alkalmazás-specifikus adatok műveletek elvégzését igénylő alkalmazások. Ezeket a kapcsolatokat a megfelelő adatbázishoz társítva kell lennie. Ez más néven **adatok függő útválasztási**. Az alkalmazás példányosítható származó a gyár csak olvasási hozzáféréssel rendelkezik a GSM adatbázishoz megadott hitelesítő adatokat használó egyik shard manager objektum. Egyes kérelmeket a későbbi kapcsolatok adja meg a megfelelő shard adatbázishoz való kapcsolódáshoz szükséges hitelesítő adatokat.

Vegye figyelembe, hogy ezek az alkalmazások (használatával **ShardMapManager** olvasási jogosultságokkal megnyitni) nem lehet módosítani a leképezéseket és leképezéseket. Hozzon létre felügyeleti-specifikus alkalmazások vagy a PowerShell-parancsfájlok, amelyek magasabb jogosultsági szintű hitelesítő adatokat a fentiekben taglaltak ezeket az igényeket. Lásd: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

További részletekért lásd: [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>A szilánkok térkép módosítása
A shard térképre különböző módokon lehet megváltoztatni. Minden, az alábbi eljárások közül módosítani a szilánkok és a hozzájuk tartozó leképezések leíró metaadatok, de a szilánkok adatainak fizikailag nem módosíthatják, és tegye azokat létrehozása vagy törlése a tényleges adatbázisok.  Az alábbiakban a shard térképen műveleteket kell össze kell hangolni, amely fizikailag helyezi át adatokat, vagy hozzáadására és eltávolítására szolgáló szilánkok adatbázisok, amelyek a felügyeleti műveleteket.

Ezek a módszerek együttesen érhető el a szilánkos adatbázis környezetében adatok teljes terjesztési módosítását építőelemeiként.  

* Szeretne felvenni vagy eltávolítani a szilánkok: használja  **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)**  és  **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)**  , a [Shardmap osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx). 
  
    A kiszolgáló és a cél shard képviselő adatbázis már léteznie kell ezeket a műveleteket végrehajtani. Ezek a módszerek gyakorolt hatás nem rendelkezik, az adatbázisok csak a metaadatok a shard a térképen.
* Létrehozandó vagy eltávolítandó pontokat vagy a szilánkok hozzárendelt tartományok: használja  **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**,  **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)**  , a [RangeShardMapping osztály](https://msdn.microsoft.com/library/azure/dn807318.aspx), és  **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)**  , a [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)
  
    Számos különböző pontok vagy tartományok ugyanazt a shard rendelhetők. Ezek a módszerek csak hatással vannak a metaadat - adatokat, amelyek esetleg már szerepel a szilánkok nem befolyásolják. Ha adatok eltávolítva az adatbázisból ahhoz, hogy a konzisztens **DeleteMapping** műveletek, akkor ezek a műveletek végrehajtásához, külön-külön, de ezekkel a módszerekkel együtt.  
* Meglévő tartományok felosztása két, vagy egyik szomszédos tartományok egyesítése: használja  **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)**  és  **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**.  
  
    Vegye figyelembe, hogy ossza fel, és egyesíti az műveletek **ne változtassa meg a shard, amelyhez a értékeit leképezett**. Valószínűségét egy meglévő tartományt bontja két részből áll, de hagyja is, mivel ugyanazt a shard leképezve. Az egyesítés két szomszédos tartományt már hozzárendelt ugyanazt a shard egyesítése őket egy egyetlen tartományba működik.  Pontok vagy a szilánkok közötti maguk tartományok használatával össze kell hangolni kell **UpdateMapping** adatok tényleges mozgatását együtt.  Használhatja a **vegyes/Egyesítés** szolgáltatáshoz, amely rugalmas adatbáziseszközöket koordinálására shard térkép módosításokat adatmozgás, amikor szükség van a mozgás részét. 
* Újbóli hozzárendelését (vagy áthelyezése) vagy különböző szilánkok vagy egyedi mutat: használja  **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**.  
  
    Mivel előfordulhat, hogy szükséges adatokat áthelyezni a shard egy másik ahhoz, hogy a konzisztens **UpdateMapping** műveletek, akkor kell végrehajtania, hogy a mozgás külön-külön, de ezekkel a módszerekkel együtt.
* Online és offline hozzárendelések érvénybe: használja  **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)**  és  **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)**  leképezéseket online állapotának vezérlésére. 
  
    A shard hozzárendelések bizonyos műveletek csak vannak engedélyezve, ha a leképezés nem "offline" állapotú, beleértve a **UpdateMapping** és **DeleteMapping**. Ha a leképezés offline állapotban, a kulcs szerepel a leképezéseket alapuló adatok függő kérelem hibát adnak vissza. Emellett egy tartomány első offline állapotba kerül, ha az érintett shard létesített összes kapcsolat vannak automatikusan el leállítja, nehogy szemben az módosítás alatt tartományok lekérdezések inkonzisztens vagy hiányos eredményeket. 

Hozzárendelések nem módosítható objektumokat a .net.  Az összes módszerek fenti leképezések módosításához el minden hivatkozást a kódban is érvénytelenné válnak. Révén könnyebben feladatütemezések, amelyek a hozzárendelés állapotváltozáshoz műveletek végrehajtásához, minden módosítása, leképezés mód adja vissza új leképezés hivatkozást, így műveletek elérése érdekében. Például egy meglévő leképezés az shardmap sm 25 kulcsot tartalmazó törléséhez hajthat végre a következő: 

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## <a name="adding-a-shard"></a>A szilánkok hozzáadása
Alkalmazások egyszerűen adja hozzá az új kulcsokat vagy egy már létező shard térkép kulcstartományokkal várt adatok kezelésének új szilánkok gyakran kell. Például egy bérlő-azonosító szerint szilánkos alkalmazás egy új shard létrehozni egy új bérlőt kell, vagy adatok szilánkos havi esetleg egy új shard kiépített új havonta megkezdése előtt. 

Az új kulcs értékek tartományán már nem része egy meglévő hozzárendelést, és nem adatmozgás szükség, esetén adja hozzá az új shard, és rendelje hozzá az új kulcs vagy az és, hogy a shard nagyon egyszerű. Új szilánkok hozzáadásával kapcsolatos részletekért lásd: [hozzáadása egy új shard](sql-database-elastic-scale-add-a-shard.md).

Adatátvitel igénylő forgatókönyvek esetén azonban a felosztás egyesítéses eszköz van szükség az adatátvitelt jelölik a között, valamint a szükséges shard leképezési frissítések szilánkok vezénylését. A felosztott egyesítéses yool használatával, lásd: [vegyes egyesítéses áttekintése](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
