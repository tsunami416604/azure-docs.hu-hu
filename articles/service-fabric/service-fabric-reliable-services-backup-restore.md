---
title: Service Fabric biztonsági mentési és visszaállítási |} Microsoft Docs
description: Service Fabric biztonsági mentési és visszaállítási fogalmi dokumentációja
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: c90231d58ca8eb562aadb916c8667e2bee700b3a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Biztonsági mentése és visszaállítása a Reliable Services és Reliable Actors
Az Azure Service Fabric egy magas rendelkezésre állású platform, amely replikálja az állapot karbantartásához a magas rendelkezésre állás több csomópont között.  Így még akkor is, ha a fürtben egy csomópont meghibásodik, a szolgáltatások is elérhetőek. Amikor ez a platform által biztosított a beépített redundanciát lehet, hogy elegendő-e bizonyos, bizonyos esetekben célszerű a szolgáltatás számára az adatok biztonsági másolatát (külső áruházban).

> [!NOTE]
> Nagyon fontos biztonsági mentését, és állítsa vissza az adatokat (és tesztelje, hogy az elvárt módon működik), akkor helyreállíthatja az adatvesztés.
> 
> 

Például egy szolgáltatás előfordulhat, hogy biztonsági másolatot szeretne adatokat ahhoz, hogy megvédje a következő esetekben:

- A teljes Service Fabric-fürt állandó adatvesztés.
- A szolgáltatás partíció replikák többsége végleges adatvesztést
- Felügyeleti hibák, amelyek állapotát véletlenül lekérdezi törölt vagy sérült. Ez például akkor fordulhat elő, ha egy megfelelő jogosultsággal rendelkező rendszergazda tévesen törli a szolgáltatást.
- Hibák az szolgáltatásban adatvesztést okozhat. Ez például akkor fordulhat elő, a kód frissítése az írás a hibás egy megbízható gyűjtemény indításakor. Ebben az esetben a kód és az adatok is lehet, hogy egy korábbi állapotára visszaállítani.
- Kapcsolat nélküli adatok feldolgozása. Előfordulhat, hogy az adatok az üzleti intelligencia, külön-külön történik, a szolgáltatás, amely az adatokat hoz létre a kapcsolat nélküli feldolgozása kényelmes.

A biztonsági mentési/visszaállítási funkció lehetővé teszi a megbízható szolgáltatások API létrehozása és a biztonsági másolatok épülő szolgáltatások. A platform által biztosított biztonsági mentési API-k biztonsági mentését vagy mentéseit szolgáltatás partíció állapot nélkül blokkoló olvasási vagy írási műveletek engedélyezése. A visszaállítási API-k engedélyezése egy szolgáltatás partíció állapotba állítani a kiválasztott biztonsági másolatból.

## <a name="types-of-backup"></a>Biztonsági mentés típusai
Két biztonsági mentési lehetőség: teljes és növekményes.
Teljes biztonsági mentés olyan biztonsági mentésből a replika állapota újbóli létrehozásához szükséges összes adatot tartalmaz: ellenőrzőpontok és az összes naplófájl rögzíti.
Mivel az ellenőrzőpontokat és a napló rendelkezik, egy teljes biztonsági mentés önmagában állíthatók vissza.

A teljes biztonsági mentés a probléma merül fel, amikor nagy az ellenőrzőpontokat.
Például egy replikát, amelynek állapota 16 GB lesz az ellenőrzőpontok hozzáadásával körülbelül legfeljebb 16 GB.
Ha okunk van, a helyreállítási időkorlát: 5 perc, a biztonsági mentését, ötpercenként a replikának szüksége lesz.
Minden alkalommal, amikor biztonsági mentést készít kell másolni 50 MB mellett az ellenőrzőpontok 16 GB (konfigurálható használatával `CheckpointThresholdInMB`) értéke a naplókat.

