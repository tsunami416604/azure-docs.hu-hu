---
title: Adatok mozgatása kiterjesztett felhőalapú adatbázisok között
description: Bemutatja, hogyan kezelheti a szilánkok és adatok áthelyezése egy saját üzemeltetésű szolgáltatás rugalmas adatbázis API-k használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c7eb1670ee911895bdba23921845b8795f4998af
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811305"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Adatok mozgatása kiterjesztett felhőalapú adatbázisok között

Ha Ön szoftver, mint szolgáltatásfejlesztő, és hirtelen az alkalmazás óriási keresleten megy keresztül, akkor be kell fogadnia a növekedést. Így további adatbázisokat (szilánkokat) ad hozzá. Hogyan terjesztheti újra az adatokat az új adatbázisokban az adatok integritásának megzavarása nélkül? A **felosztásos egyesítési eszközzel** adatokat helyezhet át a korlátozott adatbázisokból az új adatbázisokba.  

A felosztásos egyesítési eszköz Azure-webszolgáltatásként fut. A rendszergazda vagy fejlesztő az eszköz segítségével átad shardlets (adatok egy shard) különböző adatbázisok (szilánkok) között. Az eszköz shard térkép-kezelés segítségével karbantartja a szolgáltatás metaadat-adatbázis, és biztosítja a konzisztens leképezések.

![Áttekintés][1]

## <a name="download"></a>Letöltés

