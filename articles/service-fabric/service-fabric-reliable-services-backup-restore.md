---
title: Biztonsági mentés és visszaállítás Service Fabric
description: Elméleti dokumentáció Service Fabric biztonsági mentéshez és visszaállításhoz, a megbízható állapot-nyilvántartó szolgáltatások és Reliable Actors biztonsági mentésének konfigurálására szolgáló szolgáltatás.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922774"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Reliable Services és Reliable Actors biztonsági mentése és visszaállítása
Az Azure Service Fabric egy magas rendelkezésre állású platform, amely a magas rendelkezésre állás fenntartása érdekében replikálja az állapotot több csomópont között.  Így még akkor is, ha a fürt egyik csomópontja meghibásodik, a szolgáltatások továbbra is elérhetők maradnak. Habár a platform által biztosított beépített redundancia elegendő lehet bizonyos esetekben, bizonyos esetekben kívánatos, hogy a szolgáltatás biztonsági másolatot készíteni (külső tárolóba).

> [!NOTE]
> Kritikus fontosságú az adatok biztonsági mentése és visszaállítása (és az elvárt módon történő ellenőrzés), hogy az adatvesztési forgatókönyvek alapján helyreállítható legyen.
> 

> [!NOTE]
> A Microsoft azt javasolja, hogy [rendszeres biztonsági mentést és visszaállítást](service-fabric-backuprestoreservice-quickstart-azurecluster.md) használjon a megbízható állapot-nyilvántartó szolgáltatások és Reliable Actors biztonsági mentésének konfigurálásához. 
> 


Előfordulhat például, hogy egy szolgáltatás biztonsági másolatot szeretne készíteni az adatokról, hogy megvédje a következő helyzetekben:

- Egy teljes Service Fabric-fürt végleges elvesztése esetén.
- Egy szolgáltatás-partíció replikái többségének végleges elvesztése
- Rendszergazdai hibák, amelyek miatt az állapot véletlenül törölve vagy sérült. Ez például akkor fordulhat elő, ha egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- A szolgáltatásban az adatsérülést okozó hibák. Ez például akkor fordulhat elő, ha egy szolgáltatási kód frissítése megkezdi a hibás adatgyűjtést egy megbízható gyűjteménybe. Ilyen esetben a kódnak és az adatfájlnak is korábbi állapotra kell visszaállítania.
- Offline adatfeldolgozás. Előfordulhat, hogy az üzleti intelligenciához tartozó adatok offline feldolgozását az adatok előállítására szolgáló szolgáltatástól függetlenül kell megtenni.

A Backup/Restore funkció lehetővé teszi, hogy a Reliable Services API-ra épülő szolgáltatások biztonsági másolatokat hozzanak létre és állítsanak vissza. A platform által biztosított biztonsági mentési API-k lehetővé teszik a szolgáltatás-partíció állapotának biztonsági mentését az olvasási vagy írási műveletek blokkolása nélkül. A visszaállítási API-k lehetővé teszik a szolgáltatás partíciójának állapotát a kiválasztott biztonsági másolatból való visszaállításra.

## <a name="types-of-backup"></a>A biztonsági mentés típusai
Két biztonsági mentési lehetőség létezik: teljes és növekményes.
A teljes biztonsági mentés egy olyan biztonsági másolat, amely tartalmazza a replika állapotának újbóli létrehozásához szükséges összes adatát: ellenőrzőpontok és az összes naplózási rekord.
Mivel az ellenőrzőpontokkal és a naplóval rendelkezik, a teljes biztonsági mentést saját maga állíthatja vissza.

A teljes biztonsági mentéssel kapcsolatos probléma akkor fordul elő, ha az ellenőrzőpontok nagy méretűek.
Egy 16 GB-nyi állapotú replika például ellenőrzőpontokkal fog rendelkezni, amelyek körülbelül 16 GB-ot vesznek fel.
Ha öt perces helyreállítási pontra vonatkozó célkitűzéssel rendelkezik, a replikát öt percenként kell biztonsági másolatot készíteni.
Minden alkalommal, amikor biztonsági mentést készít, 16 GB-os ellenőrzőpontot másol a 50 MB (`CheckpointThresholdInMB`) értékű naplók használatával.

