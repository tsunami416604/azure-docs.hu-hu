---
title: Adatok mozgatása kiterjesztett felhőalapú adatbázisok között |} A Microsoft Docs
description: Azt ismerteti, hogyan kezelheti a szegmenseket, és helyezze át az adatokat az elastic database API-k saját üzemeltetésű szolgáltatáson keresztül.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 518d7659df603ed0fcab4aebf5f35c3b92e75ccf
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162628"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Adatok mozgatása kiterjesztett felhőalapú adatbázisok között
Ha szolgáltatás a fejlesztők olyan szoftver, és hirtelen az alkalmazás megy keresztül áttekintse igény szerint, akkor a növekedéshez. Ezért adjon hozzá további adatbázisok (szilánkok). Hogyan az adatokat az új adatbázisok újraterjeszteni a az adatok integritásának megőrzése megszakítása nélkül Használja a **felosztási-egyesítési eszközének** korlátozott adatbázisok adatok áthelyezése az új adatbázisokat.  

A felosztási-egyesítési eszközének egy Azure-webalkalmazások szolgáltatásként fusson. Egy rendszergazda vagy fejlesztő eszközt használja a különböző adatbázisok (szilánkok) közötti áthelyezéséhez a shardlet (egy szegmensből adatokat). Az eszköz a szilánkleképezés kezelése a service metaadatokat tároló adatbázis karbantartása és egységes leképezések biztosítása használja.

![Áttekintés][1]