![Teljes biztonsági mentés példa.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Ez a probléma megoldása növekményes biztonsági mentést, ha biztonsági mentés csak tartalmazza a módosított naplófájl rögzíti a legutóbbi biztonsági mentés óta.

![Növekményes biztonsági mentési példa.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Mivel a növekményes biztonsági mentések csak a változásokat a legutóbbi biztonsági mentés (nem tartalmazza a ellenőrzőpontokkal) óta, általában gyorsabb, de azok nem állítható vissza, hogy saját maguk.
A növekményes biztonsági mentés visszaállításához szükség a teljes biztonsági mentési láncolatát.
A biztonsági mentési láncolatát lánc csatlakozik a biztonsági mentések teljes biztonsági mentés kezdve, és egy egybefüggő növekményes biztonsági mentések számát.

## <a name="backup-reliable-services"></a>Biztonsági mentési megbízható szolgáltatások
A szolgáltatás Szerző el, hogy a biztonsági mentések és a biztonsági mentések tárolására szolgáló teljes körű vezérléssel rendelkezik.

A biztonsági mentést elindítani a szolgáltatást kell örökölt tagot függvényt `BackupAsync`.  
Biztonsági mentések tehető csak az elsődleges replikára változott, és igényelnek-e írási állapot engedélyezhető.

Az alábbi módon `BackupAsync` veszi a `BackupDescription` objektum, ahol egy adhat meg egy teljes vagy növekményes biztonsági mentés, valamint a visszahívási függvény, `Func<< BackupInfo, CancellationToken, Task<bool>>>` , amely nyílik meg, ha a biztonsági mentési mappája helyileg létrejött, és kell helyezni az egyes készen áll külső tárhelyen.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

A növekményes biztonsági mentés érvénybe kérelem meghiúsulhat `FabricMissingFullBackupException`. Ez a kivétel azt jelzi, hogy az alábbiak egyikét történik:

- a replika soha nem tartott egy teljes biztonsági mentés óta elsődleges, vált
- a naplóbejegyzéseket, mivel a rendszer csonkolta az utolsó biztonsági mentés néhány vagy
- az átadott replika a `MaxAccumulatedBackupLogSizeInMB` korlátot.

Felhasználók megnövelik a valószínűségét, hogy a növekményes biztonsági mentések el konfigurálásával `MinLogSizeInMB` vagy `TruncationThresholdFactor`.
Vegye figyelembe, hogy ezek növelése értékek növekszik az egyes replika lemezek használata terén.
További információkért lásd: [megbízható konfigurálása](service-fabric-reliable-services-configuration.md)

`BackupInfo` a biztonsági mentés, például a mappát, ahol a futtatókörnyezet a biztonsági mentés helye vonatkozóan nyújt információkat (`BackupInfo.Directory`). A visszahívási függvény áthelyezheti a `BackupInfo.Directory` egy külső tároló vagy egy másik helyre.  Ez a funkció is adja vissza egy logikai érték, amely jelzi, hogy volt sikeresen helyezheti át, a biztonsági mentési mappája a célhelyre.

A következő kód bemutatja, hogyan a `BackupCallbackAsync` módszer akkor használható a biztonsági mentés feltöltése az Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Az előző példában `ExternalBackupStore` a minta osztály, amely használja az Azure Blob storage szolgáltatással felületre és `UploadBackupFolderAsync` az módszer, amely a mappa tömöríti, és elhelyezi az Azure Blob-tárolóban.

Vegye figyelembe:

  - Lehetnek csak egy biztonsági mentési művelet egy replika az üzenetsoroktól egy adott időpontban. Egynél több `BackupAsync` egyszerre hívás kivételhibát `FabricBackupInProgressException` aktív biztonsági mentések egy korlátozni.
  - Ha egy replika átadja, amíg folyamatban van egy biztonsági mentés, a biztonsági mentés lehetséges, hogy nem már befejeződött. Így, Miután befejeződött a feladatátvétel, feladata a szolgáltatás a biztonsági mentés újraindítása figyelőn `BackupAsync` szükség szerint.

## <a name="restore-reliable-services"></a>Megbízható szolgáltatások visszaállítása
Általában az esetekben, amikor a visszaállítási művelet elvégzéséhez szükség lehet egyikébe ezen kategóriák:

  - A szolgáltatás elveszett adatok particionálása. Például a lemez (beleértve az elsődleges másodpéldány) partíció két kívüli három replikáinak lekérdezi sérült, vagy törlése. Az új elsődleges adatok visszaállítása biztonsági másolatból kell.
  - A teljes szolgáltatás elvész. Például a rendszergazda törli a teljes szolgáltatás, és így a szolgáltatás, és az adatok kell állítani.
  - A szolgáltatás replikált sérült alkalmazás adatokat (például azért, mert az alkalmazás hiba). Ebben az esetben a szolgáltatás tartalmaz frissíteni vagy állítja vissza a rendszer eltávolítja a meghibásodás okát, és nem sérült adatok át lehet visszaállítani.

Számos különböző módszer is előfordulhatnak, amíg fel néhány példa használatával `RestoreAsync` helyreállítása a fenti forgatókönyvekben.

## <a name="partition-data-loss-in-reliable-services"></a>Partíció adatvesztést a Reliable Services
Ebben az esetben a futtatókörnyezet volna automatikus észlelése az adatvesztés és meghívni a `OnDataLossAsync` API.

A szolgáltatás szerzőjének kell helyreállítani a következőket hajthatja végre:

  - Bírálja felül a virtuális alaposztály metódust `OnDataLossAsync`.
  - A külső helyen, amely a szolgáltatás biztonsági másolatait tartalmazza a legújabb biztonsági mentés található.
  - Töltse le a legfrissebb biztonsági mentést (és a biztonsági mentés kibontani a biztonsági mentési mappába, ha azt tömörítették).
  - A `OnDataLossAsync` módszer lehetővé teszi egy `RestoreContext`. Hívja a `RestoreAsync` meg a megadott API `RestoreContext`.
  - Térjen vissza IGAZ, ha a visszaállítás sikeres.

Az alábbiakban látható egy példa megvalósítása a `OnDataLossAsync` módszert:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` az átadott a `RestoreContext.RestoreAsync` hívás nevű tagot tartalmaz `BackupFolderPath`.
Egy teljes biztonsági mentés, visszaállításakor ez `BackupFolderPath` kell állítani a helyi mappa elérési útját, amely tartalmazza a teljes biztonsági mentés.
Teljes és növekményes biztonsági mentések, számos visszaállításakor `BackupFolderPath` a mappa nem csak a teljes biztonsági mentés, de is összes növekményes biztonsági mentés a helyi elérési utat kell megadni.
`RestoreAsync` hívás segítségével throw `FabricMissingFullBackupException` Ha a `BackupFolderPath` megadott egy teljes biztonsági mentést tartalmaz.
Azt is segítségével throw `ArgumentException` Ha `BackupFolderPath` növekményes biztonsági mentések hibás lánca.
Ha tartalmazza a teljes biztonsági mentés, például az első növekményes és a harmadik növekményes biztonsági mentést, de nem a második növekményes biztonsági mentés.

> [!NOTE]
> Alapértelmezés szerint a RestorePolicy biztonságos van állítva.  Ez azt jelenti, hogy a `RestoreAsync` API ArgumentException az sikertelen lesz, ha azt észleli, hogy a biztonsági mentési mappája tartalmazza-e olyan állapotban, amely a régebbi, mint vagy egyenlő a replikában tárolt állapottal.  `RestorePolicy.Force` az biztonsági ellenőrzés kihagyására használható. Ennek részeként van megadva `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>A törölt vagy elveszett szolgáltatás
Ha a szolgáltatás el lett távolítva, meg kell először hozza létre a szolgáltatás előtt az adatok visszaállíthatók.  Fontos létrehozni a szolgáltatást az azonos konfigurációval, például particionálási sémát, hogy az adatok zökkenőmentesen állíthatók vissza.  Ha a szolgáltatás működik-e, az adatok helyreállítását API (`OnDataLossAsync` fent) meg kell hívni minden partícióján a szolgáltatás rendelkezik. Egyik módszere azt használatával `[FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)` minden partícióján.  

Ettől a ponttól kezdve megvalósítási megegyezik a fenti forgatókönyv. Mindegyik partíció kell legújabb vonatkozó biztonsági másolat visszaállítása a külső áruházban. Egy szerint, hogy a Partícióazonosító most megváltozhatott, mert a futtatókörnyezet partíciót hoz létre azonosítók dinamikusan. Így a szolgáltatás kell tárolni a megfelelő partíció információkat és a szolgáltatás nevét a helyes legutóbbi biztonsági másolatból történő visszaállítását minden partíció esetében.

> [!NOTE]
> Nem ajánlott használandó `FabricClient.ServiceManager.InvokeDataLossAsync` minden óta, előfordulhat, hogy sérült a fürt állapota, állítsa vissza a teljes szolgáltatás a partícióhoz.
> 

## <a name="replication-of-corrupt-application-data"></a>A sérült alkalmazás adatainak replikálása
Ha az újonnan telepített alkalmazás frissítése egy hiba, amely az adatok sérülését okozhatja. Az alkalmazásfrissítés például indítsa el minden phone bejegyzést a megbízható szótárban frissíteni érvénytelen területen kódot.  Ebben az esetben az érvénytelen telefonszámmal replikálja mivel a Service Fabric nem értesüljenek a tárolt adatok.

Az elsőként után ilyen egy súlyos hiba adatsérülést okozó észlelni, hogy a szolgáltatás az alkalmazás szintjén rögzítése, és ha lehetséges, frissítse az alkalmazás kódjának a hiba nem rendelkező verzióját.  Azonban a szolgáltatáskód hibáit megszüntetése után is lehetséges, hogy az adatok továbbra is megsérült, és így adatokat érdemes lehet visszaállítani.  Ilyen esetekben nem elegendő lehet a legutóbbi biztonsági mentése, visszaállítása, mivel lehetséges, hogy a legújabb biztonsági mentéseket is megsérült.  Így van, amely történt előtt az adatok hibásak kapott utolsó biztonsági mentés található.

Ha nem biztos benne, hogy mely biztonsági mentések sérült, képes egy új Service Fabric-fürt központi telepítése és a biztonsági másolatok érintett partíciók csakúgy, mint a fenti "Törölt vagy elveszett szolgáltatás" forgatókönyv.  Minden partíció esetében, indítsa el a biztonsági másolatok visszaállítása a legújabb pedig a legkevésbé. Miután megtalálta az olyan biztonsági mentésből nem rendelkezik a sérülés, áthelyezés vagy törlése az összes biztonsági mentés a partíció volt újabb (mint a biztonsági másolat). Ismételje meg az eljárást minden partíció esetében. Most, amikor `OnDataLossAsync` nevezzük az éles fürt a partíción az utolsó biztonsági mentés található a külső tárolóban kell figyelembe venni a fenti eljárás szerint.

A lépéseket, a "törölt vagy elveszett szolgáltatás" szakasz segítségével a szolgáltatás állapotát az állapot visszaállításához, mielőtt a buggy kód sérült állapotát.

Vegye figyelembe:

  - Történő visszaállításához, esély van, hogy a visszaállítandó biztonsági másolat régebbi, mint a partíció állapotban-e előtt az adatok elvesznek. Emiatt a lehető legtöbb adatainak helyreállítása utolsó lehetőségként csak kell visszaállítani.
  - Lehet, hogy a biztonsági mentési mappa elérési útját, és az elérési utak közé a biztonsági mentési mappában lévő fájlok reprezentáló attól függően, hogy a FabricDataRoot elérési útját és alkalmazástípus nevének hossza 255 karakternél. Emiatt néhány .NET módszerek, például `Directory.Move`, kell küldeni a `PathTooLongException` kivétel. Egy megoldás, hogy közvetlenül hívó kernel32 API-k, például a `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Biztonsági mentés és visszaállítás Reliable Actors


Megbízható szereplője keretrendszer Reliable Services épül. A ActorService, amely üzemelteti a actor(s) egy olyan állapot-nyilvántartó megbízható szolgáltatás. Emiatt minden a biztonsági mentési és visszaállítási funkció érhető el a Reliable Services érhető el a Reliable Actors (kivéve, amelyek adott állapotszolgáltató viselkedések). Mivel biztonsági mentések partíciónkénti alapon veszik, állapotai összes szereplő, hogy a partíció készül biztonsági másolat (visszaállítás hasonló, és partíciónkénti alapon történik). Végre biztonsági mentés/visszaállítás, a szolgáltatás tulajdonosa hozzon létre egy egyéni szereplő szolgáltatás osztályt ActorService osztályból származik, és tegye kell biztonsági mentés/visszaállítás hasonló Reliable Services korábbi szakaszokban a fent leírt módon.

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo)
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

Amikor létrehoz egy egyéni szereplő szolgáltatásosztály, is regisztrálnia, amely a szereplő regisztrálásakor kell.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Az alapértelmezett állapota szolgáltató a Reliable Actors `KvsActorStateProvider`. Alapértelmezés szerint nincs engedélyezve a növekményes biztonsági mentés `KvsActorStateProvider`. Engedélyezheti a növekményes biztonsági mentés létrehozásával `KvsActorStateProvider` saját konstruktoraikban a megfelelő beállítást, és majd átadja azt a ActorService konstruktor, ahogy az következő kódrészletet:

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

A növekményes biztonsági mentés engedélyezése után egy növekményes biztonsági másolat FabricMissingFullBackupException a következő okok valamelyike miatt meghiúsulhat, és szüksége lesz egy teljes biztonsági másolatok készítéséhez növekményes biztonsági mentését vagy mentéseit helyezése előtt:

  - A replika soha nem tartott egy teljes biztonsági mentés óta elsődleges vált.
  - A naplóbejegyzések némelyike csonkultak óta utolsó biztonsági mentés készült.

Ha engedélyezve van a növekményes biztonsági mentést, `KvsActorStateProvider` nem használja az körkörös puffer naplófájl rekordjainak kezelése és rendszeres időközönként csonkolja azt. Biztonsági mentés nélküli felhasználó 45 percig történik, ha a rendszer automatikusan levágja a napló rögzíti. Ez az időtartam alatt konfigurálható megadásával `logTrunctationIntervalInMinutes` a `KvsActorStateProvider` konstruktor (hasonló, ha engedélyezi a növekményes biztonsági mentés). A naplóbejegyzések is beolvasása csonkolva lesz, ha elsődleges replikának szüksége lesz egy másik replika létrehozásához úgy, hogy az összes adatot küld.

A biztonsági mentési láncolatát való visszaállítása során hasonló Reliable Services, a BackupFolderPath tartalmaznia kell az egyik alkönyvtár tartalmazó teljes biztonsági mentés, míg mások növekményes biztonsági mentését vagy mentéseit tartalmazó alkönyvtárakat alkönyvtárak. A visszaállítási API kivételhibát FabricException megfelelő hibaüzenet, ha a biztonsági mentési tanúsítványlánc érvényesítése sikertelen. 

> [!NOTE]
> `KvsActorStateProvider` jelenleg figyelmen kívül hagyja a beállítást RestorePolicy.Safe. Ez a szolgáltatás támogatása tervezett egy jövőbeli verzióban.
> 

## <a name="testing-backup-and-restore"></a>Tesztelési biztonsági mentése és visszaállítása
Fontos, hogy a kritikus fontosságú adatok biztonsági mentése van folyamatban, és visszaállíthatóak. Hogy ehhez a `Start-ServiceFabricPartitionDataLoss` , amely annak megállapítására, hogy az adatok biztonsági mentése és visszaállítása funkciót az a szolgáltatás a várt módon működik egy adott partíció adatvesztés helyeken PowerShell parancsmag.  Akkor is programozott módon adatvesztés meghívni, és adott eseménytől visszaállításához.

> [!NOTE]
> Keresse meg a biztonsági mentés minta végrehajtásának, és működőképes állapotba hozni a webalkalmazásban hivatkozás a Githubon. Tekintse meg a `Inventory.Service` szolgáltatás további részleteket.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>A technikai részletek alatt: további részleteket a biztonsági mentés és helyreállítás
Íme, néhány további részleteket a biztonsági mentését és helyreállítását.

### <a name="backup"></a>Backup
A megbízható állapotkezelője konzisztens biztonsági másolatok létrehozása nélkül blokkolja az összes olvasási és írási műveleteket biztosít. Ehhez használja a checkpoint és a naplófájlok mechanizmus.  Megbízható állapotkezelő teszi a terhelés a tranzakciós napló és a helyreállítási időkről javításához bizonyos időpontokban intelligens (egyszerűsített) ellenőrzőpontot vesz igénybe.  Amikor `BackupAsync` beszerzését, a megbízható állapotkezelője arra utasítja az összes megbízható objektumot a helyi biztonsági mentés mappájába másolhatja a legújabb ellenőrzőpontfájlok.  Ezt követően a megbízható állapotkezelője másolja át összes naplóbejegyzést kiindulva a "start"mutató legújabb naplórekord a biztonsági mentési mappába.  Akár a legújabb naplóbejegyzés naplórekordok vannak a biztonsági mentésben szereplő, és a megbízható állapotkezelője írási előre naplózási megőrzi, megbízható állapotkezelő garantálja a összes tranzakciók, amelyek véglegesített (`CommitAsync` sikeresen adott vissza ) a biztonsági mentésben szereplő.

Bármely tranzakció, amely véglegesíti után `BackupAsync` előfordulhat, hogy hívása történt, vagy nem a biztonsági mentésben.  Ha a helyi biztonsági mentési mappája feltöltődtek a Platform (Ez azt jelenti, hogy helyi biztonsági másolat elkészült a futtatókörnyezet), a szolgáltatás biztonsági mentési visszahívási meghívták.  A visszahívási felelős a biztonsági mentési mappa áthelyezése egy külső helyre, például az Azure Storage.

### <a name="restore"></a>Visszaállítás
A megbízható állapotkezelője lehetővé teszi a használatával állítsa vissza biztonsági másolatból a `RestoreAsync` API.  
A `RestoreAsync` metódusa `RestoreContext` csak belül hívható a `OnDataLossAsync` metódust.
A logikai által visszaadott `OnDataLossAsync` azt jelzi, hogy a szolgáltatás visszaállítva-e külső forrásból állapotában.
Ha a `OnDataLossAsync` igaz értéket ad vissza, a Service Fabric újra létrehozza az összes többi replikáit az elsődleges. A Service Fabric biztosítja, hogy fogadó replikák `OnDataLossAsync` hívható meg az elsődleges szerepkör első átmenet, de vannak nem rendelkeznek állapot írási vagy olvasási állapota.
Ez azt jelenti, hogy a StatefulService implementers `RunAsync` nem lesz meghívva, amíg `OnDataLossAsync` futtatása sikeresen befejeződött.
Ezt követően `OnDataLossAsync` az új elsődleges fogja meghívni.
Mindaddig, amíg a szolgáltatás teszi teljessé az API sikeresen (ad vissza IGAZ vagy hamis), és megfelelő újrakonfigurálását befejeződik, az API-t fog tartani meghívott egyenként.

`RestoreAsync` először elutasítja azokat a teljes meglévő állapota az elsődleges másodpéldány, amely azt hívták meg. Ezután a megbízható állapotkezelője hoz létre a megbízható objektumok, amelyek a biztonsági mentési mappában találhatók. Ezt követően a megbízható objektumok utasította visszaállítása az ellenőrzőpontok a biztonsági mentési mappában. Végül a megbízható állapotkezelője saját állapot helyreállítja a biztonsági mentési mappája napló rekordjai, és helyreállítást hajt végre. A helyreállítási folyamat részeként a "kiindulási pontja"-től kezdődő műveletek, a biztonsági mentési mappája véglegesítési naplórekordokat rendelkező megbízható objektumokhoz rendszer játssza. Ez a lépés biztosítja, hogy a helyreállított állapot konzisztens.

## <a name="next-steps"></a>További lépések
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
  - [Megbízható szolgáltatások értesítések](service-fabric-reliable-services-notifications.md)
  - [Megbízható konfigurálása](service-fabric-reliable-services-configuration.md)
  - [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