![Teljes biztonsági mentési példa.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A probléma megoldása a növekményes biztonsági mentések, ahol a biztonsági mentés csak a legutóbbi biztonsági mentés óta a módosított naplókat tartalmazza.

![Növekményes biztonsági mentés – példa.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Mivel a növekményes biztonsági mentések csak a legutóbbi biztonsági mentés óta változnak (nem tartalmazza az ellenőrzőpontokat), általában gyorsabbak, de nem állíthatók vissza a helyükön.
A növekményes biztonsági mentés visszaállításához a teljes biztonsági mentési láncra van szükség.
A biztonsági mentési lánc egy teljes biztonsági mentéssel kezdődő biztonsági másolatok lánca, amelyet számos összefüggő növekményes biztonsági mentés követ.

## <a name="backup-reliable-services"></a>Biztonsági másolat Reliable Services
A szolgáltatás szerzője teljes körű irányítást biztosít a biztonsági másolatok készítéséhez és a biztonsági mentések tárolásához.

A biztonsági mentés elindításához a szolgáltatásnak meg kell hívnia az örökölt tag függvényt `BackupAsync`.  
A biztonsági mentések csak az elsődleges replikából készíthetők, és az írási állapot megadása kötelező.

Ahogy az alábbi ábrán is látható, `BackupAsync` egy `BackupDescription` objektumba kerül, ahol megadhat egy teljes vagy növekményes biztonsági mentést, valamint egy visszahívási függvényt, `Func<< BackupInfo, CancellationToken, Task<bool>>>`, amely akkor jelenik meg, ha a biztonsági mentési mappát helyileg hozták létre, és készen áll arra, hogy egy külső tárhelyre lehessen áthelyezni.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

A növekményes biztonsági mentés elvégzésére irányuló kérelem sikertelen lehet a `FabricMissingFullBackupException`. Ez a kivétel azt jelzi, hogy a következők egyike történik:

- a replika soha nem készített teljes biztonsági mentést, mert az elsődleges,
- néhány naplózási rekord a legutóbbi biztonsági mentés óta csonkolt vagy
- a replika átadta a `MaxAccumulatedBackupLogSizeInMB` korlátot.

A felhasználók a `MinLogSizeInMB` vagy `TruncationThresholdFactor`konfigurálásával növelhetik a növekményes biztonsági mentések lehetséges valószínűségét.
Ezeknek az értékeknek a növelése növeli a replikák lemezterület-használatát.
További információ: [Reliable Services konfiguráció](service-fabric-reliable-services-configuration.md)

`BackupInfo` a biztonsági mentéssel kapcsolatos információkat tartalmaz, beleértve annak a mappának a helyét, amelyben a futtatókörnyezet mentette a biztonsági másolatot (`BackupInfo.Directory`). A visszahívási függvény áthelyezheti a `BackupInfo.Directory`t egy külső tárolóba vagy egy másik helyre.  Ez a függvény egy logikai értéket is ad vissza, amely azt jelzi, hogy sikerült-e sikeresen áthelyezni a biztonsági mentési mappát a célhelyre.

A következő kód bemutatja, hogyan használható a `BackupCallbackAsync` módszer a biztonsági mentés Azure Storage-ba történő feltöltésére:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Az előző példában `ExternalBackupStore` az Azure Blob Storage-hoz való kapcsolódáshoz használt minta osztály, és `UploadBackupFolderAsync` az a módszer, amely tömöríti a mappát, és elhelyezi az Azure Blob-tárolóban.

Vegye figyelembe:

  - Egy adott időpontban csak egy biztonsági mentési művelet helyezhető el a repüléshez. Egyszerre több `BackupAsync` hívása is `FabricBackupInProgressException` a fedélzeti biztonsági mentések egyra való korlátozásához.
  - Ha egy replika feladatátvétel közben egy biztonsági mentés folyamatban van, előfordulhat, hogy a biztonsági mentés nem fejeződött be. Így a feladatátvétel befejeződése után a szolgáltatás feladata a biztonsági mentés újraindítása a `BackupAsync` szükség szerinti meghívásával.

## <a name="restore-reliable-services"></a>Reliable Services visszaállítása
Általánosságban elmondható, hogy a visszaállítási művelet elvégzéséhez a következő kategóriák valamelyikébe tartozik:

  - A szolgáltatás partíciója elvesztette az adatvesztést. Például a partíciók három másodpéldánya (beleértve az elsődleges replikát is) esetében a lemez megsérül vagy törölve lesz. Előfordulhat, hogy az új elsődleges biztonsági másolatból kell visszaállítani az adatait.
  - A teljes szolgáltatás elvész. A rendszergazda például eltávolítja a teljes szolgáltatást, és így a szolgáltatást és az adatkészletet is vissza kell állítani.
  - A szolgáltatás replikálta a sérült alkalmazásadatok (például egy alkalmazás hibája miatt). Ebben az esetben a szolgáltatást frissíteni kell, vagy vissza kell állítania, hogy eltávolítsa a sérülés okát, és a nem sérült adatmennyiséget vissza kell állítani.

Habár számos módszer lehetséges, néhány példát is kínálunk a `RestoreAsync` használatára a fenti forgatókönyvek helyreállításához.

## <a name="partition-data-loss-in-reliable-services"></a>Adatvesztés Reliable Services
Ebben az esetben a futtatókörnyezet automatikusan felismeri az adatvesztést, és meghívja a `OnDataLossAsync` API-t.

A szolgáltatás szerzőjének a következőket kell elvégeznie a helyreállításhoz:

  - Bírálja felül a virtuális alaposztály metódusát `OnDataLossAsync`.
  - Keresse meg a legújabb biztonsági mentést a szolgáltatás biztonsági másolatait tartalmazó külső helyen.
  - Töltse le a legújabb biztonsági mentést (és bontsa ki a biztonsági mentést a biztonsági mentési mappába, ha tömörítve volt).
  - A `OnDataLossAsync` metódus `RestoreContext`biztosít. Hívja meg a `RestoreAsync` API-t a megadott `RestoreContext`.
  - Igaz értéket ad vissza, ha a visszaállítás sikeres volt.

A következő példa a `OnDataLossAsync` módszer megvalósítását szemlélteti:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

a `RestoreContext.RestoreAsync` hívásba átadott `RestoreDescription` egy `BackupFolderPath`nevű tagot tartalmaz.
Egyetlen teljes biztonsági mentés visszaállításakor ezt a `BackupFolderPath` a teljes biztonsági mentést tartalmazó mappa helyi elérési útjára kell beállítani.
A teljes biztonsági mentés és a növekményes biztonsági másolatok számának visszaállításakor `BackupFolderPath`t a mappa helyi elérési útjára kell beállítani, amely nem csak a teljes biztonsági mentést tartalmazza, hanem az összes növekményes biztonsági mentést is.
Ha a megadott `BackupFolderPath` nem tartalmaz teljes biztonsági mentést, akkor a `RestoreAsync` hívása `FabricMissingFullBackupException` dobni.
Azt is eldöntheti, `ArgumentException`, ha `BackupFolderPath` a növekményes biztonsági mentések hibás láncával rendelkezik.
Ha például tartalmazza a teljes biztonsági mentést, az első növekményes és a harmadik növekményes biztonsági mentést, de a második növekményes biztonsági mentést.

> [!NOTE]
> A RestorePolicy alapértelmezés szerint biztonságos értékre van állítva.  Ez azt jelenti, hogy a `RestoreAsync` API a ArgumentException sikertelen lesz, ha azt észleli, hogy a biztonsági mentési mappa olyan állapotot tartalmaz, amely régebbi vagy egyenlő, mint a replikában található állapot.  ezt a biztonsági ellenőrzés kihagyása `RestorePolicy.Force` használható. Ez a `RestoreDescription`részeként van megadva.
> 

## <a name="deleted-or-lost-service"></a>Törölt vagy elveszett szolgáltatás
Ha eltávolít egy szolgáltatást, először újra létre kell hoznia a szolgáltatást az adatgyűjtés előtt.  Fontos, hogy a szolgáltatást ugyanazzal a konfigurációval hozza létre, mint például a particionálási séma, így az Adathelyreállítás zökkenőmentesen visszaállítható.  A szolgáltatás bekapcsolása után a szolgáltatás minden partícióján meg kell hívni az API-t, amelyről vissza kell állítani az adatvisszaállítást (`OnDataLossAsync`). Ennek a megvalósításának egyik módja a [FabricClient. TestManagementClient. StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) használata minden partíción.  

Ettől a ponttól kezdve a megvalósítás ugyanaz, mint a fenti forgatókönyv. Minden partíciónak vissza kell állítania a biztonsági mentést a külső tárolóból. Ennek egyik kikötése, hogy a partíció-azonosító mostantól módosult, mivel a futtatókörnyezet dinamikusan hozza létre a partíciós azonosítókat. Ennek megfelelően a szolgáltatásnak meg kell tárolnia a megfelelő partíciós adatokat és a szolgáltatás nevét, hogy azonosítsa a megfelelő legutóbbi biztonsági mentést, amelyet az egyes partíciók esetében vissza kell állítani.

> [!NOTE]
> A teljes szolgáltatás visszaállításához nem ajánlott a `FabricClient.ServiceManager.InvokeDataLossAsync` használata az egyes partíciók esetében, mivel ez megsértheti a fürt állapotát.
> 

## <a name="replication-of-corrupt-application-data"></a>Sérült alkalmazásadatok replikálása
Ha az újonnan telepített alkalmazás frissítése hibát tartalmaz, az adatsérülést eredményezhet. Előfordulhat például, hogy egy alkalmazás frissítése megkezdi az összes telefonszám rekordjának frissítését egy olyan megbízható szótárban, amely érvénytelen körzetszámmal rendelkezik.  Ebben az esetben a rendszer replikálja az érvénytelen telefonszámokat, mivel Service Fabric nem ismeri a tárolt adatok természetét.

Az ilyen, az adatsérülést okozó kirívó hibák észlelése után az első teendő a szolgáltatás alkalmazási szinten történő rögzítése, és ha lehetséges, az alkalmazás kódjának olyan verziójára kell frissítenie, amely nem rendelkezik a hibával.  Azonban még a szolgáltatási kód rögzítése után is előfordulhat, hogy az adatmennyiség sérült, és így előfordulhat, hogy vissza kell állítani az adatmennyiséget.  Ilyen esetekben előfordulhat, hogy nem elegendő a legújabb biztonsági mentés visszaállítása, mert a legújabb biztonsági másolatok is sérültek lehetnek.  Ezért meg kell találnia az utolsó biztonsági mentést, amelyet az adatvesztés előtt hozott létre.

Ha nem tudja biztosan, hogy mely biztonsági másolatok sérültek, telepíthet egy új Service Fabric fürtöt, és visszaállíthatja az érintett partíciók biztonsági másolatait ugyanúgy, mint a fenti "törölt vagy elveszett szolgáltatás" forgatókönyvet.  Minden partíció esetében indítsa el a legutóbbi biztonsági másolatok visszaállítását a legutóbb a legkevésbé. Ha olyan biztonsági mentést talál, amely nem rendelkezik a sérüléssel, helyezze át vagy törölje a partíció összes olyan biztonsági másolatát, amely ennél frissebb (a biztonsági mentéstől számítva). Ismételje meg ezt a folyamatot az egyes partíciók esetében. Ha a `OnDataLossAsync` az éles fürtben található partíción hívja meg, akkor a fenti folyamat a külső tárolóban található utolsó biztonsági mentést fogja kiválasztani.

A "törölt vagy elveszett szolgáltatás" szakaszban leírt lépések segítségével visszaállíthatja a szolgáltatás állapotát az állapotba, mielőtt a hibás kód megsérült az állapot.

Vegye figyelembe:

  - A visszaállítás során előfordulhat, hogy a visszaállítani kívánt biztonsági mentés régebbi, mint a partíció állapota az adatvesztés előtt. Emiatt csak a lehető legkevesebb adattal állíthatja helyre a helyreállítást.
  - A biztonsági mentési mappa elérési útját és a biztonsági mentési mappában található fájlok elérési útját jelölő karakterlánc 255 karakternél hosszabb lehet a FabricDataRoot elérési útjának és az alkalmazás típusának nevétől függően. Ez néhány .NET-metódust (például `Directory.Move`) okozhat a `PathTooLongException` kivétel eldobásához. Az egyik megkerülő megoldás az, hogy közvetlenül hívja a Kernel32 API-kat, például a `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Biztonsági mentés és visszaállítás Reliable Actors


A Reliable Actors-keretrendszer Reliable Servicesra épül. A ActorService, amely a színész (eke) t tárolja, egy állapot-nyilvántartó megbízható szolgáltatás. Ezért a Reliable Services elérhető összes biztonsági mentési és visszaállítási funkció elérhető a Reliable Actors számára is (kivéve az állami szolgáltató által meghatározott viselkedéseket). Mivel a biztonsági mentések particionálás alapján történnek, a partíción lévő összes szereplő állapotáról biztonsági mentés készül (és a visszaállítás hasonló, és a partíciók alapján történik). A biztonsági mentés/visszaállítás végrehajtásához a szolgáltatás tulajdonosának létre kell hoznia egy egyéni Actor Service osztályt, amely a ActorService osztályból származik, majd az előző szakaszokban leírtak szerint a Reliable Services hasonló biztonsági mentést/visszaállítást végez.

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

Egyéni színészi szolgáltatási osztály létrehozásakor regisztrálnia kell azt is a színész regisztrálása során.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Az Reliable Actors alapértelmezett állapot-szolgáltatója `KvsActorStateProvider`. A növekményes biztonsági mentés alapértelmezés szerint nincs engedélyezve a `KvsActorStateProvider`számára. A növekményes biztonsági mentést úgy is engedélyezheti, ha `KvsActorStateProvider` létrehozásával létrehozza a megfelelő beállítást a konstruktorában, majd átadja azt a ActorService konstruktornak a következő kódrészletben látható módon:

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

A növekményes biztonsági mentés engedélyezése után a növekményes biztonsági mentés a következő okok egyike miatt meghiúsulhat a FabricMissingFullBackupException, és teljes biztonsági mentést kell készítenie a növekményes biztonsági mentés (ok) készítése előtt:

  - A replika soha nem készített teljes biztonsági mentést, mert az elsődleges volt.
  - A naplózási rekordok némelyike a legutóbbi biztonsági mentés óta csonkítva lett.

Ha a növekményes biztonsági mentés engedélyezve van, `KvsActorStateProvider` nem használ körkörös puffert a naplók kezeléséhez, és rendszeresen csonkolja azt. Ha a felhasználó nem készít biztonsági másolatot a 45 perces időszakra, a rendszer automatikusan csonkolja a napló rekordjait. Ezt az időközt úgy konfigurálhatja, hogy a `KvsActorStateProvider` konstruktorban `logTruncationIntervalInMinutes` megadásával (a növekményes biztonsági mentés engedélyezésekor). A naplóbejegyzések akkor is lerövidítve jelenhetnek meg, ha az elsődleges replika egy újabb replikát kell létrehoznia az összes adatai elküldésével.

A Reliable Serviceshoz hasonló biztonsági mentési láncból történő visszaállításkor a BackupFolderPath a teljes biztonsági mentést tartalmazó alkönyvtárral és a növekményes biztonsági mentés (eke) t tartalmazó alkönyvtárakkal rendelkező alkönyvtárakat is tartalmaznia kell. Ha a biztonsági mentési lánc ellenőrzése sikertelen, a Restore API a megfelelő hibaüzenettel ellátott FabricException fog dobni. 

> [!NOTE]
> `KvsActorStateProvider` jelenleg figyelmen kívül hagyja a RestorePolicy. Safe beállítást. A szolgáltatás támogatását egy közelgő kiadásban tervezték meg.
> 

## <a name="testing-back-up-and-restore"></a>A biztonsági mentés és a visszaállítás tesztelése
Fontos ellenőrizni, hogy a kritikus fontosságú adatok biztonsági mentése folyamatban van-e, és a rendszerből állítható vissza. Ezt úgy teheti meg, hogy a PowerShellben meghívja a `Start-ServiceFabricPartitionDataLoss` parancsmagot, amely adatvesztést okozhat egy adott partíción annak ellenőrzéséhez, hogy a szolgáltatás adatbiztonsági mentési és visszaállítási funkciója a várt módon működik-e.  Az is lehetséges, hogy programozott módon meghívja az adatvesztést és a visszaállítást az adott eseményből is.

> [!NOTE]
> Megtalálhatja a biztonsági mentési és visszaállítási funkciók megvalósítását a GitHubon elérhető webes hivatkozás alkalmazásban. További részletekért tekintse meg a `Inventory.Service` szolgáltatást.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>A motorháztető alatt: további részletek a biztonsági mentésről és a visszaállításról
Íme néhány további információ a biztonsági mentésről és a visszaállításról.

### <a name="backup"></a>Biztonsági mentés
A megbízható állapot-kezelő lehetővé teszi, hogy konzisztens biztonsági másolatokat hozzon létre az olvasási vagy írási műveletek blokkolása nélkül. Ehhez az ellenőrzőpont-és a naplózási adatmegőrzési mechanizmust használja.  A megbízható állapot-kezelő olyan homályos (könnyűsúlyú) ellenőrzőpontokat tart bizonyos pontokon, amelyekkel a tranzakciós naplóból kikényszerítheti a nyomást, és javíthatja a helyreállítási időpontokat.  `BackupAsync` hívásakor a megbízható State Manager arra utasítja az összes megbízható objektumot, hogy a legújabb ellenőrzőpont-fájljaikat egy helyi biztonsági mentési mappába másolja.  Ezután a megbízható állapot-kezelő az összes naplózási rekordot átmásolja, a "Start" mutatótól kezdve a legújabb naplóig a biztonsági mentési mappába.  Mivel a biztonsági mentés tartalmazza az összes naplózási rekordot a legutóbbi naplóba, és a megbízható állapot kezelője megőrzi a szükséges írási naplózást, a megbízható állapot-kezelő garantálja, hogy a biztonsági mentés tartalmazza az összes véglegesített tranzakciót (`CommitAsync` sikeresen visszaadott).

Minden olyan tranzakció, amely a `BackupAsync` után véglegesítve lett, vagy nem lehet a biztonsági másolatban.  Miután a platform kitöltötte a helyi biztonsági mentési mappát (azaz a helyi biztonsági mentést a futtatókörnyezet végezte el), a rendszer meghívja a szolgáltatás biztonsági mentési visszahívását.  A visszahívás feladata, hogy a biztonsági mentési mappát egy külső helyre, például az Azure Storage-ba helyezze át.

### <a name="restore"></a>Visszaállítás
A megbízható állapot-kezelő lehetővé teszi a biztonsági másolatból való visszaállítást a `RestoreAsync` API használatával.  
A `RestoreContext` `RestoreAsync` metódusa csak a `OnDataLossAsync` metódusban hívható meg.
A `OnDataLossAsync` által visszaadott logikai érték azt jelzi, hogy a szolgáltatás visszaállította-e az állapotát egy külső forrásból.
Ha a `OnDataLossAsync` igaz értéket ad vissza, akkor Service Fabric újraépíti az összes többi replikát ebből az elsődlegesből. Service Fabric biztosítja, hogy a rendszer `OnDataLossAsync` hívja az elsődleges szerepkörre, de ne kapjon olvasási állapotot vagy írási állapotot.
Ez azt jelenti, hogy az StatefulService-végrehajtók esetében a `RunAsync` nem lesz meghívva, amíg a `OnDataLossAsync` sikeresen be nem fejeződik.
Ezt követően a rendszer meghívja a `OnDataLossAsync` az új elsődlegesen.
Amíg egy szolgáltatás sikeresen befejezi ezt az API-t (igaz vagy hamis értéket ad vissza), és befejezi a megfelelő újrakonfigurálást, az API-t a rendszer egyszerre fogja hívni.

`RestoreAsync` először a meghívott elsődleges replika összes meglévő állapotát eldobja. Ezután a megbízható állapot-kezelő létrehozza a biztonsági mentési mappában található összes megbízható objektumot. Ezután a megbízható objektumokat a rendszer arra utasítja, hogy a biztonsági mentési mappában lévő ellenőrzőpontok alapján állítsa vissza. Végül a megbízható állapot-kezelő a biztonsági mentési mappában található naplókból helyreállítja a saját állapotát, és végrehajtja a helyreállítást. A helyreállítási folyamat részeként a rendszer a biztonsági mentési mappában lévő véglegesített naplókat tartalmazó "kezdőpont" kezdetű műveleteket a megbízható objektumokra játssza újra. Ez a lépés biztosítja, hogy a helyreállított állapot konzisztens legyen.

## <a name="next-steps"></a>Következő lépések
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start.md)
  - [Értesítések Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Reliable Services konfiguráció](service-fabric-reliable-services-configuration.md)
  - [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

