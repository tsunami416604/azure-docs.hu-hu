---
title: Adatok áthelyezése másik kiterjesztett felhő adatbázisok |} Microsoft Docs
description: Ismerteti, hogyan kezelheti a szilánkok és áthelyezni az adatokat, a rugalmas adatbázis API-k használata önálló üzemeltetett szolgáltatáson keresztül.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3c68b18a96ae79cd32cd3059eab837e6051847dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647418"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Adatok mozgatása kiterjesztett felhőalapú adatbázisok között
Ha egy szoftver szolgáltatás fejlesztőként, és hirtelen az alkalmazás megy keresztül rengeteg igény szerint, akkor a növekedési igazodniuk kell. Így adhat meg további adatbázisok (szilánkok). Hogyan az adatokat az új adatbázisok újraterjeszteni a adatok integritásának megszakítása nélkül Használja a **vegyes egyesítéses eszköz** korlátozott adatbázisból származó adatok áthelyezése az új adatbázisokat.  

A felosztott egyesítéses eszköz fut egy Azure webes. Egy rendszergazda vagy a fejlesztői áthelyezése shardlets (a shard adatait) között a különböző adatbázisokhoz (szilánkok) eszközt használja. Az eszköz shard térkép felügyeleti használja a szolgáltatás metaadatokat tároló adatbázis karbantartása és egységes hozzárendelések biztosítja.

![Áttekintés][1]