## <a name="download"></a>Letöltés
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentáció
1. [Elastic database felosztási-egyesítési eszközzel oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Biztonság szétválasztás és egyesítés konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Biztonság szétválasztás és egyesítés kapcsolatos szempontok](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
5. [Meglévő adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Elastic database-eszközökkel](sql-database-elastic-scale-introduction.md)
7. [Rugalmas adatbáziseszközökkel kapcsolatos](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Miért érdemes használni a felosztó / egyesítő eszköz?
**Rugalmasság**

Alkalmazások rugalmasan kívüli egy egyetlen Azure SQL Database-adatbázist Stretch kell. Az eszköz használatával adatok áthelyezése az új adatbázisok integritás megőrzése igény szerint.

**Split növekedésért** 

Teljes kapacitás a robbanásszerű növekedést növelni kell. Ehhez hozzon létre további kapacitást az adatok horizontális skálázást és szétosztása növekményes további adatbázisok között, amíg kapacitásigények teljesülnek. Ez az a legjobb példa a "split" funkció. 

**Egyesítési zsugorítása**

Kapacitás egy üzleti szezonális jellege miatt kell zsugorítani. Vertikális leskálázás kevesebb skálázási egység, ha üzleti lelassítja az eszköz segítségével. A "merge" funkció a rugalmasan méretezhető felosztási-egyesítési szolgáltatás ismerteti ennek a követelménynek. 

**Shardlet váltással elérési pontokhoz való kezelése**

Több bérlő adatbázisonként shardlet szegmenseken való kiosztását az egyes szegmensek vezethet, kapacitás szűk keresztmetszeteket. Ehhez a shardlet újbóli lefoglalása, és foglalt shardlet áthelyezése az új vagy kevésbé kihasznált szegmensek. 

## <a name="concepts--key-features"></a>Fogalmait és főbb funkcióiról
**Ügyfél-ban üzemeltetett szolgáltatásokkal**

A szétválasztás és egyesítés ügyfél által üzemeltetett szolgáltatás kézbesíti a rendszer. Kell üzembe és üzemeltethet a szolgáltatás a Microsoft Azure-előfizetésében. A letöltött NuGet csomag tartalmaz egy konfigurációs sablonját a információkat az adott központi telepítés befejezéséhez. Tekintse meg a [felosztási-egyesítési oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md) részleteiről. Mivel a szolgáltatás fut az Azure-előfizetésében, szabályozhatja, és a szolgáltatás a legtöbb biztonsági beállításait konfigurálja. Az alapértelmezett sablont a SSL, a tanúsítvány alapú ügyfél-hitelesítés, a tárolt hitelesítő adatokat, esetlegesen korán DoS és IP-korlátozások konfigurálása a lehetőségeket tartalmazza. A biztonsági szempontokra vonatkozó további információ található a következő dokumentum [biztonság szétválasztás és egyesítés konfigurációs](sql-database-elastic-scale-split-merge-security-configuration.md).

Az alapértelmezett üzembe helyezett egy feldolgozó és a egy webes szerepkör-szolgáltatásának futtatásakor. Minden egyes használja az a1-es virtuális gép méretét az Azure Cloud Servicesben. Bár ezek a beállítások nem módosítható, ha a csomag rendszerbe állítása, ezt módosíthatja őket a futó felhőszolgáltatás, (az Azure Portalon) sikeres üzembe helyezést követően. Vegye figyelembe, hogy a feldolgozói szerepkör kell nem konfigurálható a több mint egy egypéldányos technikai okokból. 

**Szilánkleképezés térkép-integráció**

A felosztási-egyesítési szolgáltatás kommunikál az alkalmazás horizontális skálázási térképet. A felosztási-egyesítési szolgáltatás használatakor fel vagy egyesíthet tartományok vagy shardlet áthelyezése a szegmensek között, a szolgáltatás automatikusan megőrzi a szegmenstérkép naprakész. Ehhez a szolgáltatás a szegmenstérkép-kezelő adatbázis az alkalmazás csatlakozik, és fenntartja a tartományokat és leképezések felosztása és egyesítése /-áthelyezési kérelem állapotát. Ez biztosítja, hogy a szegmenstérkép mindig megadja naprakész képet, amikor felosztási-egyesítési műveletek fog. Felosztása, egyesítése és shardlet adatátviteli műveletek vannak megvalósítva shardlet kötegelt a forrás-szegmenst a cél szegmensre helyezi át. A shardlet adatátviteli művelet során vonatkoznak az aktuális köteg shardlet adatain a szegmenstérkép offline állapotúként vannak megjelölve, és Adatfüggő útválasztás használatával kapcsolatokhoz nem érhető el a **OpenConnectionForKey** API-t. 

**Egységes shardlet kapcsolatok**

Adatáthelyezés shardlet új kötegelt indul el, ha bármely szegmenstérkép megadott Adatfüggő útválasztás kapcsolatokat a szegmens tárolja a shardlet leállított vannak, és az API-k segítségével shardlet adatain le vannak tiltva, míg az adatok áthelyezése a szegmenstérkép kapcsolatait a folyamatban lévő inkonzisztenciák elkerülése érdekében. Más shardlet ugyanabban a szegmensben lévő kapcsolatokat is első leállított, de újra lesz sikeres, azonnal az újra gombra. Után a batch helyezik, shardlet adatain újra az a cél szegmens online vannak megjelölve, és a forrásadatok törlődik a forrás-szegmens. A szolgáltatás halad át ezeket a lépéseket minden, mindaddig, amíg az összes shardlet át lett helyezve. Több kapcsolat kill művelet ez fog vezethet, továbbá a teljes felosztása/egyesítése/áthelyezési művelet során.  

**Shardlet rendelkezésre állás kezelése**

Egy kötegelt shardlet elérhetetlensége hatókörének egyszerre korlátozza a kapcsolatot a shardlet mint már említettük, az aktuális köteg leállítása korlátozza. Ez az előnyben részesített keresztül megközelítést, a teljes szegmensek marad offline állapotban van, a hozzá tartozó összes shardlet felosztása és egyesítése művelet során. Egy köteg különböző shardlet áthelyezése egyszerre, mennyi mérete egy konfigurációs paraméter. Az alkalmazások rendelkezésre állását és teljesítményét szükségleteinek minden felosztása és egyesítése művelet lehet meghatározni. Vegye figyelembe, hogy a tartományt a horizontális skálázási térképet zárolva van, előfordulhat, hogy nagyobb, mint a megadott köteg mérete. Ennek az oka a szolgáltatás választja ki a tartomány méretét úgy, hogy a horizontális skálázási kulcs értékeit az adatok tényleges száma körülbelül megegyezik a Köteg mérete. Ez nem szabad elfelejteni, különösen a horizontális skálázás ritkásan feltöltött kulcsok. 

**Metaadat-tároló**

A felosztási-egyesítési szolgáltatás adatbázist használ, annak állapotát és a kérelem feldolgozása során naplók tárolása céljából. A felhasználó hoz létre ehhez az adatbázishoz előfizetésüket, és biztosít a kapcsolati karakterláncot, a szolgáltatás központi telepítés konfigurációs fájlban. A felhasználó szervezete rendszergazdákat is csatlakozhat az adatbázis kérelem előrehaladását, és vizsgálja meg a lehetséges hibák kapcsolatos részletes információkat.

**A horizontális skálázás-figyelése**

A felosztási-egyesítési szolgáltatás (1) horizontálisan particionált táblák, (2) referencia táblák és (3) a normál táblázatokhoz állapottípust. Felosztás/egyesítés/áthelyezési művelet szemantikáját használja a tábla típusa határozza meg, és meghatározása a következő: 

* **Horizontálisan particionált táblák**: felosztása, egyesítése és áthelyezési műveleteket áthelyezése shardlet forrásból cél szegmensben. A teljes kérelem a sikeres telepítést követően a ezeket shardlet már nem találhatók el a forráson. Vegye figyelembe, hogy a célként megadott táblákhoz a cél szegmens léteznie kell, és nem tartalmazhatja a célként megadott tartomány, a művelet feldolgozása előtt adatait. 
* **Táblák hivatkozhat**: referencia táblák, a felosztás egyesítése és áthelyezése a műveletek az adatok másolása a forrás a cél szegmensre. Vegye figyelembe azonban, hogy a módosítása nélkül az adott táblában a cél horizontális történnek, ha bármely sor már létezik ebben a táblázatban a célon. A tábla nem lehet üres hivatkozás tábla másolási műveletek feldolgozni.
* **Más táblák**: a forrás- és a egy felosztása és egyesítése művelet célja a más táblák szerepelhet. A felosztási-egyesítési szolgáltatás nem veszi figyelembe ezeket a táblákat, bármilyen adatáthelyezés és másolási műveletek. Vegye figyelembe azonban, hogy azok zavaró megkötések esetén ezeket a műveleteket.

Az információkat a horizontálisan particionált táblák és a hivatkozás által biztosított a **SchemaInfo** API-k a horizontális skálázási térképet. Az alábbi példa egy adott szegmens térkép manager objektum smm a következő API-k használatát mutatja be: 

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

A táblák "régió" és "nemzet" referencia táblák vannak meghatározva, és másolja a felosztás/egyesítés/áthelyezési műveleteket. "ügyfél" és "orders" pedig vannak meghatározva horizontálisan particionált táblák. A horizontális skálázási kulcs C_CUSTKEY és O_CUSTKEY szolgál. 

**A hivatkozási integritás**

A felosztási-egyesítési szolgáltatás a táblák közötti függőségek elemzésével biztosítja, és használja az elsődleges kulcs külső kulcsok kapcsolatai előkészítéséhez referencia táblák és shardlet áthelyezéséhez a műveletek. Általában referencia táblák másolása először függőségi sorrendben shardlet belül az egyes kötegek függősége sorrendben másolja, majd. Erre akkor szükség, hogy a cél szegmens FK-PK korlátait is figyelembe véve, amint új adatok érkeznek. 

**Szilánkleképezés-térkép konzisztencia és a végleges**

Folytonosságát hibák esetén a felosztási-egyesítési szolgáltatás bármely leállás után folytatja a műveleteket, és a célja, hogy hajtsa végre a végrehajtási kérelem. Azonban lehetnek helyreállíthatatlan olyan esetekben, például amikor a célként megadott szegmens elveszett vagy sérült a biztonsága javíthatók. Ilyen körülmények egyes shardletek áthelyezésének amelyeknek elhelyezése a forrás-szegmens továbbra is. A szolgáltatás biztosítja, hogy shardlet leképezéseket csak akkor frissülnek, miután a szükséges adatok másolása sikeresen megtörtént a cél. Shardlet csak törlődnek a forrás minden adataikat a rendszer átmásolt a cél, és a megfelelő leképezések frissítése sikeresen megtörtént. A törlési művelet a háttérben történik, amíg a tartomány már kapcsolódik a célként megadott szegmens a. A felosztási-egyesítési szolgáltatás mindig biztosítja helyességét a horizontális skálázási térképet tárolt leképezéseket.

## <a name="the-split-merge-user-interface"></a>A szétválasztás és egyesítés felhasználói felület
A felosztási-egyesítési szolgáltatás csomag tartalmazza a feldolgozói szerepkör és a egy webes szerepkörben. A webes szerepkör segítségével felosztási-egyesítési kérelmek elküldése interaktív módon. A felhasználói felület fő összetevői a következők:

* Művelet típusa: A művelet típusa: egy választógombot, amely szabályozza a kérelem a szolgáltatás által végrehajtott műveletet. Választhat a felosztás egyesítése és áthelyezése forgatókönyveket. Egy korábban elküldött művelet is visszavonhatja. Használata felosztása, egyesítése, és helyezze át a tartomány szegmenstérképet kérelmeket. Lista szilánkleképezések csak a támogatási áthelyezési műveleteket.
* Horizontális skálázási térképet: A következő szakaszban a kérelem paramétereit, és tartalmaz információt a szegmenstérkép az adatbázis-üzemeltetési a szegmenstérkép. Különösen meg kell adnia az Azure SQL Database-kiszolgáló és a shardmap eléréséhez megadott hitelesítő adatok a szegmenstérkép-adatbázis, és végül a szegmenstérkép neve üzemeltető adatbázis nevére. A művelet jelenleg csak egyetlen hitelesítő adatra fogad el. Ezekkel a hitelesítő adatokkal kell rendelkeznie a szegmensek a horizontális skálázási térképet, valamint a felhasználói adatok esetleges szabályozási hiányosságok elhárítását módosítások végrehajtásához szükséges engedélyekkel.
* Forrástartomány (felosztása és egyesítése): egy felosztása és egyesítése művelet dolgozza fel a alacsony és magas kulcsok a tartományt. Adjon meg egy műveletet a korlátlan streameken működő magas kulcsérték, jelölje be a "felső kulcs is maximális" jelölőnégyzetet, és a magas kulcs mezőt hagyja üresen. Tartomány-tartományneveket, amelyeket nem kell pontosan egyezik a leképezés és a horizontális skálázási térképet a határokon. Ha nem ad meg semmilyen címtartomány típusú határokra egyáltalán a szolgáltatás lesz kikövetkeztetni a legközelebb esik meg automatikusan. A GetMappings.ps1 PowerShell-parancsfájlt használhatja a jelenlegi lekérdezéseket egy adott szegmenstérkép lekéréséhez.
* Split forrás viselkedés (split): A felosztás műveletek definiálása a pont a forrástartomány felosztása. Ehhez a horizontális skálázási kulcs, ahol azt szeretné, hogy megtörténjen a felosztás megadásával. Használja a választógomb adja meg, hogy kívánja-e a tartomány (kivéve a Felosztás kulcs) alsó részén áthelyezése, illetve hogy szeretné-e a felső rész áthelyezése (a felosztás kulcsot is beleértve).
* Forrás Shardlet (áthelyezése): műveletek eltérnek felosztása és egyesítése operations, nincs szükségük a forrás leírására széles áthelyezése. Áthelyezés forrást egyszerűen azonosítja a horizontális skálázási kulcs értékét, amelyet át szeretne.
* Cél (split) szegmens: Miután az adatokat a forrás a felosztási művelet, meg kell határoznia, ahová az adatokat a cél Azure SQL Database-kiszolgáló és adatbázis nevének megadásával kell másolni.
* Céltartomány (egyesítés): egyesítési műveletek shardlet át egy meglévő szegmensben. A meglévő szegmens azonosítható azáltal, hogy a meglévő tartomány egyesítés a kívánt címtartomány típusú határokra.
* Köteg mérete: A Köteg mérete határozza meg, hogy a kapcsolat nélküli során az adatok áthelyezése egyszerre shardlet száma. Ez az egész szám használható a kisebb értékek Ha hosszú időszakok shardlet az érzékeny áll. A nagyobb értékek növeli az idő, amely egy adott shardlet offline de javíthatja a teljesítményt.
* A művelet azonosítója (Mégse): Ha már nincs szükség egy folyamatban lévő műveletet, megszakíthatja a műveletet azáltal, hogy a művelet azonosítója ebben a mezőben. A kérelem állapotát tábla lehet lekérdezni a művelet azonosítója (lásd a szakasz 8.1) vagy a böngésző, ahol a kérelmet küldte el a kimenetből.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
A felosztási-egyesítési szolgáltatás jelenlegi implementációja el az alábbi követelmények és korlátozások érvényesek: 

* A szegmensek létezik, és a egy ezekben a szegmensekben felosztási-egyesítési művelet végrehajtása előtt a szegmenstérkép regisztrálni kell. 
* A szolgáltatás nem hoz létre táblák vagy bármely más adatbázis-objektumok automatikusan a műveleteit részeként. Ez azt jelenti, hogy a séma összes horizontálisan particionált táblák és referencia táblák kell léteznie, a cél szegmens bármely felosztása/egyesítése/áthelyezési művelet előtt. Horizontálisan particionált táblák különösen kell megadni a tartományban, ahol új shardlet felosztása/egyesítése/áthelyezési művelet fel kell venni. Ellenkező esetben a művelet sikertelen lesz a cél szegmens a kezdeti konzisztencia-ellenőrzést. Azt is vegye figyelembe az adatok másolásakor csak ha a referencia-tábla üres-e, és, hogy más egyidejű semmiféle garanciát nem konzisztencia jelentenek írási művelet a referencia táblák mutató. Azt javasoljuk, hogy ez: Ha a felosztó/egyesítő műveletek fut, nincs más írási műveleteket módosítja a referencia táblák.
* A szolgáltatás egy egyedi indexet vagy kulcs, amely tartalmazza a jobb teljesítmény és megbízhatóság nagy shardlet a horizontális skálázási kulcs által létrehozott sor identitás támaszkodik. Ez lehetővé teszi az adatok áthelyezése egy még részletesebben részletességet használni, csupán a horizontális skálázási kulcs értékét, mint a szolgáltatás. Ez segít csökkenteni a maximális mennyisége naplózási helyet és a zárolásokat, amelyek szükségesek a művelet során. Fontolja meg egy egyedi indexet vagy egy elsődleges kulcs, köztük a horizontális skálázási kulcs egy adott táblán, ha azt szeretné, hogy a táblázat használata felosztása és egyesítése /-áthelyezési kérelem létrehozása. Teljesítménybeli megfontolások miatt a horizontális skálázási kulcs a kulcs vagy az index a vezető oszlop kell lennie.
* A kérelem feldolgozása során egyes shardletek adatok mind a forrás- és a cél szegmens a jelen lehetnek. Ez azért szükséges, a shardlet mozgás során hibák ellen. Felosztási-egyesítési a szegmenstérkép-integrációja biztosítja, hogy az adatok függő útválasztási API-k használatával keresztüli kapcsolat a **OpenConnectionForKey** módszerrel a szegmenstérkép nem jelenik meg semmilyen inkonzisztens köztes állapotok. Azonban ha használata nélkül a forrás vagy a cél szegmensek csatlakozik a **OpenConnectionForKey** metódus, köztes inkonzisztens állapotok előfordulhat, hogy láthatók, ha felosztás/egyesítés/áthelyezési kéréseket fog. Ezek a kapcsolatok részleges vagy ismétlődő eredmények időzítése vagy a mögöttes a kapcsolat szegmens függően előfordulhat, hogy megjelenítése. Ezt a korlátozást jelenleg a rugalmas méretezés több-Shard-lekérdezések által létrehozott kapcsolatok is tartalmaz.
* A felosztási-egyesítési szolgáltatás a metaadatokat tároló adatbázis különböző szerepkörök között nem kell megosztani. Például az átmeneti állapotában is futó felosztási-egyesítési szolgáltatás szerepkör kell mutatnia, mint az éles szerepkör különböző metaadat-adatbázishoz.

## <a name="billing"></a>Számlázás
A felosztási-egyesítési szolgáltatás fut, felhőalapú szolgáltatásként a Microsoft Azure-előfizetésében. Ezért a cloud services díját kell megfizetni a szolgáltatáspéldányt. Kivéve, ha gyakran felosztása/egyesítése/áthelyezési műveleteket végez, javasoljuk, hogy a szétválasztás és egyesítés felhőszolgáltatás törlése. Amely költséget futtatásához, vagy üzembe helyezett cloud service-példányok. Újbóli üzembe helyezés, és indítsa el a könnyen futtatható konfiguráció felosztása és egyesítése műveletek elvégzésére van szüksége. 

## <a name="monitoring"></a>Figyelés
### <a name="status-tables"></a>Állapot-táblák
A felosztási-egyesítési szolgáltatás biztosítja a **RequestStatus** a metaadatokat tároló adatbázis figyelés befejezett és folyamatban lévő kérelmek táblájában. A táblázat minden egyes felosztási-egyesítési kérelem ezt a példányt a felosztási-egyesítési szolgáltatás elküldött sor. Kínál az egyes kérések a következő információkat:

* **Időbélyeg**: dátumát és időpontját, amikor a kérelem meg lett elindítva.
* **Műveletazonosító:**: egy GUID Azonosítót, amely egyedileg azonosítja a kérelmet. A kérelem is használható, szakítsa meg a műveletet, amíg még mindig folyamatban.
* **Állapot**: a kérés aktuális állapotát. Folyamatban lévő kérések esetén, amelyben a kérelme, mert a jelenlegi fázis is felsorolja.
* **CancelRequest**: azt A jelzőt, amely azt jelzi, hogy a kérelem meg lett szakítva.
* **Folyamat**: egy százalékos becslése a művelet befejezését. 50 érték azt jelzi, hogy a művelet körülbelül 50 % kész.
* **Részletek**: az XML-érték, amely egy részletes jelentést biztosít. Az állapotjelentés sorok részhalmazához forrásból másolandó cél rendszeresen frissül. Hibák vagy kivételek Ez az oszlop a hibával kapcsolatos részletes információkat is tartalmaz.

### <a name="azure-diagnostics"></a>Azure Diagnostics
A felosztási-egyesítési szolgáltatás használja az Azure Diagnostics monitorozást és diagnosztikát az Azure SDK 2.5-ös alapján. A diagnosztikai konfigurációja szabályozhatja az itt leírtak: [Diagnosztikának az Azure Cloud Services és Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). A letöltött csomag tartalmazza a két diagnosztikai konfiguráció – egy, a webes szerepkör és egy, a feldolgozói szerepkör esetében. Ezek a szolgáltatás magába kövessék a [a Microsoft Azure Cloud Service Fundamentals](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Naplófájl teljesítményszámlálók, az IIS-naplók, Windows-eseménynaplók és felosztási-egyesítési alkalmazás eseménynaplóit kapcsolatos definíciókat tartalmazza. 

## <a name="deploy-diagnostics"></a>Diagnosztikai üzembe helyezése
Figyelési és diagnosztikai konfiguráció használatával a webes és feldolgozói szerepkörök a NuGet-csomag által nyújtott diagnosztika engedélyezéséhez futtassa a következő parancsokat az Azure PowerShell használatával: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Hogyan ide a diagnosztika beállításokat konfigurálhat és telepíthet további tájékoztatást talál: [Diagnosztikának az Azure Cloud Services és Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Diagnosztikai beolvasása
A diagnosztikai könnyen elérhető a Visual Studio Server Explorerben a Server Explorer fa Azure részében. Nyissa meg a Visual Studio-példányon, és a menüsávon kattintson a nézet és a Server Explorerben. Kattintson a csatlakozás az Azure-előfizetéshez az Azure ikonra. Ezután lépjen az Azure Storage -> -> <your storage account> -> táblák, -> WADLogsTable. További információkért lásd: [tárolási erőforrások tallózása a Server Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

A fenti ábrán vannak kiemelve a WADLogsTable a felosztási-egyesítési szolgáltatás napló részletes eseményeit tartalmazza. Vegye figyelembe, hogy az alapértelmezett konfiguráció, a letöltött csomag éles környezet részesíti előnyben. Ezért a milyen naplók és -számlálók kikerülnek a szolgáltatáspéldányok időköz nagy méretű (5 perc). A fejlesztéshez és teszteléshez csökkentheti az időközt a diagnosztikai beállítások, a weben vagy a feldolgozói szerepkör az igényeinek megfelelően. Kattintson a jobb gombbal a szerepkör a Visual Studio Server Explorer (lásd fent), majd az átviteli időszak a diagnosztika a konfigurációs beállításokat a párbeszédpanelen: 

![Konfiguráció][3]

## <a name="performance"></a>Teljesítmény
Általában jobb teljesítményt, hogy minél nagyobb, több nagy teljesítményű szolgáltatásszintek az Azure SQL Database várható. A szolgáltatás magasabb szinten magasabb i/o-, Processzor- és memória kiosztásokat a tömeges másolási előnyeit, és törlési műveletek a felosztási-egyesítési szolgáltatás által használt. Éppen ezért növelése a szolgáltatási rétegben, és ezeket az adatbázisokat az egy meghatározott, korlátozott ideig.

A szolgáltatás a normál működés részeként érvényesítési lekérdezéseket is végez. A lekérdezések érvényesítés keresse meg a célként megadott tartomány adatainak váratlan jelenlétét, és győződjön meg arról, hogy bármely felosztása/egyesítése/áthelyezési művelet elindítja a konzisztens. Ezek a lekérdezések összes működnek a horizontális skálázás kulcstartományokkal határozzák meg a műveletet hatókörének és a Köteg mérete, a kérelem definíciójának részeként. Ezeket a lekérdezéseket akkor teljesítenek a legjobban, ha index jelen, a horizontális skálázási kulcs oszlopként vezető. 

Emellett a horizontális skálázási kulcs a vezető oszlop egyediségének tulajdonságot lehetővé teszi a szolgáltatás egy optimalizált módszerrel, amely korlátozza a naplózási helyet és memória szempontjából erőforrás-használat. Ez a tulajdonság egyedi-e nagy mennyiségű adat méretek (általában újabb 1 GB-os) áthelyezése szükséges. 

## <a name="how-to-upgrade"></a>Frissítése
1. Kövesse a [felosztási-egyesítési szolgáltatás üzembe helyezése](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Módosítsa a felhőszolgáltatás konfigurációs fájljához, hogy az új konfigurációs paramétereket az felosztási-egyesítési üzembe helyezéshez. Egy új kötelező paraméter a titkosításhoz használt tanúsítvány adatait. Ehhez egyszerűen az hasonlítsa össze a letöltés a meglévő konfiguráció szemben az új konfigurációs sablon fájlt. Ne feledje megadni a "DataEncryptionPrimaryCertificateThumbprint" és "DataEncryptionPrimary" beállításait a webes és a feldolgozói szerepkör.
3. Üzembe helyezés előtt a frissítés az Azure-ba, győződjön meg arról, hogy minden jelenlegi felosztási-egyesítési művelet befejezése után. Könnyedén megteheti ezt a szétválasztás és egyesítés metaadatokat tároló adatbázis folyamatban lévő kérések RequestStatus és PendingWorkflows táblázatok lekérdezésével.
4. Frissítse a meglévő felhőszolgáltatás üzembe helyezésének a szétválasztás és egyesítés esetén az Azure-előfizetésében az új csomag és a frissített konfigurációs fájlt.

Felosztási-egyesítési frissítése új metaadat-adatbázis kiépítése nem kell. Az új verzió automatikusan frissíti a meglévő metaadatokat tároló adatbázis az új verzióra. 

## <a name="best-practices--troubleshooting"></a>Ajánlott eljárások és hibaelhárítás
* Határozzon meg egy tesztelési bérlőn, és a legfontosabb felosztása/egyesítése/áthelyezési műveleteket a tesztbérlővel gyakorolja több szegmens között. Győződjön meg arról, hogy minden metaadat helyesen van definiálva a horizontális skálázási térképet, hogy a műveletek sérti korlátozások vagy a külső kulcsok.
* Tartsa a tesztelési célú bérlői adatok mérete fölött a legnagyobb bérlő annak érdekében, hogy nem adatok mérete eléri a maximális adatméret kapcsolatos problémákat. Ez segít a egyetlen új bérlő mozgáshoz szükséges időt a felmérheti a felső határt. 
* Győződjön meg arról, hogy a séma lehetővé teszi a törlést. A felosztási-egyesítési szolgáltatás lehetővé teszi távolíthatja el az adatokat a forrás-szegmens után az adatok másolása sikeresen megtörtént a cél szükséges. Ha például **triggerek törlése** megakadályozhatja, hogy a szolgáltatás törlésével az adatokat a forrás és a sikertelen műveletek okozhat.
* A horizontális skálázási kulcs a vezető oszlop legyen az elsődleges kulcs, vagy egyedi index definícióját. Ezzel biztosítható a felosztása és egyesítése lekérdezések érvényesítés, és a tényleges adatok mozgását és törlési műveletek horizontális skálázási kulcstartományokkal mindig működésbe, amelyek a lehető legjobb teljesítményt.
* A régiótól és központban, amelyen az adatbázisok találhatók a felosztási-egyesítési szolgáltatás elhelyezésének engedélyezése. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

