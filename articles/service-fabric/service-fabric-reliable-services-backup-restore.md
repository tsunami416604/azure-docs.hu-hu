---
title: Service Fabric biztonsági mentése és visszaállítása |} A Microsoft Docs
description: A Service Fabric biztonsági mentési és visszaállítási fogalmi dokumentációja
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
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: d01d2f18ed35d1752f97f405ae7f7bfb4708ca0d
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570045"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Biztonsági mentését és visszaállítását a Reliable Services és Reliable Actors
Az Azure Service Fabric egy magas rendelkezésre állású platform, amely az állapot replikál a magas rendelkezésre állás fenntartása érdekében több csomópontra.  Ily módon még akkor is, ha a fürtben egy csomópont meghibásodik, a szolgáltatások továbbra is elérhető. Bár ez a platform által biztosított beépített redundancia néhány elegendő, bizonyos esetekben célszerű a szolgáltatás számára az adatok biztonsági másolatát (egy külső tároló).

> [!NOTE]
> Kritikus fontosságú biztonsági mentését és visszaállítását (és tesztelése a várt módon működik), a helyreállítható adatok elvesztése esetén.
> 

> [!NOTE]
> A Microsoft azt javasolja, használandó [rendszeres biztonsági mentés és visszaállítás](service-fabric-backuprestoreservice-quickstart-azurecluster.md) Reliable Stateful services és Reliable actors – adatok biztonsági másolatának konfigurálásához. 
> 


Például egy szolgáltatást érdemes adatok biztonsági mentéséhez a következő esetekben a védelme érdekében:

- Ha egy egész Service Fabric-fürt állandó elvesztését.
- A szolgáltatás partíció replikák többségi végleges adatvesztést
- Felügyeleti hibák, amellyel az állapot véletlenül lekérdezi törölték vagy sérült állapotba kerül. Például ennek oka lehet egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- Hibák a szolgáltatásban, amely adatsérülést okozhat. Ez például akkor fordulhat elő, a kód frissítése az írás a hibás egy megbízható gyűjteményben indításakor. Ebben az esetben a kódot és az adatok is előfordulhat alakítható vissza egy korábbi állapotba.
- Offline adatok feldolgozása. Érdemes lehet kényelmes szeretné, hogy a kapcsolat nélküli adatfeldolgozást az üzleti intelligenciát, amely a szolgáltatás, amely létrehozza az adatok külön-külön történik.

A biztonsági mentési és visszaállítási funkció lehetővé teszi, hogy a szolgáltatások hozhat létre és biztonsági másolatokat állíthatja vissza a Reliable Services API épül. A platform által biztosított biztonsági mentési API-k lehetővé teszik a biztonsági másolat szolgáltatás partíció állapot nélküli blokkoló olvasási vagy írási műveleteket. A visszaállítási API-k engedélyezése a szolgáltatás partíció állapot vissza kell állítani a kiválasztott biztonsági másolatból.

## <a name="types-of-backup"></a>Biztonsági mentés típusai
Kétféle biztonsági mentés: Teljes és növekményes.
Egy teljes biztonsági mentés egy biztonsági mentés állapota a replika újbóli létrehozásához szükséges összes adatot tartalmazó: ellenőrzőpontokat, és minden napló rögzíti.
Mivel az ellenőrzőpontokat, és a napló rendelkezik, egy teljes biztonsági mentés önmagában állíthatók vissza.

A teljes biztonsági mentések probléma merült fel merül fel, amikor nagyok az ellenőrzőpontokat.
Például egy replikát, amelynek állapota 16 GB lesz, amely hozzá ellenőrzőpontokat körülbelül legfeljebb 16 GB.
Ha a helyreállítási időkorlátot öt perc alatt van, a replika kell öt percenként kell készíteni.
Minden alkalommal, amikor biztonsági mentést készít kell másolni az ellenőrzőpontok mellett 50 MB-ot 16 GB (konfigurálható használatával `CheckpointThresholdInMB`) adatot dolgoztak fel naplók.