## <a name="download"></a>Letöltés
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentáció
1. [A rugalmas adatbázis vegyes egyesítéses eszköz oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Vegyes egyesítéses biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Vegyes egyesítéses biztonsági megfontolások](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
5. [Meglévő adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [A rugalmas adatbázis-eszközök](sql-database-elastic-scale-introduction.md)
7. [A rugalmas adatbázis eszközök szószedet](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>A felosztott egyesítéses eszköz miért érdemes használni?
**Rugalmasság**

Alkalmazásokat kell egy Azure SQL Database-adatbázis kívüli rugalmasan stretch. Az eszköz segítségével új adatbázisok sértetlenségének megtartásával igény szerint helyezi át az adatokat.

**Vegyes növelése sikertelen volt** 

Teljes kapacitás a robbanásszerűen növekedett növelnie kell. Ehhez hozzon létre további kapacitást horizontális az adatok és szétosztása Növekményesen további adatbázisok között, amíg kapacitásigények teljesülnek. Ez az a legjobb példa a "vágási" funkció. 

**Egyesítés: zsugorítani**

Kapacitás egy üzleti határozza jellege miatt kell elvégezni. Az eszköz segítségével csökkentheti kevesebb méretezési egységek amikor üzleti lelassul. A "merge" funkciók a rugalmas bővítést vegyes egyesítéses-Ez a követelmény vonatkozik. 

**Shardlets mozgatásával elérési pontokhoz való kezelése**

Az adatbázisonkénti több bérlő lefoglalása a szilánkok shardlets a kapacitás szűk keresztmetszetek vezethet az egyes szilánkok. Ehhez az újbóli hozzárendelésének shardlets vagy foglalt shardlets áthelyezése új vagy kevésbé használt szilánkok. 

## <a name="concepts--key-features"></a>Alapfogalmak és a legfontosabb jellemzők
**Ügyfél által üzemeltetett szolgáltatások**

A felosztott egyesítéses kerülnek az ügyfél által szolgáltatott szolgáltatásként. Kell telepítenie és működteti a szolgáltatást a Microsoft Azure-előfizetést. A letöltött NuGet csomag konfigurációs sablont a információkat az adott központi telepítés befejezéséhez. Tekintse meg a [vegyes egyesítéses oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md) részleteiről. Mivel a szolgáltatás fut az Azure-előfizetéssel, szabályozhatja, és konfigurálhatja a szolgáltatás a legtöbb biztonsági funkcióit. Az alapértelmezett sablon tartalmazza a beállítások konfigurálása az SSL, a tanúsítvány alapú ügyfél-hitelesítéshez, a titkosítási tárolt hitelesítő adatok, esetlegesen korán DoS és az IP-korlátozásokat. További információt a biztonsági szempontok a következő dokumentumban talál [vegyes egyesítéses biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

Az alapértelmezett telepített szolgáltatás fut egy worker és egy webes szerepkör. Azure Cloud Services egyes használja a A1 Virtuálisgép-méretet. Ezeket a beállításokat nem módosíthatja, ha a csomag telepítése, amíg a futó felhőszolgáltatásban, (az Azure portálon) keresztül a sikeres telepítés után megváltoztathatja őket. Vegye figyelembe, hogy a feldolgozói szerepkör nem szabad beállítani több mint egy egypéldányos technikai okokból. 

**A shard térkép integráció**

A felosztott egyesítéses szolgáltatás kommunikációja a shard térkép az alkalmazás. A felosztott egyesítéses szolgáltatás használatakor felosztása vagy tartományok egyesítése vagy shardlets válthat a szilánkok között, a szolgáltatás automatikusan a shard térkép naprakészen tartása. Ehhez az szükséges, a szolgáltatás az alkalmazás a shard manager adatbázisához csatlakozik, és tárolja a tartományok hozzárendelések vegyes/egyesítési/move kérelmek állapotát. Ez biztosítja, hogy a shard térkép mindig megadja egy naprakész nézetben, amikor vegyes-merge műveletek fog. Szétválasztásához lemezegyesítési és -shardlet mozgási műveletek a forrás shard shardlets egy tranzakcióköteghez áthelyezését a cél shard alapján történik. A shardlet adatátviteli művelet során az aktuális köteg függvényében shardlets a shard térkép offline állapotúként megjelölt, és nem állnak rendelkezésre adatok függő útválasztási kapcsolatok használata a **OpenConnectionForKey** API. 

**Egységes shardlet kapcsolatok**

Adatátvitel egy új tranzakcióköteghez shardlets indításakor bármely shard térképét adatok függő útválasztási való csatlakozás a shardlet tárolására a shard történik az ezekben a shard leképezés API-k elejtett és az azt követő kapcsolatok shardlets le vannak tiltva, miközben az adatok a mozgás inkonzisztenciák elkerülése érdekében folyamatban van. Más shardlets ugyanazt a shard a kapcsolatok is beolvasása leállítása, de ismét sikeres lesz azonnal az újra gombra. A kötegelt helyezik át, ha a shardlets újra a cél shard online megjelölve, és az adatforrás adatai törlődnek a forrás szilánkcímtárban. A szolgáltatás végighalad ezeket a lépéseket minden kötegnél, amíg az összes shardlets át lett helyezve. Ez irányítja több kapcsolat kill művelet a teljes vegyes/egyesítési/áthelyezési művelet során.  

**Shardlet rendelkezésre állásának kezelése**

Korlátozza a kapcsolat leállítása végleges, tehát az aktuális köteg shardlets kapcsolatban a fentiekben ismertetett módon, hogy korlátozza a elérhetetlensége hatóköre shardlets egy tranzakcióköteghez egyszerre. Ez az előnyben részesített keresztül megközelítés ahol a teljes shard marad a shardlets offline egy megosztott vagy egyesítési művelet során. Egy kötegben, a különböző shardlets egyszerre, áthelyezése számának definiált mérete egy konfigurációs paraméter. Minden egyes felosztása és az egyesítési művelet attól függően, hogy az alkalmazások rendelkezésre állásáról és teljesítményéről igényeihez lehet meghatározni. Előfordulhat, hogy a tartományhoz, amely zárás alatt áll a shard térkép nagyobb, mint a megadott köteg mérete. Ennek oka az, a szolgáltatás úgy, hogy a tényleges adatok értékeit horizontális körülbelül egyeznie kell a Köteg mérete szerzi meg a tartomány méretét. Ez azért fontos, különösen ritkán megadott horizontális skálázási kulcs megjegyezhető. 

**Metaadat-tároló**

A felosztott egyesítéses szolgáltatás egy adatbázist használ, állapotának karbantartásához és naplók kérelem feldolgozása során. A felhasználó hoz létre az adatbázis az előfizetését, és biztosít a kapcsolati karakterláncot, a szolgáltatás központi telepítése a konfigurációs fájlban. A felhasználó szervezete rendszergazdákat is csatlakozhat az adatbázis kérelem lefolyásának áttekintéséhez, és vizsgálja meg a lehetséges hibák kapcsolatos részletes információk.

**Horizontális-figyelése**

A felosztott egyesítéses szolgáltatás (1) szilánkos táblák, (2) hivatkozás táblázatok, illetve (3) normál közötti különbséget tesz. A felosztott/egyesítési/áthelyezés szemantikáját használt tábla típusa határozza meg, és az alábbiak szerint definiáltuk: 

* **Horizontálisan skálázott táblák**: vegyes, egyesítési és áthelyezési műveletek áthelyezése shardlets forrásból cél szilánkcímtárban. A teljes kérelem művelet sikeres befejezését követően ezek shardlets sem már nem található a forrás. Vegye figyelembe, hogy a célként megadott táblákhoz meg a cél shard léteznie kell, és nem tartalmazhat a művelet feldolgozása előtt a célként megadott tartomány adatait. 
* **Táblák hivatkozhat**: hivatkozási táblák, a felosztás egyesítési, és helyezze át a műveletek másolnia az adatokat a forráskiszolgálóról a cél szilánkcímtárban. Vegye figyelembe azonban, hogy nem végez módosítást újrapróbálkozásoknak a cél a shard egy adott táblához tartozó, ha bármely sor már létezik ebben a táblázatban a célszámítógépen. A tábla nem lehet üres. a hivatkozás tábla másolási művelet feldolgozni.
* **Más táblák**: a forrás- vagy egy megosztott és az egyesítési művelet célja a más táblák jelen lehet. A felosztott egyesítéses szolgáltatás parancsában ezek a táblázatok adatmozgás vagy másolási műveleteket. Vegye figyelembe azonban, hogy azok zavarhatja a megkötések esetén ezeket a műveleteket.

A hivatkozás szilánkos táblák és a vonatkozó információkat a **SchemaInfo** API-k a shard térképen. A következő példa egy adott shard térkép manager objektum smm ezen API-használatát mutatja: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

A táblák "region" és "számára" hivatkozási táblák is meg van adva, és a felosztott/egyesítési/move műveletekkel másolja. "ügyfél" és "rendelések" pedig meg van adva szilánkos táblákat. A horizontális kulcs C_CUSTKEY és O_CUSTKEY szolgál. 

**A hivatkozási integritás**

A vegyes egyesítéses szolgáltatás a táblák közötti függőségek elemzésével, és használja az elsődleges kulcs külső kulcsok kapcsolatai hivatkozási táblák és shardlets áthelyezésére műveletek előkészítése. Általában referencia táblák másolása először az függőségi sorrend, majd shardlets belül minden kötegelt függősége sorrendben kerülnek. Erre akkor szükség, úgy, hogy a cél shard FK-PK korlátai vannak figyelembe véve, az új adatok érkezik. 

**A shard térkép konzisztencia és végleges befejezése**

Hibák, esetén a felosztás egyesítéses szolgáltatás bármely leállás után folytatja a műveleteket, és célja, hogy hajtsa végre a folyamatban lévő kérések. Azonban lehetnek helyreállíthatatlan olyan helyzetekben, például amikor a célként megadott shard elveszett vagy sérült javíthatók. Ilyen körülmények néhány shardlets áthelyezésének amelyeknek továbbra is a forrás shard legyen elhelyezve. A szolgáltatás biztosítja, hogy shardlet leképezéseket csak frissítése után a szükséges adatok másolása sikeresen megtörtént a cél. Shardlets csak törlődnek a forrás, amennyiben a cél másolta az adatokat, és a megfelelő leképezések frissítése sikeresen megtörtént. A törlési művelet történik a háttérben, amíg a tartomány már online szerepel a cél szilánkcímtárban. A felosztott egyesítéses szolgáltatás mindig biztosítja a megfeleltetéseket, tárolja a shard leképezés helyességét.

## <a name="the-split-merge-user-interface"></a>A felosztott egyesítéses felhasználói felülete
A felosztott egyesítéses service-csomag magában foglalja, a feldolgozói szerepkör és a webes szerepkör. A webes szerepkör vegyes egyesítéses kéréseket küldhetnek egy interaktív módon használt. A felhasználói felület a fő összetevői a következők:

* Művelet típusa: A művelet típus, amely meghatározza, milyen típusú ehhez a kérelemhez a szolgáltatás által végrehajtott művelet a megfelelő választógombra kattintva. Választhat a felosztás egyesíteni, és helyezze át a forgatókönyveket. Szakítsa meg a korábban elküldött műveletet is. Vegyes használata, egyesítése és a tartomány shard maps áthelyezések. Lista shard csak a támogatási áthelyezési műveletek képezi le.
* Shard leképezés: A következő szakaszban a kérelemben szereplő paraméterek terjednek ki, a shard leképezés és az adatbázis-üzemeltetési a shard térképre. Különösen meg kell adnia a nevét, az Azure SQL adatbázis-kiszolgáló és adatbázis-üzemeltetési a shardmap a shard adatbázist, és végül a shard leképezés neve eléréséhez megadott hitelesítő adatok. A művelet jelenleg csak egyetlen halmazába hitelesítő adatokat fogad el. Ezeket a hitelesítő adatokat kell végrehajtani a shard a térképen, a felhasználói adatok módosításait a szilánkok megfelelő jogosultsággal.
* Forrástartományt (ossza fel, és egyesíti az): egy vegyes és az egyesítési művelet feldolgozza a alacsony és magas kulcsok a tartományt. Adja meg egy művelet egy unbounded magas kulcsérték, jelölje be a "magas kulcsa maximális" jelölőnégyzetet, és a magas kulcs mezőt hagyja üresen. A tartomány kulcsértékeit adja meg, hogy nem kell pontosan egyezik a leképezési és a határok shard képezze. Ha nem ad meg tartományt határokat minden a szolgáltatás fogja következtethető ki a legközelebbi tartomány meg automatikusan. A GetMappings.ps1 PowerShell-parancsfájl segítségével adott shard térképen aktuális hozzárendelések beolvasása.
* Megosztott adatforrás viselkedés (vegyes): A felosztás műveleteket, adja meg a ponthoz, hogy a forrástartomány felosztása. Ehhez a kívánt megtörténik a felosztás horizontális kulcs megadásával. Használja a választógomb adja meg, hogy áthelyezendő alsó részén található a tartomány (kivéve a megosztott kulcs), vagy hogy szeretné-e a felső rész áthelyezése (beleértve a megosztott kulcs).
* Forrás-Shardlet (lépés): műveletek eltérnek a megosztott vagy merge műveletek, nincs szükségük a forrás leírására széles áthelyezése. A horizontális kulcs értékét, az áthelyezni kívánt egyszerűen azonosítja áthelyezés forrást.
* Cél Shard (vegyes): az adatok a felosztás művelet a forrás megadása után meg kell határoznia, ahová az adatokat szeretné átmásolni a cél az Azure SQL Database-kiszolgáló és adatbázis nevét.
* Cél tartományon (egyesítés): egyesítési műveletek shardlets helyezze át egy meglévő szilánkcímtárban. A meglévő shard azáltal, hogy a meglévő tartomány, amelyet egyesíteni a tartományhatárokon azonosította.
* Köteg mérete: A kötegelt mérete határozza meg, hogy a rendszer kapcsolat nélküli módba az adatátvitel során egyszerre shardlets száma. Ez az egy egész érték használható a kisebb értékek amikor érzékeny a idõszakok shardlets a hosszú. A nagyobb értékek növeli az időt, amely egy adott shardlet offline de javíthatja a teljesítményt.
* Művelet azonosítója (Mégse): Ha már nincs szükség a folyamatban lévő műveletet, megszakíthatja a műveletet, adja meg a műveletet Azonosítóval ebben a mezőben. A kérelem állapot tábla kérhetnek le a Műveletazonosító (lásd a szakasz 8.1) vagy a webböngészőben, amelyben a kérés elküldése megtörtént a kimenetből.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
A felosztott egyesítéses szolgáltatás jelenlegi implementációja van a következő követelmények és korlátozások vonatkoznak: 

* A szilánkok létezik, és a shard leképezés egy vegyes-merge műveletet a szilánkok elvégzése előtt regisztrálni kell. 
* A szolgáltatás nem hoz létre táblák vagy bármely más adatbázis-objektumok automatikusan műveletei részeként. Ez azt jelenti, hogy kell-e a séma összes szilánkos táblák és hivatkozási táblák megtalálható a cél a shard bármely vegyes/egyesítési/áthelyezési művelet előtt. Horizontálisan skálázott táblák különösen kell megadni a tartományban, amelyben új shardlets is lehet hozzáadni egy vegyes/egyesítési/áthelyezési művelet. Ellenkező esetben a művelet sikertelen lesz a cél shard a kezdeti konzisztencia-ellenőrzést. Ne feledje, hogy az adatok másolásakor csak ha a referencia tábla üres-e, és, hogy vannak-e nem konzisztencia garanciát többi párhuzamosan futó írási műveleteket a hivatkozási táblák hivatkozás. Ezt azért ajánljuk: vegyes/egyesítési műveletek futtatásakor más írási műveletek nem módosítja a hivatkozási táblák.
* A szolgáltatás egy egyedi index vagy a kulcs, amely tartalmazza a jobb teljesítmény és megbízhatóság a nagy shardlets a horizontális kulcs által létrehozott sor identitás támaszkodik. Ez lehetővé teszi, hogy a szolgáltatás csak a horizontális kulcsérték mint még pontosabban részletességű áthelyezni az adatokat. Ez segít csökkenteni maximális naplóterület és zárolásokat, amelyek szükségesek a művelet során. Érdemes létrehozni egy egyedi index vagy egy elsődleges kulcs, beleértve a horizontális kulcs egy adott táblán, ha azt szeretné, hogy a tábla használata vegyes/egyesítési/move kérelmek. A horizontális kulcs teljesítményének javítására szolgál, a kulcs, vagy az index vezető oszlopa lehet.
* A kérelem feldolgozása során néhány shardlet adatok jelen, mind a forrás-és a cél shard lehet. Erre akkor szükség a shardlet mozgás során a hibák elleni védelem érdekében. A shard leképezés a felosztás egyesítéses integrációja biztosítja, hogy kapcsolatok keresztül az adatok függő útválasztási API-k használata a **OpenConnectionForKey** a shard térképen metódus nem látható minden inkonzisztens köztes állapotok. Azonban használata nélkül a forrás vagy a cél szilánkok való csatlakozáskor a **OpenConnectionForKey** metódus, köztes inkonzisztens állapotok visszatéréskor jelenik vegyes/egyesítési/move kérelmek fog. Ezek a kapcsolatok részleges vagy ismétlődő eredmények időzítése vagy a mögöttes a kapcsolat shard függően előfordulhat, hogy megjelenítése. Ez a korlátozás jelenleg magában foglalja a rugalmas bővítést több-Shard-lekérdezések által létrehozott kapcsolatok.
* A felosztott egyesítéses szolgáltatás a metaadatokat tároló adatbázis különböző szerepkörök között nem kell osztani. Például egy szerepkör kell mutatnia a különböző metaadatokat tároló adatbázis éles szerepkörnél átmeneti futó vegyes egyesítéses szolgáltatás.

## <a name="billing"></a>Számlázás
A felosztott egyesítéses szolgáltatás fut, felhőalapú szolgáltatásként a Microsoft Azure-előfizetést. Ezért felhőalapú szolgáltatások a szolgáltatás a példányra alkalmazni. Kivéve, ha gyakran műveleteket hajt végre vegyes/egyesítési/áthelyezésére, javasoljuk a felosztás egyesítéses felhőszolgáltatás törlése. Menti a futó költségeit vagy felhő szolgáltatáspéldány telepített. Hozza létre újra, és a könnyen futtatható konfiguráció elindítását megosztott vagy merge műveletek végrehajtásához. 

## <a name="monitoring"></a>Figyelés
### <a name="status-tables"></a>Állapot táblák
A felosztott egyesítéses szolgáltatás biztosítja a **RequestStatus** befejeződött, és a folyamatban lévő kérelmek figyelésére a metaadatokat tároló adatbázis táblájában. A táblázat minden egyes ehhez a példányhoz, a felosztás egyesítéses szolgáltatás küldtek vegyes egyesítéses kérelemhez sor. A következő információkat az egyes kérelmek biztosít:

* **Timestamp típusú**: a kérés bekapcsolásakor dátumát és időpontját.
* **OperationID azonosítójú**: A GUID, amely egyedileg azonosítja a kérelmet. A kérés is használható megszakítja a műveletet, amíg még mindig folyamatban.
* **Állapot**: a kérés a jelenlegi állapotában. A folyamatban lévő kéréseket, amelyben a vonatkozó kérés, mert a jelenlegi fázis is felsorolja.
* **CancelRequest**: A jelzőt, amely azt jelzi, hogy a kérelem visszavonásra került.
* **Folyamatban lévő**: A százalékos becsült a művelet befejezését. 50 érték azt jelzi, hogy a művelet körülbelül 50 % kész.
* **Részletek**: az XML-érték, amely egy részletesebb jelentést. Az állapotjelentés sorok készleteinek másolja át a forrás cél rendszeresen frissül. Hibák vagy kivételek esetén ezt az oszlopot a hibával kapcsolatos részletes információkat is tartalmaz.

### <a name="azure-diagnostics"></a>Azure Diagnostics
A felosztott egyesítéses szolgáltatás figyelési és diagnosztika Azure SDK 2.5 alapján Azure Diagnostics használja. A diagnosztikai konfigurációja szabályozhatja az itt leírtak szerint: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](../cloud-services/cloud-services-dotnet-diagnostics.md). A letöltött csomag tartalmazza a két diagnosztikai konfiguráció - egy, a webes szerepkör és egy, a feldolgozói szerepkör esetében. Ezek a szolgáltatás diagnosztika konfigurációi kövessék a [Cloud Service Fundamentals a Microsoft Azure-ban](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). A teljesítményszámlálók, a IIS naplókat, a Windows eseménynaplóiban keresse meg és a felosztott egyesítéses alkalmazás eseménynaplóiban definíciókat tartalmazza. 

## <a name="deploy-diagnostics"></a>Diagnosztika telepítése
Figyelés és diagnosztikai konfiguráció biztosítja a NuGet-csomagot a webes és feldolgozói szerepkörök használatával diagnosztika engedélyezéséhez futtassa a következő parancsokat, Azure PowerShell használatával: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

További információt itt a diagnosztikai beállításokat konfigurálhat és telepíthet módjáról: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Diagnosztika beolvasása
A diagnosztika könnyen elérhető a Visual Studio Server Explorer a Server Explorer fa Azure részében. Nyissa meg a Visual Studio példánya, és a menüsávon kattintson a nézet, és a Server Explorer. Kattintson az Azure-előfizetéssel kapcsolódni az Azure ikonra. Navigáljon az Azure Storage -> -> <your storage account> -> táblák WADLogsTable ->. További információkért lásd: [tárolási erőforrások keresse meg a Server Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

A fenti ábrán kiemelt WADLogsTable a felosztás egyesítéses szolgáltatás alkalmazás naplóból részletes eseményeket tartalmazza. Vegye figyelembe, hogy az alapértelmezett konfiguráció a letöltött csomag éles környezet részesíti előnyben. Ezért az időköz, amelynél naplókat, valamint a számlálók kikerülnek a szolgáltatáspéldány, nagy (5 perc). Teszt és fejlesztési csökkentse az intervallum a diagnosztikai beállítások a webes és a feldolgozói szerepkör az igényeinek megfelelően. Kattintson a jobb gombbal a szerepkört a Visual Studio Server Explorer (lásd fent), és adja meg az átviteli időszak a diagnosztikai beállításokat a párbeszédpanelen: 

![Konfiguráció][3]

## <a name="performance"></a>Teljesítmény
Jobb teljesítmény általában annál magasabb további performant szolgáltatási szinteket az Azure SQL Database várható. A magasabb szolgáltatásszintek magasabb IO-, Processzor- és memória helylefoglalását hasznot húzhatnak a tömeges másolás, és törlési műveletek a felosztás egyesítéses szolgáltatás által használt. Emiatt növelje a szolgáltatási szintjei csak ezeket az adatbázisokat egy meghatározott, korlátozott ideig.

A szolgáltatás érvényesítése lekérdezések a normál műveletek részeként is végez. A következő érvényesítési lekérdezések ellenőrizze az adatokat a célként megadott tartomány nem várt meglétét, és győződjön meg arról, hogy a vegyes/egyesítési/áthelyezési művelet elindítja a konzisztens. Ezeket a lekérdezéseket az összes működnek horizontális kulcstartományokkal határozzák meg a művelet hatókörén és a Köteg mérete, a kérelem definíciójának részeként. Ezeket a lekérdezéseket legjobb végre, ha az index jelen, a kezdő oszlop horizontális kulccsal rendelkezik. 

A horizontális kulccsal a kezdő oszlop egyediségének tulajdonság ezenkívül lehetővé teszi a szolgáltatás egy optimalizált megközelítés, amely korlátozza az erőforrás-felhasználás naplózási hely- és memória szempontjából. Helyezze át a nagy méretű (általában felett 1 GB-os) a egyediségi tulajdonság szükséges. 

## <a name="how-to-upgrade"></a>Frissítése
1. Kövesse a [vegyes egyesítéses szolgáltatás telepítése](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. A felhőalapú szolgáltatás konfigurációs fájlja a felosztás egyesítéses telepítéshez tükrözzék az új konfigurációs paraméterek módosítása. Egy új kötelező paraméter a titkosításhoz használt tanúsítvány adatait. Ehhez egyszerűen a letöltés, szemben a meglévő konfigurációt az új konfigurációs sablon fájljának összehasonlítani. Ellenőrizze, hogy hozzáadta a webes és a feldolgozói szerepkör "DataEncryptionPrimaryCertificateThumbprint" és "DataEncryptionPrimary" a beállításait.
3. Telepítése előtt a frissítés az Azure-ba, győződjön meg arról, hogy az összes jelenleg futó vegyes egyesítési művelet befejeződött. Könnyen ehhez a felosztás egyesítéses metaadatokat tároló adatbázis, a folyamatban lévő kérelmek RequestStatus és PendingWorkflows táblázatok lekérdezésével.
4. Frissítse a felosztás egyesítéses az Azure-előfizetése a meglévő felhőalapú szolgáltatás telepítése az új csomag és a frissített konfigurációs fájlt.

Nem kell vegyes egyesítéses frissítése egy új metaadatokat tároló adatbázisát kiépítéséhez. Az új verzió automatikusan frissíti a meglévő metaadatokat tároló adatbázis az új verzióra. 

## <a name="best-practices--troubleshooting"></a>Ajánlott eljárások és hibaelhárítás
* Adja meg egy tesztelési bérlőn, valamint a legfontosabb vegyes/egyesítési/áthelyezési műveletek a tesztbérlővel megadásával több szilánkok között. Győződjön meg arról, hogy minden helyesen van definiálva a shard képezze, hogy a műveletek sérti vonatkozó megkötések vagy a külső kulcsokat.
* A tesztelési bérlői tartsa adatok mérete nem áll kapcsolatban adatméret biztosításához a legnagyobb bérlő maximális méretének fent kapcsolatos hiba lépett fel. Ezzel a megoldással egy felső határa felmérheti a Navigálás egy egybérlős szükséges idő. 
* Győződjön meg arról, hogy a séma lehetővé teszi a törlést. A vegyes egyesítéses szolgáltatás megköveteli az adatok eltávolítása a forrás shard, ha az adatok másolása sikeresen megtörtént a cél képes. Például **triggerek törlése** megakadályozhatja, hogy a szolgáltatás törlése az adatok a forráshely és a művelet sikertelen lesz.
* A horizontális kulcs az Ön elsődleges kulcs vagy egyedi index definícióját a kezdő oszlop lehet. Amely biztosítja, hogy a megosztott vagy egyesítés érvényesítési lekérdezésekhez, és a tényleges mozgás és törlési műveletek horizontális kulcstartományokkal mindig működésbe, amelyek a lehető legjobb teljesítményt.
* A felosztott egyesítéses szolgáltatás az adatbázisokat tároló terület és az adatok közepén elhelyezésének engedélyezése. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