[Microsoft.Azure.SqlDatabase.elasticscale.service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentáció

1. [Rugalmas adatbázis Split-Merge eszköz oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Biztonsági konfiguráció felosztása](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Biztonsági szempontok a felosztásos egyesítésre vonatkozó szempontokkal](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
5. [Meglévő adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Rugalmas adatbázis-eszközök](sql-database-elastic-scale-introduction.md)
7. [Rugalmas adatbázis-eszközök szószedet](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Miért érdemes használni a felosztott egyesítési eszközt?

- **Rugalmasság**

  Az alkalmazásoknak rugalmasan kell nyúlniuk egyetlen Azure SQL DB-adatbázis korlátain túl. Az eszközzel szükség szerint áthelyezi az adatokat az új adatbázisokba, miközben megőrzi az integritást.

- **Split a növekedéshez**

  A robbanásveszélyes növekedés kezeléséhez szükséges teljes kapacitás növelése érdekében hozzon létre további kapacitást az adatok fokozatos növelésével és fokozatosan több adatbázis között való elosztásával, amíg a kapacitásigények teljesültek. Ez egy kiváló példa a **felosztási** funkcióra.

- **Egyesítés zsugorításhoz**

  A kapacitásigények a vállalkozás szezonális jellege miatt csökkennek. Az eszköz lehetővé teszi, hogy kevesebb méretezési egységre skálázható, amikor az üzleti tevékenység lelassul. A rugalmas skálázás felosztása szolgáltatás "egyesítése" funkció ezt a követelményt fedezi.

- **Hotspotok kezelése shardlets áthelyezésével**

  Adatbázisonként több bérlővel a szilánkok felosztása egyes szegmensek kapacitásszűk keresztmetszetek vezethet. Ez a shardlets újrakiosztása vagy a foglalt shardlets áthelyezése új vagy kevésbé használt szilánkok.

## <a name="concepts--key-features"></a>Fogalmak & főbb jellemzőik

- **Ügyfél által üzemeltetett szolgáltatások**

  A felosztásos egyesítés ügyfél által üzemeltetett szolgáltatásként kerül kiszállításra. A szolgáltatást a Microsoft Azure-előfizetésében kell üzembe helyeznie és üzemeltetnie. A NuGet-ből letöltött csomag tartalmaz egy konfigurációs sablont, amely az adott központi telepítéssel kapcsolatos információkat tartalmazza. A részleteket a [felosztásos egyesítésoktató](sql-database-elastic-scale-configure-deploy-split-and-merge.md) iszatban találja. Mivel a szolgáltatás az Azure-előfizetésben fut, szabályozhatja és konfigurálhatja a szolgáltatás legtöbb biztonsági aspektusát. Az alapértelmezett sablon tartalmazza a TLS konfigurálásának lehetőségeit, a tanúsítványalapú ügyfélhitelesítést, a tárolt hitelesítő adatok titkosítását, a DoS-védelmet és az IP-korlátozásokat. A biztonsági szempontokról a következő [dokumentumfelosztott egyesítésbiztonsági konfigurációban](sql-database-elastic-scale-split-merge-security-configuration.md)talál további információt.

  Az alapértelmezett üzembe helyezett szolgáltatás egy feldolgozóval és egy webes szerepkörrel fut. Mindegyik az A1 virtuális gép méretét használja az Azure Cloud Servicesben. Bár ezeket a beállításokat nem módosíthatja a csomag üzembe helyezésekor, módosíthatja őket a futó felhőszolgáltatásban (az Azure Portalon keresztül) végzett sikeres üzembe helyezés után. Vegye figyelembe, hogy a feldolgozói szerepkört technikai okokból csak egy példányra szabad konfigurálni.

- **Shard térkép integrációja**

  A felosztásos egyesítési szolgáltatás együttműködik az alkalmazás szegmenstérképével. Ha a felosztásos egyesítési szolgáltatás használatával felosztása vagy egyesítése tartományok vagy a szegmensek közötti áthelyezése, a szolgáltatás automatikusan tartja a szegmens leképezés naprakész. Ehhez a szolgáltatás csatlakozik az alkalmazás shard map manager adatbázisához, és a felosztási/egyesítési/áthelyezési kérelmek előrehaladása ként fenntartja a tartományokat és a leképezéseket. Ez biztosítja, hogy a szegmenstérkép mindig egy naprakész nézetet jelenít meg, amikor a felosztásos egyesítési műveletek folynak. A felosztási, egyesítési és shardlet-mozgatási műveletek a shardlets kötegének a forrásszegmensből a célszegmensbe való áthelyezésével valósítják meg. A shardlet-mozgási művelet során az aktuális köteg tárgyát képező shardletek offline módban vannak megjelölve a shard térképen, és nem érhetők el az **OpenConnectionForKey** API-t használó adatfüggő útválasztási kapcsolatok hoz.

- **Egységes shardlet kapcsolatok**

  Amikor a shardlets új kötegének adatáthelyezése elindul, a shardletet tároló shardlet-shard-hoz megadott adatfüggő útválasztási kapcsolatok megszakadnak, és a shard map API-kból a shardlets-hez való későbbi csatlakozások blokkolva vannak, miközben az adatáthelyezés folyamatban van az inkonzisztenciák elkerülése érdekében. Az ugyanazon a shardon lévő többi shardlet-hez való csatlakozás is meghal, de az újrapróbálkozáskor azonnal sikeres lesz. A köteg áthelyezése után a shardlets van megjelölve online újra a cél shard, és a forrásadatok törlődnek a forrás shard. A szolgáltatás végigmegy ezeken a lépéseken minden kötegnél, amíg az összes shardlets át nem lett helyezve. Ez több kapcsolatleölési művelethez vezet a teljes felosztási/egyesítési/áthelyezési művelet során.  

- **A shardlet elérhetőségének kezelése**

  A kapcsolat leölése az aktuális köteg shardlets a fent tárgyalt korlátozza a hatókörelérhetetlenségét, hogy egy köteg shardlets egy időben. Ez inkább egy olyan megközelítés, ahol a teljes shard offline maradna az összes shardlets során egy felosztási vagy egyesítési művelet. A köteg mérete, amely et az egyszerre áthelyezhető különálló shardletek számaként határoznak meg, egy konfigurációs paraméter. Az alkalmazás rendelkezésre állásától és teljesítményigényeitől függően minden egyes felosztási és egyesítési művelethez megadható. Vegye figyelembe, hogy a shard térképen zárolt tartomány nagyobb lehet, mint a megadott kötegméret. Ennek az az oka, hogy a szolgáltatás úgy választja ki a tartomány méretet, hogy az adatokban lévő összegösszegszerint megegyezik a kötegmérettel. Ez különösen a ritkán lakott szilánkok esetében fontos.

- **Metaadat-tárolás**

  A felosztott egyesítési szolgáltatás egy adatbázist használ az állapot ának megőrzéséhez és a naplók megőrzéséhez a kérelmek feldolgozása során. A felhasználó létrehozza ezt az adatbázist az előfizetésében, és biztosítja a kapcsolati karakterláncot a szolgáltatás központi telepítéséhez szükséges konfigurációs fájlban. A felhasználó szervezetének rendszergazdái is csatlakozhatnak ehhez az adatbázishoz, hogy áttekintsék a kérelmek előrehaladását, és részletes információkat vizsgáljanak a lehetséges hibákról.

- **Szilánkos tudatosság**

  A felosztásos egyesítési szolgáltatás különbséget tesz (1) szilánkos táblák, (2) hivatkozási táblák és (3) normál táblák között. A felosztási/egyesítési/áthelyezési művelet szemantikája a használt tábla típusától függ, és a következőképpen határozható meg:

  - **Szilánkos asztalok**

    A felosztás, egyesítés és áthelyezési műveletek shardleteket helyeznek át a forrásból a célszegmensbe. A teljes kérelem sikeres befejezése után ezek a shardlets már nincsenek jelen a forráson. Vegye figyelembe, hogy a céltábláknak a célszegmensen kell lennie, és a művelet feldolgozása előtt nem tartalmazhatnak adatokat a céltartományban.

  - **Referenciatáblák**

    Referenciatáblák, a felosztás, egyesítése és áthelyezése műveletek másolja az adatokat a forrásból a cél shard. Vegye figyelembe azonban, hogy nem történik változás a cél shard egy adott tábla, ha egy sor már jelen van ebben a táblázatban a cél. A feldolgozáshoz a tábla minden referenciatábla másolási művelethez üresnek kell lennie.

  - **Egyéb táblázatok**

    Más táblák is jelen lehetnek a felosztási és egyesítési művelet forrásán vagy célján. A felosztásos egyesítési szolgáltatás figyelmen kívül hagyja ezeket a táblákat az adatmozgatási vagy másolási műveleteknél. Ne feledje azonban, hogy korlátozások esetén zavarhatják ezeket a műveleteket.

    A referencia- és a szilánkos táblák `SchemaInfo` ra vonatkozó információkat a shard térképAPI-k biztosítják. A következő példa bemutatja ezeknek az API-knak a használatát egy adott shard map manager objektumon:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    A "régió" és a "nemzet" táblák hivatkozási táblákként vannak definiálva, és felosztási/egyesítési/áthelyezési műveletekkel lesznek másolva. A "vevő" és a "rendelések" viszont szilánkos táblákként vannak meghatározva. `C_CUSTKEY`és `O_CUSTKEY` szolgálnak a szilánkok kulcsa.

- **Hivatkozási integritás**

  A felosztásos egyesítési szolgáltatás elemzi a táblák közötti függőségeket, és az idegen kulcs-elsődleges kulcs kapcsolatok at használja a referenciatáblák és shardlets áthelyezési műveleteinek szakaszában. Általánosságban elmondható, hogy a hivatkozási táblákat először függőségi sorrendben másolja, majd a shardleteket az egyes kötegekfüggőségei szerint másolja. Erre azért van szükség, hogy a célszegmens FK-PK-korlátozásai az új adatok megérkezésével tiszteletben legyenek.

- **Shard térkép konzisztencia és végleges befejezése**

  Hibák esetén a felosztásos egyesítési szolgáltatás minden kimaradás után folytatja a műveleteket, és célja a folyamatban lévő kérelmek végrehajtása. Azonban előfordulhatnak helyreállíthatatlan helyzetek, például, amikor a cél shard elvész, vagy javíthatatlan. Ilyen körülmények között néhány shardlets, amelyek et kellett volna mozgatni továbbra is a forrás shard továbbra is a forrás shard. A szolgáltatás biztosítja, hogy a shardlet-leképezések csak a szükséges adatok sikeres másolása után frissülnek. Shardlets csak akkor törlődik a forrás, ha az összes adatot másolta a cél, és a megfelelő leképezések sikeresen frissültek. A törlési művelet a háttérben történik, miközben a tartomány már online a célszegmensen. A felosztásos egyesítési szolgáltatás mindig biztosítja a szegmenstérképen tárolt leképezések helyességét.

## <a name="the-split-merge-user-interface"></a>Az osztott egyesítésű felhasználói felület

A felosztásos egyesítési szolgáltatáscsomag munkavégző szerepkört és webes szerepkört tartalmaz. A webes szerepkör segítségével interaktív módon küldhet felosztásos egyesítési kérelmeket. A felhasználói felület fő összetevői a következők:

- **Művelettípus**

  A művelet típusa egy választógomb, amely a szolgáltatás által a kéréshez végrehajtott művelet típusát vezérli. Választhat a felosztás, egyesítés és áthelyezés forgatókönyvek között. Egy korábban elküldött műveletet is visszavonhat. Használhatja split, egyesítése és áthelyezése kérelmek tartomány szilánkleképezések. A shard térképek listája csak támogatja az áthelyezési műveleteket.

- **Shard térkép**

  A kérelemparaméterek következő szakasza a szegmenstérkép és a shard térképet üzemeltető adatbázis adatait ismerteti. Különösen meg kell adnia az Azure SQL Database-kiszolgáló és a shardmap ot üzemeltető adatbázis nevét, a shard térkép-adatbázishoz való csatlakozáshoz szükséges hitelesítő adatokat, és végül a szegmenstérkép-leképezés nevét. Jelenleg a művelet csak egyetlen hitelesítő adatokat fogad el. Ezek a hitelesítő adatok kell rendelkeznie a megfelelő engedélyekkel a shard térkép módosításai, valamint a szegmensek felhasználói adatai.

- **Forrástartomány (felosztás és egyesítés)**

  A felosztásos és egyesítési művelet egy tartományt az alacsony és a magas gombbal dolgoz fel. Ha egy nagy kulcsú műveletet szeretne megadni, jelölje be a "Magas kulcs max" jelölőnégyzetet, és hagyja üresen a magas kulcsmezőt. A megadott tartománykulcs-értékeknek nem kell pontosan egyeztetniük a leképezést és annak határait a shard térképen. Ha egyáltalán nem ad meg tartományhatárokat, a szolgáltatás automatikusan a legközelebbi tartományt hozza meg. A GetMappings.ps1 PowerShell-parancsfájl segítségével lekérheti az aktuális leképezéseket egy adott szegmensleképezésben.

- **Forrás viselkedésének felosztása (felosztott)**

  Felosztási műveletek esetén adja meg a forrástartomány felosztásához megadott pontot. Ehhez adja meg a szilánkos kulcsot, ahol a felosztást szeretné történni. A választógomb balgombjával adja meg, hogy a tartomány alsó részét (kivéve az osztott kulcsot) helyezze-e át, vagy a felső részt (beleértve az osztott kulcsot is) helyezze-e el.

- **Forrás shardlet (mozgás)**

  Az áthelyezési műveletek eltérnek a felosztási vagy egyesítési műveletektől, mivel nincs szükségük tartományra a forrás leírásához. Az áthelyezési forrást egyszerűen azonosítja a szilánkos kulcs értéke, amelyet át helyezni kíván.

- **Célszegmens (osztott)**

  Miután megadta az adatokat a felosztási művelet forrásáról, meg kell határoznia, hogy az adatokat hol kell másolni az Azure SQL Db kiszolgáló és a cél adatbázisnevének megadásával.

- **Céltartomány (egyesítés)**

  Egyesítési műveletek átad nak shardlets egy meglévő shard. A meglévő szegmens ta- és a meglévő tartomány azon tartományhatárainak megadásával azonosíthatja, amelyekkel egyesíteni szeretne.

- **Köteg mérete**

  A kötegméret szabályozza a shardlets, amelyek offline állapotba kerülnek az adatmozgatás során. Ez egy egész érték, ahol kisebb értékeket használhat, ha érzékeny a shardlets hosszú állásidőre. A nagyobb értékek növelik az időt, hogy egy adott shardlet offline állapotban van, de javíthatja a teljesítményt.

- **Művelet azonosítója (Mégse)**

  Ha van egy folyamatban lévő művelet, amelyre már nincs szükség, a műveletet megszakíthatja, ha megadja a műveletazonosítóját ebben a mezőben. A műveletazonosítót a kérelem állapottáblájából (lásd a 8.1 szakaszban) vagy a böngésző kimenetéből, ahol a kérelmet küldte.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

A felosztásos egyesítési szolgáltatás jelenlegi megvalósítására a következő követelmények és korlátozások vonatkoznak:

- A szegmensek kell léteznie, és regisztrálni kell a shard térképen, mielőtt egy felosztásos egyesítési művelet et ezeken a szegmenseken lehet végrehajtani.
- A szolgáltatás nem hoz létre automatikusan táblákat vagy más adatbázis-objektumokat a műveletek részeként. Ez azt jelenti, hogy a séma az összes szilánkos táblák és a referenciatáblák kell léteznie a cél shard bármely felosztási/egyesítési/áthelyezési művelet előtt. A szilánkos tábláknak különösen üresnek kell lenniük abban a tartományban, ahol új shardleteket kell hozzáadni egy felosztási/egyesítési/áthelyezési művelettel. Ellenkező esetben a művelet sikertelen lesz a célszegmens kezdeti konzisztencia-ellenőrzése. Azt is vegye figyelembe, hogy a referenciaadatok másolása csak akkor történik meg, ha a referenciatábla üres, és nincsenek konzisztencia-garanciák a referenciatáblákon szereplő egyéb egyidejű írási műveletektekintetében. Ezt javasoljuk: split/merge műveletek futtatásakor más írási műveletek nem módosítják a referenciatáblákat.
- A szolgáltatás támaszkodik egy egyedi index vagy kulcs, amely tartalmazza a nagy shardlets teljesítményének és megbízhatóságának javítása érdekében egy egyedi index vagy kulcs által létrehozott soridentitás. Ez lehetővé teszi, hogy a szolgáltatás az adatok áthelyezése még finomabb, mint a szilánkos kulcs értéke. Ez segít csökkenteni a művelet során szükséges maximális naplóterületet és zárolásokat. Fontolja meg egy egyedi index vagy egy elsődleges kulcs létrehozása, beleértve a szilánkos kulcsot egy adott táblában, ha azt szeretné használni, hogy a tábla split/merge/move kérelmek. A teljesítmény miatt a szilánkos kulcs kell a vezető oszlop a kulcs vagy az index.
- A kérelem feldolgozása során néhány shardlet adatok jelennek meg mind a forrás és a cél shard. Ez szükséges a shardlet mozgás közbeni hibák elleni védelemhez. A felosztásos egyesítés és a szegmenstérkép integrációja biztosítja, hogy az adatfüggő útválasztási API-kon keresztül a szegmenstérképen az **OpenConnectionForKey** metódushasználatával létesített kapcsolatok ne lássanak inkonzisztens köztes állapotokat. Azonban az **OpenConnectionForKey** metódus használata nélkül a forráshoz vagy a célszegmensekhez való csatlakozáskor inkonzisztens köztes állapotok láthatók lehetnek a felosztási/egyesítési/áthelyezési kérelmek során. Ezek a kapcsolatok részleges vagy ismétlődő eredményeket jeleníthetnek meg a kapcsolat időzítésétől vagy a kapcsolat alapjául szolgáló szegmenstől függően. Ez a korlátozás jelenleg tartalmazza a rugalmas méretezéstöbbsű-shard-lekérdezések által hozott kapcsolatokat.
- A felosztott egyesítési szolgáltatás metaadat-adatbázisa nem osztható meg a különböző szerepkörök között. Például az átmeneti állapotban futó felosztott egyesítési szolgáltatás szerepkörének az éles szerepkörnél eltérő metaadat-adatbázisra kell mutatnia.

## <a name="billing"></a>Számlázás

A felosztott egyesítési szolgáltatás felhőszolgáltatásként fut a Microsoft Azure-előfizetésében. Ezért a felhőszolgáltatások díjai vonatkoznak a szolgáltatás példányára. Hacsak nem hajt végre gyakran felosztási/egyesítési/áthelyezési műveleteket, javasoljuk, hogy törölje a felosztott egyesítésű felhőszolgáltatást. Ez költségeket takarít meg a futó vagy üzembe helyezett felhőszolgáltatás-példányok. Újratelepítheti és elindíthatja a könnyen futtatható konfigurációt, amikor felosztási vagy egyesítési műveleteket kell végrehajtania.

## <a name="monitoring"></a>Figyelés

### <a name="status-tables"></a>Állapottáblák

A split-merge szolgáltatás biztosítja a **RequestStatus** tábla a metaadat-tároló adatbázis ban a befejezett és folyamatban lévő kérelmek figyelésére. A táblázat felsorolja a felosztásos egyesítési kérelem minden olyan felosztási kérelemhez egy sorát, amelyet a felosztásos egyesítési szolgáltatás ezen példányához küldtek el. Minden egyes kérelemre vonatkozóan a következő információkat adja meg:

- **Időbélyeg**

  A kérelem elindításának időpontja és dátuma.

- **OperationId**

  A kérelmet egyedileg azonosító GUID. Ez a kérés is használható a művelet megszakítására, amíg még folyamatban van.

- **Állapot**

  A kérelem aktuális állapota. Folyamatban lévő kérelmek esetén azt is felsorolja, hogy a kérelem milyen aktuális fázisban van.

- **Request visszavonása**

  Egy jelző, amely jelzi, hogy a kérés megszakadt-e.

- **Haladás**

  A művelet készültségi százalékának becslése. Az 50-es érték azt jelzi, hogy a művelet körülbelül 50%-os készültségi szint.

- **Részletek**

  Részletesebb állapotjelentést tartalmazó XML-érték. Az állapotjelentés rendszeres en megy, ahogy a sorok készletei a forrásból a célba kerülnek. Hibák vagy kivételek esetén ez az oszlop részletesebb információkat is tartalmaz a hibáról.

### <a name="azure-diagnostics"></a>Azure Diagnostics

A split-merge szolgáltatás az Azure SDK 2.5-ön alapuló Azure Diagnostics szolgáltatást használja a figyeléshez és a diagnosztikához. A diagnosztikai konfigurációt az itt ismertetett módon szabályozhatja: [Diagnosztika engedélyezése az Azure Cloud Servicesben és a virtuális gépeken.](../cloud-services/cloud-services-dotnet-diagnostics.md) A letöltési csomag két diagnosztikai konfigurációt tartalmaz - egyet a webes szerepkörhöz és egyet a feldolgozói szerepkörhöz. Tartalmazza a teljesítményszámlálók, az IIS-naplók, a Windows eseménynaplók és az alkalmazás felosztásos egyesítése eseménynaplók naplózására vonatkozó definíciókat.

## <a name="deploy-diagnostics"></a>Diagnosztikai eszközök telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Ha engedélyezni szeretné a figyelést és a diagnosztikát a NuGet csomag által biztosított webes és feldolgozói szerepkörök diagnosztikai konfigurációjával, futtassa a következő parancsokat az Azure PowerShell használatával:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

A diagnosztikai beállítások konfigurálásával és telepítésével kapcsolatos további tudnivalókitt: [Diagnosztika engedélyezése az Azure Cloud Servicesben és a virtuális gépeken.](../cloud-services/cloud-services-dotnet-diagnostics.md)

## <a name="retrieve-diagnostics"></a>Diagnosztika beolvasása

A diagnosztika könnyen elérheti a Visual Studio Server Explorer az Azure része a Server Explorer fa. Nyisson meg egy Visual Studio-példányt, és a menüsorban kattintson a Nézet és a Kiszolgálókezelő elemre. Az Azure-előfizetéshez való csatlakozáshoz kattintson az Azure ikonra. Ezután keresse meg az `<your storage account>` Azure -> Storage -> -> Tables -> WADLogsTable. További információt a [Kiszolgálókezelő című](https://msdn.microsoft.com/library/x603htbk.aspx)témakörben talál.

![WADLogsTábla][2]

A fenti ábrán kiemelt WADLogsTable a felosztásos egyesítési szolgáltatás alkalmazásnaplójából származó részletes eseményeket tartalmazza. Vegye figyelembe, hogy a letöltött csomag alapértelmezett konfigurációja éles környezetben történő telepítésre irányul. Ezért a naplók és számlálók lekérése a szolgáltatáspéldányok nagy (5 perc). A tesztelés és fejlesztés, csökkentse az intervallum módosításával a diagnosztikai beállításokat a web vagy a feldolgozói szerepkör az Ön igényeinek. Kattintson a jobb gombbal a Visual Studio Server Explorer ben betöltött szerepére (lásd fent), majd módosítsa az átviteli időszakot a párbeszédpanelen a Diagnosztika konfigurációs beállításaihoz:

![Konfiguráció][3]

## <a name="performance"></a>Teljesítmény

Általában jobb teljesítmény várható az Azure SQL Database magasabb, nagyobb teljesítményű szolgáltatási szintjeitől. A magasabb szolgáltatási szintek magasabb I/O-, PROCESS- és memórialefoglalásai a felosztásos egyesítési szolgáltatás által használt tömeges másolási és törlési műveletek számára előnyösek. Ebből az okból növelje a szolgáltatási szint csak az ilyen adatbázisok egy meghatározott, korlátozott ideig.

A szolgáltatás a szokásos műveletek részeként is végez érvényesítési lekérdezéseket. Ezek az érvényesítési lekérdezések ellenőrzik az adatok váratlan jelenlétét a céltartományban, és biztosítják, hogy bármely felosztási/egyesítési/áthelyezési művelet konzisztens állapotból induljon. Ezek a lekérdezések a művelet hatóköre és a kérelemdefiníció részeként megadott kötegméret által meghatározott kötegméret felett dolgoznak. Ezek a lekérdezések akkor teljesítenek a legjobban, ha egy index jelen van, amely a szegmenskulcsot vezető oszlopként rendelkezik.

Emellett egy egyediségi tulajdonság a szegmenskulcs, mint a vezető oszlop lehetővé teszi a szolgáltatás számára, hogy egy optimalizált megközelítést, amely korlátozza az erőforrás-felhasználás a naplótér és a memória. Ez az egyediségi tulajdonság nagy (általában 1 GB feletti) adatméretek áthelyezéséhez szükséges.

## <a name="how-to-upgrade"></a>Hogyan lehet frissíteni

1. Kövesse a [Felosztásegyesítési szolgáltatás telepítése című](sql-database-elastic-scale-configure-deploy-split-and-merge.md)részben leírt lépéseket.
2. Módosítsa a felhőszolgáltatás konfigurációs fájlját az osztott egyesítésközponti telepítéséhez, hogy tükrözze az új konfigurációs paramétereket. Az új kötelező paraméter a titkosításhoz használt tanúsítvány adatai. Ennek egyik egyszerű módja, ha összehasonlítja az új konfigurációs sablonfájlt a letöltésből a meglévő konfigurációval. Győződjön meg arról, hogy adja hozzá a beállításokat a "DataEncryptionPrimaryCertificateThumbprint" és a "DataEncryptionPrimary" mind a web és a feldolgozói szerepkör.
3. A frissítés üzembe helyezése előtt az Azure-ban győződjön meg arról, hogy az összes jelenleg futó felosztásos egyesítési műveletek befejeződtek. Ezt könnyedén megteheti, ha lekérdezi a RequestStatus és pendingmunkafolyamat táblákat a felosztott metaadat-adatbázisban a folyamatban lévő kérelmekhez.
4. Frissítse a meglévő felhőszolgáltatás-telepítést az Azure-előfizetésfelosztáshoz az új csomaggal és a frissített szolgáltatáskonfigurációs fájllal.

A frissítéshez nem kell új metaadat-adatbázist létesítenie a felosztott egyesítéshez. Az új verzió automatikusan frissíti a meglévő metaadat-adatbázist az új verzióra.

## <a name="best-practices--troubleshooting"></a>Ajánlott eljárások és hibaelhárítás

- Definiáljon egy tesztbérlőt, és gyakorolja a legfontosabb felosztási/egyesítési/áthelyezési műveleteket a tesztbérlővel több szegmensen keresztül. Győződjön meg arról, hogy az összes metaadat helyesen van definiálva a shard térképen, és hogy a műveletek nem sértik a korlátozásokat vagy az idegen kulcsokat.
- Tartsa a teszt bérlői adatok mérete meghaladja a legnagyobb bérlő maximális adatméretet, hogy megbizonyosodjon arról, hogy nem találkozik az adatok méretével kapcsolatos problémák. Ez segít felmérni a felső határ a bérlő áthelyezéséhez szükséges idő.
- Győződjön meg arról, hogy a séma lehetővé teszi a törlést. A felosztásos egyesítési szolgáltatás nak szüksége van arra, hogy távolítsa el az adatokat a forrásszegmensből, miután az adatok sikeresen átmásolva a cél. A **törlési eseményindítók megakadályozhatják** például, hogy a szolgáltatás törölje a forrásadatait, és a műveletek sikertelenségéhez vezethet.
- A szilánkos kulcs kell lennie a vezető oszlop az elsődleges kulcs vagy egyedi index definíciója. Ez biztosítja a legjobb teljesítményt a felosztási vagy egyesítési érvényesítési lekérdezések, valamint a tényleges adatáthelyezési és törlési műveletek, amelyek mindig a skálázási kulcs tartományok.
- A felosztásos egyesítési szolgáltatás elhelyezése abban a régióban és adatközpontban, ahol az adatbázisok tartózkodnak.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