![A példában teljes biztonsági mentés.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A megoldás erre a problémára, a növekményes biztonsági mentések, ahol biztonsági mentése csak a módosított naplófájl rekordokat tartalmazza a legutóbbi biztonsági mentés óta.

![A növekményes biztonsági mentési példa.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Mivel a növekményes biztonsági mentések csak a változásokat a legutóbbi biztonsági mentés (nem tartalmazza az ellenőrzőpontok) óta, általában gyorsabb, de azok nem állítható vissza, a saját.
A növekményes biztonsági mentés visszaállításához szükség a teljes biztonsági mentési láncolatát.
A biztonsági mentési láncolatát egy láncot, a biztonsági mentések teljes biztonsági mentés kezdve, és egymást követő növekményes biztonsági mentések egy szám követ.

## <a name="backup-reliable-services"></a>A Reliable Services biztonsági mentési
A szolgáltatás Szerző el, hogy a biztonsági mentések és a biztonsági mentések tárolására szolgáló teljes körű vezérléssel rendelkezik.

A biztonsági mentést elindítani a szolgáltatást kell az örökölt tag függvény hívása `BackupAsync`.  
Biztonsági másolatok lehet tenni, csak az elsődleges replikára, és írási állapot nyújtott van szükségük.

Az alábbi módon `BackupAsync` vesz egy `BackupDescription` objektumot, ahol egy megadható egy teljes és növekményes biztonsági mentést, valamint egy visszahívási függvény, `Func<< BackupInfo, CancellationToken, Task<bool>>>` , amelyek akkor aktiválódnak, ha a biztonsági mentési mappa helyileg létrehozott, és készen áll a egyes helyezhető külső tárterület.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Egy növekményes biztonsági mentés irányuló kérelem sikertelen lehet a `FabricMissingFullBackupException`. Ez a kivétel azt jelzi, hogy a következő dolog történik:

- a replika soha nem vette egy teljes biztonsági mentés óta vált, elsődleges,
- a naplórekordok, mivel a rendszer csonkolta a legutóbbi biztonsági mentés némelyike vagy
- az átadott replika a `MaxAccumulatedBackupLogSizeInMB` korlátot.

Felhasználók növelheti annak esélyét, hogy hajtsa végre a növekményes biztonsági mentések konfigurálásával `MinLogSizeInMB` vagy `TruncationThresholdFactor`.
Ezek növekvő értékei növeli a lemezhasználat replika száma.
További információkért lásd: [Reliable Services-konfiguráció](service-fabric-reliable-services-configuration.md)

`BackupInfo` a biztonsági mentés, például a mappát, ahol a futtatókörnyezet a biztonsági másolat helye kapcsolatos információkat biztosít (`BackupInfo.Directory`). A visszahívási függvény áthelyezheti a `BackupInfo.Directory` egy külső tároló vagy egy másik helyre.  Ez a funkció is, amely jelzi, hogy volt képes sikeresen célhelye helyezze át a biztonsági mentési mappát egy logikai adja vissza.

A következő kód bemutatja, hogyan a `BackupCallbackAsync` módszer használható a biztonsági mentés feltölteni az Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Az előző példában `ExternalBackupStore` a minta osztály, amely a használható felület az Azure Blob storage, és `UploadBackupFolderAsync` a metódus tömöríti a mappát, és elhelyezi azt az Azure Blob-tároló.

Vegye figyelembe:

  - Lehetnek csak egy biztonsági mentési művelet replikánként átvitel közben az adott időpontban. Egynél több `BackupAsync` egyszerre a hívás kivételt fogja kijelezni `FabricBackupInProgressException` megszakít biztonsági mentések egyik korlátozása.
  - Ha egy replika feladatátadást hajt végre, amíg folyamatban van egy biztonsági mentés, a biztonsági mentés lehetséges, hogy nem fejeződtek be. Így, miután a feladatátvétel befejezését követően feladata a szolgáltatás a biztonsági mentés újraindítása meghívásával `BackupAsync` szükség szerint.

## <a name="restore-reliable-services"></a>A Reliable Services-visszaállítás
Általában az esetekben, amikor szüksége lehet, hogy a visszaállítási művelet végrehajtásához az alábbi kategóriák valamelyikébe tartoznak:

  - A szolgáltatás az elveszett adatok particionálásához. Például a lemez ki, a három replikák egy partícióhoz (többek között az elsődleges replika) lekérdezi sérült, vagy tartalmának végleges törléséig. Adatok visszaállítása biztonsági másolatból az új elsődleges szükségessé.
  - A teljes szolgáltatás megszakad. Például a rendszergazda törli a teljes szolgáltatás, és így a szolgáltatás, és az adatok kell állítani.
  - A szolgáltatás replikált sérült alkalmazás adatokat (például egy alkalmazás hiba) miatt. Ebben az esetben a szolgáltatás később frissített vagy állítja vissza a rendszer eltávolítja a meghibásodás okát, pedig nem sérült adatokat vissza kell állítani.

Számos különböző módszer lehetséges, amelyek használatával kapcsolatos példákat biztosítunk `RestoreAsync` a fenti helyzetek helyreállíthatók.

## <a name="partition-data-loss-in-reliable-services"></a>A Reliable Services partíció adatvesztés
Ebben az esetben a futtatókörnyezet szeretné automatikusan észleli az adatvesztés és meghívása a `OnDataLossAsync` API-t.

A szolgáltatás Szerző kell végeznie a következő helyre:

  - Bírálja felül a virtuális alaposztály metódusának `OnDataLossAsync`.
  - A legújabb biztonsági mentés található a külső helyre, amely tartalmazza a szolgáltatás biztonsági mentéseket.
  - Töltse le a legújabb biztonsági mentés (és bontsa ki a biztonsági mentés a biztonsági mentési mappába, ha azt tömörítették).
  - A `OnDataLossAsync` módszert biztosít a `RestoreContext`. Hívja a `RestoreAsync` API a megadott `RestoreContext`.
  - Igaz értéket ad vissza, ha a visszaállítás sikeres.

Az alábbiakban egy példa megvalósítását a a `OnDataLossAsync` módszer:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` az átadott a `RestoreContext.RestoreAsync` hívást tartalmaz a tag `BackupFolderPath`.
Egy egyszeri teljes biztonsági mentés visszaállítása során ez `BackupFolderPath` kell állítani a helyi mappa elérési útját, amely tartalmazza a teljes biztonsági mentés.
Egy teljes biztonsági mentés és a egy növekményes biztonsági másolatok száma visszaállításához `BackupFolderPath` kell állítani a helyi mappa elérési útját, amely csak nem tartalmazza a teljes biztonsági mentés, de is összes növekményes biztonsági mentés.
`RestoreAsync` hívás nagyvállalat `FabricMissingFullBackupException` Ha a `BackupFolderPath` megadott egy teljes biztonsági mentés nem tartalmazza.
Azt is nagyvállalat `ArgumentException` Ha `BackupFolderPath` növekményes biztonsági mentések hibás lánca.
Például, ha a teljes biztonsági mentést tartalmaz az első növekményes és a harmadik növekményes biztonsági mentés, de nem a második növekményes biztonsági mentés.

> [!NOTE]
> Alapértelmezés szerint a RestorePolicy biztonságos van beállítva.  Ez azt jelenti, hogy a `RestoreAsync` API-ArgumentException sikertelen lesz, ha azt észleli, hogy a biztonsági mentési mappát tartalmaz olyan állapotban, amely régebbi, mint vagy egyenlő, mint az állapot, a replika található.  `RestorePolicy.Force` a biztonsági ellenőrzés kihagyására használható. Ennek részeként megadott `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>A törölt vagy elveszett szolgáltatás
Ha eltávolít egy szolgáltatás, kell először újra a szolgáltatás létrehozása előtt az adatok visszaállíthatók.  Fontos, ugyanazt a konfigurációt, például a particionálási sémát, hogy az adatokat zökkenőmentesen visszaállíthatók szolgáltatás létrehozásához.  A szolgáltatás működik, az API-t az adat-visszaállítás után (`OnDataLossAsync` fent) meg kell hívni a szolgáltatás minden egyes partíción van. Egyik módszere e használatával [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) minden partícióján.  

Ettől kezdve a megvalósítás ugyanaz, mint a fenti forgatókönyv. Mindegyik partíció kell a legújabb fontos biztonsági másolat visszaállítása a külső tárolóból. Egyik kikötés az, hogy a Partícióazonosító előfordulhat, hogy most már megváltoztak, mivel a runtime partíciót hoz létre azonosítók dinamikusan. Így a szolgáltatás kell tárolnia a megfelelő partíció információ és a szolgáltatás neve helyes legutóbbi biztonsági másolatból történő visszaállítását az egyes partíciók azonosítására.

> [!NOTE]
> Nem javasoljuk, hogy használjon `FabricClient.ServiceManager.InvokeDataLossAsync` az egyes partíciókon az egész szolgáltatás óta, előfordulhat, hogy sérült a fürt állapota visszaállítására.
> 

## <a name="replication-of-corrupt-application-data"></a>Sérült alkalmazás-adatok replikálása
Ha az újonnan üzembe helyezett alkalmazás frissítése egy hiba, amely az adatok sérülését okozhatja. Alkalmazásfrissítések például indítson el egy megbízható szótárban tároló minden telefon száma rekord frissítése egy érvénytelen körzetszámmal együtt.  Ebben az esetben az érvénytelen telefonszámmal replikálja, mivel a Service Fabric még nem ismeri, hogy az adatokat tárol.

Az első teendő után, például egy súlyos hiba adatsérülést okozó észleli, hogy a szolgáltatást az alkalmazás szintjén rögzítése, és ha lehetséges, frissítse a verzióra, az alkalmazás-szabályzat, amely nem rendelkezik a bejelentett hiba.  Azonban akkor is, a webszolgáltatás kódjához rögzített, az adatok továbbra is lehetséges sérült, és így előfordulhat, hogy kell az adatokat vissza kell állítani.  Ezekben az esetekben nem elegendő lehet visszaállítani a legújabb biztonsági mentés óta a legújabb biztonsági mentések is valószínűleg sérült.  Így hogy keresse meg a legutóbbi biztonsági mentés előtt az adatok sérültek van végrehajtott.

Ha nem biztos abban, hogy melyik biztonsági mentések sérültek, sikerült új Service Fabric-fürt üzembe helyezése és az érintett partíciók csakúgy, mint a fenti biztonsági mentések visszaállítása "Törölt vagy elveszett szolgáltatás" forgatókönyv.  Minden egyes partíción elindítani a biztonsági mentésekből a legutóbbi, a legkevésbé. Miután megtalálta a sérülés nem rendelkező biztonsági másolat, áthelyezés/törlése minden biztonsági mentés a partíció, amely újabb (mint a biztonsági mentés) is. Ismételje meg ezt a folyamatot mindegyik partíció. Most, hogy amikor `OnDataLossAsync` nevezzük az éles fürtöt a partíción a legutóbbi biztonsági mentés található a külső tárban kell figyelembe venni a fenti eljárás szerint.

A lépéseket, a "törölt vagy elveszett szolgáltatás" szakasz a szolgáltatás állapotát a állapotának visszaállításához, mielőtt a buggy kód sérült állapota használható.

Vegye figyelembe:

  - Visszaállításkor, esetén megvan az esélye, hogy a visszaállítandó biztonsági másolat régebbi, mint a partíció állapota-e előtt az adatok elvesznek. Ez az oka állítsa vissza a lehető legtöbb adatot helyreállítása végső megoldásként csak.
  - Lehet, hogy a karakterlánc, amely a biztonsági mentési mappa elérési útjának és az olyan fájlok elérési útjait a biztonsági mentési mappába függően FabricDataRoot elérési útjának és az alkalmazástípus nevének hossza 255 karakternél. Egy .NET-metódusokat, ez okozhat, például `Directory.Move`állíthatunk munkába, a `PathTooLongException` kivétel. Egy megkerülő megoldás az, hogy közvetlenül hívó kernel32 API-k, például `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Biztonsági mentése és visszaállítása a Reliable Actors


A Reliable Actors-keretrendszer Reliable Services épül. A ActorService, amely a actor(s) egy állapotalapú megbízható szolgáltatást. Ezért a biztonsági mentési és helyreállítási funkciókat a Reliable Services elérhető is rendelkezésre áll a Reliable Actors (kivéve, amelyek adott állapotszolgáltató viselkedések). Mivel a biztonsági másolatokat partíciónkénti alapon, állapotai összes szereplő, hogy a partíció készül biztonsági másolat (visszaállítás hasonló, és partíciónkénti alapon történik). Biztonsági mentési és visszaállítási végre, a szolgáltatás tulajdonosa hozzon létre egy egyéni aktor service osztályt, amely ActorService osztályból származik kell, és tegye hasonló az előző szakaszokban fentieknek megfelelően a Reliable Services biztonsági mentési és visszaállítási.

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

Amikor létrehoz egy egyéni aktor service osztályt, szeretne regisztrálni, amely, valamint az aktor regisztrálásakor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Az alapértelmezett állapotba szolgáltató a Reliable actors `KvsActorStateProvider`. Alapértelmezés szerint nincs engedélyezve a növekményes biztonsági mentés `KvsActorStateProvider`. Engedélyezheti a növekményes biztonsági mentés létrehozásával `KvsActorStateProvider` a megfelelő beállítás a konstruktorban és átadásával ActorService konstruktor az alábbi kódrészletben látható módon:

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

A növekményes biztonsági mentés engedélyezése után a növekményes biztonsági mentés FabricMissingFullBackupException az alábbi okok egyike sikertelen lehet, és egy teljes biztonsági mentési igénybe véve a növekményes biztonsági másolat előtt kell:

  - A replika elsődleges vált, mert soha nem tartott egy teljes biztonsági mentés.
  - A naplórekordok némelyike csonkolt óta a legutóbbi biztonsági mentés készült.

Ha engedélyezve van a növekményes biztonsági mentést, `KvsActorStateProvider` a napló-rekordok kezelése nem használ. kör alakú puffer, és rendszeres időközönként csonkolja azt. Ha nincs biztonsági mentés készül a felhasználó egy időszak 45 perc, a rendszer automatikusan levágja a rekordok naplózása. Ez az időtartam alatt megadásával konfigurálható `logTruncationIntervalInMinutes` a `KvsActorStateProvider` konstruktor (hasonlóan a növekményes biztonsági mentés engedélyezése). Ha az elsődleges replikának továbbítania kell egy másik replikára készítését minden adat küldése a rekordok naplózása is első csonkolva.

Egy biztonsági mentési láncolatát való visszaállítása során, a Reliable Services hasonló a BackupFolderPath tartalmaznia kell a teljes biztonsági mentés és mások tartalmazó alkönyvtárakat, amely tartalmazza a növekményes biztonsági másolat egy alkönyvtár alkönyvtárak. A visszaállítási API FabricException váltja a megfelelő hibaüzenet jelenik meg, ha a biztonsági mentési láncolatát érvényesítése sikertelen. 

> [!NOTE]
> `KvsActorStateProvider` jelenleg figyelmen kívül hagyja a beállítást RestorePolicy.Safe. Ez a funkció támogatása tervezünk egy soron következő kiadásban.
> 

## <a name="testing-back-up-and-restore"></a>Tesztelés biztonsági mentése és visszaállítása
Fontos győződjön meg arról, hogy a kritikus fontosságú adatokat készül biztonsági másolat, és a vissza tudja állítani. Ezt megteheti meghívásával a `Start-ServiceFabricPartitionDataLoss` parancsmagot a PowerShellben, amely egy adott partíció annak megállapítására, hogy az adatok biztonsági mentése és visszaállítása funkciók esetében a szolgáltatás elvárt működésének adatvesztés lehet szükség.  Egyben lehetővé teszi a programozott módon adatvesztés előidézése és visszaállítása, valamint a kapcsolódó eseményre.

> [!NOTE]
> Keresse meg a biztonsági mentés egy minta megvalósítását, és a GitHub webes hivatkozás alkalmazásban funkciójának helyreállításához. Tekintse meg a `Inventory.Service` szolgáltatás további részletekért.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Technikai részletek: további részleteket a biztonsági mentés és visszaállítás
Íme néhány további részleteket a biztonsági mentés és visszaállítás.

### <a name="backup"></a>Backup
A Reliable State Manager lehetővé teszi az alkalmazáskonzisztens biztonsági másolatok létrehozása hogy nem minden olvasási vagy írási műveleteket. Ehhez használja egy ellenőrzőpont- és naplófájlok mechanizmus.  A Reliable State Manager intelligens (egyszerűsített) ellenőrzőpontok bizonyos időpontokban, a tranzakciós napló kiürítése informatikusokra és javíthatják a visszaállítási időt vesz igénybe.  Amikor `BackupAsync` nevezzük, a Reliable State Manager az összes megbízható objektumok arra utasítja a legújabb ellenőrzőpont-fájlok másolása egy helyi biztonsági mentési mappába.  Ezt követően a Reliable State Manager másolja át az összes naplófájl rögzíti, kezdve a "kezdő"mutatót a legújabb naplórekord a biztonsági mentési mappába.  Legfeljebb legújabb naplórekord naplórekordok szerepelnek a biztonsági mentés és a Reliable State Manager megőrzi az írási előre naplózást, mivel a Reliable State Manager garantálja a összes tranzakciók, amelyek véglegesített (`CommitAsync` sikeresen adott vissza ) a biztonsági mentés szerepelnek.

Bármely tranzakció, amely után `BackupAsync` május lett meghívva, vagy nem lehet a biztonsági mentéshez.  Ha a helyi biztonsági mentési mappa feltöltődtek a Platform (vagyis helyi biztonsági másolat befejeződött futásidőben), a szolgáltatás biztonsági mentési visszahívási meghívásainak.  A visszahívási felelős a biztonsági mentési mappa áthelyezése – például az Azure Storage külső helyre.

### <a name="restore"></a>Visszaállítás
A Reliable State Manager lehetővé teszi a használatával állítsa vissza biztonsági másolatból a `RestoreAsync` API-t.  
A `RestoreAsync` metódust `RestoreContext` csak belül nem hívható a `OnDataLossAsync` metódust.
A logikai által visszaadott `OnDataLossAsync` azt jelzi, hogy a szolgáltatás állapotában visszaállítása egy külső forrásból.
Ha a `OnDataLossAsync` igaz értéket ad vissza, a Service Fabric újra létrehozza az összes többi az elsődleges replika. A Service Fabric biztosítja, hogy replikákat, amelyek megkapják `OnDataLossAsync` elsődleges szerepkörre váltson első hívás, de állapota nem biztosított olvassa vagy állapotának írása.
Ez azt jelenti, hogy a statefulservice-ből szolgáltatást bevezetők részére, `RunAsync` nem lesz meghívva, amíg `OnDataLossAsync` sikeresen befejeződik.
Ezt követően `OnDataLossAsync` az új elsődleges akkor kell meghívni.
Mindaddig, amíg a szolgáltatás fejezheti be ez az API sikeresen (IGAZ vagy hamis értéket visszaadó), és végül a releváns újrakonfigurálása, az API-t fog tartani hívott egyenként.

`RestoreAsync` először az elsődleges replika, a hívott teljes meglévő állapota miatt növekedhet. A Reliable State Manager ezután a megbízható objektumok, amelyek szerepelnek a biztonsági mentési mappát hoz létre. Ezt követően a megbízható objektumok visszaállítása az ellenőrzőpontok a biztonsági mentési mappában vannak utasította. Végül a Reliable State Manager állítja helyre a saját állapota a biztonsági mentési mappa log rekordjai és helyreállítást hajt végre. A helyreállítási folyamat részeként a sértették meg a biztonsági mentési mappa rekordok naplózása "kiindulási pontot" kezdve műveletek megbízható objektumokhoz játssza vissza. Ez a lépés biztosítja, hogy a helyreállított állapot nem konzisztens.

## <a name="next-steps"></a>További lépések
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [A Reliable Services a rövid útmutató](service-fabric-reliable-services-quick-start.md)
  - [A Reliable Services-értesítések](service-fabric-reliable-services-notifications.md)
  - [A Reliable Services-konfiguráció](service-fabric-reliable-services-configuration.md)
  - [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

