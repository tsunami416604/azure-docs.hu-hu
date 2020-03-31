---
title: A Service Fabric biztonsági mentése és helyreállítása
description: Koncepcionális dokumentáció a Service Fabric biztonsági mentési és visszaállítási, a megbízható állapotalapú szolgáltatások és a megbízható szereplők biztonsági mentésének konfigurálásához.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922774"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Megbízható szolgáltatások és megbízható szereplők biztonsági mentése és visszaállítása
Az Azure Service Fabric egy magas rendelkezésre állású platform, amely replikálja az állapot ot több csomóponton, hogy fenntartsa ezt a magas rendelkezésre állást.  Így még akkor is, ha a fürt egyik csomópontja meghibásodik, a szolgáltatások továbbra is elérhetők. Bár ez a beépített redundancia, amelyet a platform biztosít, elegendő lehet néhány számára, bizonyos esetekben kívánatos, hogy a szolgáltatás biztonsági másolatot készüljön az adatokról (egy külső tárolóba).

> [!NOTE]
> Fontos, hogy biztonsági másolatot készítsen és visszaállítsa az adatokat (és tesztelje, hogy a várt módon működik-e), így helyreállíthatja az adatvesztési forgatókönyveket.
> 

> [!NOTE]
> A Microsoft azt javasolja, hogy [rendszeres biztonsági mentési és visszaállítási](service-fabric-backuprestoreservice-quickstart-azurecluster.md) megbízható állapotalapú szolgáltatások és megbízható szereplők adatbiztonsági biztonsági mentésének konfigurálása. 
> 


Előfordulhat például, hogy egy szolgáltatás biztonsági másolatot szeretne tenni az adatokról, hogy védelmet nyújtjon a következő esetekben:

- Abban az esetben, ha a teljes Service Fabric-fürt végleges elvesztése.
- A szolgáltatáspartíció kópiák többségének végleges elvesztése
- Felügyeleti hibák, amelyek alapján az állapot véletlenül törlődik vagy megsérül. Ez például akkor fordulhat elő, ha egy megfelelő jogosultsággal rendelkező rendszergazda tévesen törli a szolgáltatást.
- Hibák a szolgáltatásban, amelyek adatsérülést okoznak. Ez például akkor fordulhat elő, ha egy szolgáltatáskód-frissítés hibás adatokat kezd írni egy megbízható gyűjteménybe. Ebben az esetben előfordulhat, hogy mind a kódot, mind az adatokat vissza kell fordítani egy korábbi állapotba.
- Offline adatfeldolgozás. Célszerű lehet az adatok offline feldolgozása az üzleti intelligencia számára, amely az adatokat generáló szolgáltatástól elkülönítve történik.

A Biztonsági mentés/visszaállítás funkció lehetővé teszi a Reliable Services API-ra épülő szolgáltatások számára a biztonsági mentések létrehozását és visszaállítását. A platform által biztosított biztonsági másolat API-k lehetővé teszik a szolgáltatáspartíció állapotának biztonsági mentését anélkül, hogy blokkolnák az olvasási vagy írási műveleteket. A visszaállítási API-k lehetővé teszik a szolgáltatáspartíció állapotának visszaállítását egy kiválasztott biztonsági másolatból.

## <a name="types-of-backup"></a>A biztonsági mentés típusai
Két biztonsági mentési lehetőség van: Teljes és Növekményes.
A teljes biztonsági másolat egy biztonsági másolat, amely tartalmazza a replika állapotának újbóli létrehozásához szükséges összes adatot: ellenőrzőpontokat és az összes naplórekordot.
Mivel rendelkezik az ellenőrzőpontokkal és a naplóval, a teljes biztonsági mentés önmagában is visszaállítható.

A probléma a teljes biztonsági mentések akkor merül fel, amikor az ellenőrzőpontok nagyok.
Például egy 16 GB-os állapotú replika ellenőrzőpontokkal rendelkezik, amelyek körülbelül 16 GB-ot adnak ki.
Ha van egy 5 perces helyreállítási pontcélkitűzésünk, a replikáról öt percenként biztonsági másolatot kell készített.
Minden alkalommal, amikor biztonsági másolatot készít, 16 GB ellenőrzőpontokat kell másolnia az 50 MB -os (konfigurálható) `CheckpointThresholdInMB`naplók on kívül.

![Teljes biztonsági mentés példa.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A probléma megoldása a növekményes biztonsági mentések, ahol a biztonsági mentés csak a legutóbbi biztonsági mentés óta módosított naplórekordokat tartalmazza.

![Példa növekményes biztonsági mentésre.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Mivel a növekményes biztonsági mentések csak a legutóbbi biztonsági mentés óta módosulnak (nem tartalmazza az ellenőrzőpontokat), általában gyorsabbak, de önmagukban nem állíthatók vissza.
Növekményes biztonsági mentés visszaállításához a teljes biztonsági mentési lánc szükséges.
A biztonsági mentési lánc a biztonsági mentések láncolata, amely teljes biztonsági másolattal kezdődik, majd számos összefüggő növekményes biztonsági mentés következik.

## <a name="backup-reliable-services"></a>Megbízható szolgáltatások biztonsági mentése
A szolgáltatás szerzője teljes hozzáféréssel rendelkezik a biztonsági mentések készítésének időpontjáról, és a biztonsági mentések tárolási helyéről.

A biztonsági mentés elindításához a szolgáltatásnak meg `BackupAsync`kell hívnia az örökölt tagfüggvényt.  
Biztonsági mentések csak az elsődleges replikák, és írási állapot ot kell adni.

Amint az `BackupAsync` alábbiakban `BackupDescription` látható, vesz egy objektumot, ahol meg adhat egy `Func<< BackupInfo, CancellationToken, Task<bool>>>` teljes vagy növekményes biztonsági mentést, valamint egy visszahívási függvényt, amelyakkor hívható meg, amikor a biztonsági másolat mappája helyileg lett létrehozva, és készen áll arra, hogy áthelyezhető egy külső tárolóba.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

A növekményes `FabricMissingFullBackupException`biztonsági mentésre vonatkozó kérés sikertelen lehet a segítségével. Ez a kivétel azt jelzi, hogy az alábbi események egyike történik:

- a replika soha nem vett teljes biztonsági mentést, mivel elsődlegesvé vált,
- a naplórekordok egy része az utolsó biztonsági mentés csonkolása vagy
- a replika túllépte a `MaxAccumulatedBackupLogSizeInMB` korlátot.

A felhasználók növelhetik annak valószínűségét, hogy növekményes biztonsági mentéseket végezhessenek a vagy `MinLogSizeInMB` `TruncationThresholdFactor`a konfigurálásával.
Ezen értékek növelésével növeli a replika lemezhasználat.
További információ: [Megbízható szolgáltatások konfigurációja](service-fabric-reliable-services-configuration.md)

`BackupInfo`tájékoztatást nyújt a biztonsági mentésről, beleértve annak a mappának`BackupInfo.Directory`a helyét, ahová a futásidejű mentette a biztonsági másolatot ( ). A visszahívási funkció `BackupInfo.Directory` áthelyezheti a külső tárolóba vagy más helyre.  Ez a függvény egy bool-t is ad vissza, amely azt jelzi, hogy sikerült-e a biztonsági másolat mappáját a célhelyre helyezni.

A következő kód bemutatja, hogyan használható a `BackupCallbackAsync` módszer a biztonsági mentés Azure Storage-ba való feltöltéséhez:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Az előző példában a mintaosztály, `ExternalBackupStore` amely az Azure `UploadBackupFolderAsync` Blob storage felületére szolgál, és az a módszer, amely tömöríti a mappát, és elhelyezi az Azure Blob áruházban.

Vegye figyelembe:

  - Replikánként csak egy biztonsági mentési művelet lehet egy adott időpontban. Egyszerre több `BackupAsync` hívás is megnöveli `FabricBackupInProgressException` a fedélzeti mentés korlátozását.
  - Ha egy replika átadja a feladatait, miközben a biztonsági mentés folyamatban van, előfordulhat, hogy a biztonsági mentés nem fejeződött be. Így a feladatátvétel befejezése után a szolgáltatás feladata, hogy szükség esetén `BackupAsync` szükség esetén újraindítsa a biztonsági mentést.

## <a name="restore-reliable-services"></a>Megbízható szolgáltatások visszaállítása
Általában azok az esetek, amikor szükség lehet egy visszaállítási művelet végrehajtására, az alábbi kategóriák egyikébe tartoznak:

  - A szolgáltatáspartíció elveszett adatai. Például a partíció (beleértve az elsődleges replikát is) három replikájából kettő lemeze megsérül vagy törlődik. Előfordulhat, hogy az új elsődleges állapotnak vissza kell állítania az adatokat egy biztonsági másolatból.
  - Az egész szolgáltatás elveszett. Például egy rendszergazda eltávolítja a teljes szolgáltatást, és így a szolgáltatást és az adatokat vissza kell állítani.
  - A szolgáltatás replikált sérült alkalmazásadatokat replikált (például egy alkalmazáshiba miatt). Ebben az esetben a szolgáltatást frissíteni kell, vagy vissza kell állítani a sérülés okának eltávolításához, és a nem sérült adatokat vissza kell állítani.

Bár számos megközelítés lehetséges, kínálunk néhány `RestoreAsync` példát a fenti forgatókönyvek helyreállítására.

## <a name="partition-data-loss-in-reliable-services"></a>Partíció adatvesztése a megbízható szolgáltatásokban
Ebben az esetben a futásidejű automatikusan észleli `OnDataLossAsync` az adatvesztést, és meghívja az API-t.

A szolgáltatás szerzőjének a következőket kell végrehajtania a helyreállításhoz:

  - A virtuális alaposztály `OnDataLossAsync`metódusának felülbírálása .
  - Keresse meg a legújabb biztonsági másolatot a szolgáltatás biztonsági mentését tartalmazó külső helyen.
  - Töltse le a legújabb biztonsági mentést (és csomagolja ki a biztonsági másolatot a biztonsági másolat mappába, ha tömörített).
  - A `OnDataLossAsync` módszer `RestoreContext`egy . Hívja `RestoreAsync` meg az `RestoreContext`API-t a megadott .
  - Visszatérés igaz, ha a helyreállítás sikeres volt.

Az alábbiakban egy `OnDataLossAsync` példa végrehajtása a módszer:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`a híváshoz `RestoreContext.RestoreAsync` egy . `BackupFolderPath`
Egyetlen teljes biztonsági mentés visszaállításakor ezt `BackupFolderPath` a teljes biztonsági másolatot tartalmazó mappa helyi elérési útjána kell beállítani.
A teljes biztonsági mentés és számos növekményes biztonsági mentés visszaállításakor a mappa helyi elérési útját kell beállítani, `BackupFolderPath` amely nem csak a teljes biztonsági mentést tartalmazza, hanem az összes növekményes biztonsági mentést is.
`RestoreAsync`hívás `FabricMissingFullBackupException` dobhat, `BackupFolderPath` ha a megadott nem tartalmaz teljes biztonsági mentést.
Azt is `ArgumentException` dobja, ha `BackupFolderPath` van egy törött lánc növekményes biztonsági mentések.
Például ha tartalmazza a teljes biztonsági mentést, az első növekményes és a harmadik növekményes biztonsági mentést, de nem a második növekményes biztonsági mentést.

> [!NOTE]
> A RestorePolicy alapértelmezés szerint Biztonságos értékre van állítva.  Ez azt `RestoreAsync` jelenti, hogy az API sikertelen lesz a ArgumentException szolgáltatással, ha azt észleli, hogy a biztonsági másolat mappája olyan állapotot tartalmaz, amely régebbi vagy egyenlő a replikában lévő állapottal.  `RestorePolicy.Force`segítségével kihagyhatja ezt a biztonsági ellenőrzést. Ez a részeként `RestoreDescription`van megadva.
> 

## <a name="deleted-or-lost-service"></a>Törölt vagy elveszett szolgáltatás
Ha egy szolgáltatást eltávolítanak, az adatok visszaállítása előtt újra létre kell hoznia a szolgáltatást.  Fontos, hogy a szolgáltatás létrehozása ugyanazzal a konfigurációval, például particionálási séma, hogy az adatok zökkenőmentesen visszaállíthatók.  Miután a szolgáltatás működik, az`OnDataLossAsync` adatok visszaállításához szükséges API-t (fent) meg kell hívni a szolgáltatás minden partícióján. Ennek egyik módja a [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) használata minden partíción.  

Ettől a ponttól kezdve a megvalósítás megegyezik a fenti forgatókönyvvel. Minden partíciónak vissza kell állítania a külső tárolóból a legújabb megfelelő biztonsági mentést. Az egyik kikötés az, hogy a partíció azonosítója most megváltozott, mivel a futásidejű dinamikusan hozza létre a partícióazonosítókat. Így a szolgáltatásnak tárolnia kell a megfelelő partícióadatokat és szolgáltatásnevet a megfelelő legutóbbi biztonsági mentés azonosításához az egyes partíciókhoz.

> [!NOTE]
> Nem ajánlott minden `FabricClient.ServiceManager.InvokeDataLossAsync` partíción használni a teljes szolgáltatás visszaállításához, mivel ez megsérülhet a fürt állapotában.
> 

## <a name="replication-of-corrupt-application-data"></a>Sérült alkalmazásadatok replikációja
Ha az újonnan telepített alkalmazásfrissítés hibát okoz, az adatok sérülését okozhatja. Előfordulhat például, hogy egy alkalmazásfrissítés elkezdi frissíteni a Megbízható szótár minden telefonszámrekordját, érvénytelen körzetszámmal.  Ebben az esetben az érvénytelen telefonszámok replikálni fogják, mivel a Service Fabric nem ismeri a tárolt adatok jellegét.

Az első dolog, miután észleli az ilyen példátlan hibát, amely adatsérülést okoz, hogy lefagyassza a szolgáltatást az alkalmazás szintjén, és ha lehetséges, frissítsen az alkalmazáskód olyan verziójára, amely nem rendelkezik a hibával.  Azonban még a szolgáltatáskód rögzítése után is előfordulhat, hogy az adatok sérültek, ezért az adatokat vissza kell állítani.  Ilyen esetekben előfordulhat, hogy nem elegendő a legutóbbi biztonsági mentés visszaállítása, mivel a legutóbbi biztonsági mentések is sérültek lehetnek.  Így meg kell találnia az utolsó biztonsági mentést, amely az adatok sérülése előtt készült.

Ha nem biztos abban, hogy mely biztonsági mentések sérültek, üzembe helyezhet egy új Service Fabric-fürtöt, és visszaállíthatja az érintett partíciók biztonsági másolatait, ahogy a fenti "Törölt vagy elveszett szolgáltatás" forgatókönyv is.  Minden partíció esetében indítsa el a biztonsági mentések visszaállítását a legutóbbitól a legkevésbé. Ha olyan biztonsági másolatot talál, amely nem rendelkezik a sérüléssel, helyezze át/törölje a partíció összes olyan biztonsági másolatát, amely újabb (mint a biztonsági másolat) volt. Ismételje meg ezt a folyamatot minden partícióesetében. Most, `OnDataLossAsync` amikor megvan hívva a partíción az éles fürtben, a külső tárolóban található utolsó biztonsági mentés lesz a fenti folyamat által kiválasztott.

Most a "Törölt vagy elveszett szolgáltatás" szakaszlépései segítségével visszaállíthatja a szolgáltatás állapotát az állapotra, mielőtt a hibás kód sérült volna meg az állapotban.

Vegye figyelembe:

  - Visszaállításkor előfordulhat, hogy a visszaállított biztonsági másolat régebbi, mint a partíció állapota az adatok elveszése előtt. Emiatt csak végső megoldásként állítsa vissza a visszaállítást, hogy a lehető legtöbb adatot helyreállítsa.
  - A biztonsági másolat mappájának elérési útját és a biztonsági másolat mappájában lévő fájlok elérési útját jelölő karakterlánc a FabricDataRoot elérési útjától és az alkalmazástípus nevének hosszától függően 255 karakternél nagyobb lehet. Ez azt eredményezheti, hogy `Directory.Move`egyes .NET metódusok, például a `PathTooLongException` kivétel eldobása. Az egyik megoldás az, hogy közvetlenül `CopyFile`hívja kernel32 API-k, mint a .

## <a name="back-up-and-restore-reliable-actors"></a>Megbízható szereplők biztonsági és visszaállítása


A Reliable Actors Framework a megbízható szolgáltatásokra épül. Az ActorService, amely az aktor(ok) egy állapotalapú megbízható szolgáltatás. Ezért a megbízható szolgáltatásokban elérhető összes biztonsági mentési és visszaállítási funkció elérhető a Reliable Actors számára is (kivéve az állapotszolgáltató-specifikus viselkedéseket). Mivel a biztonsági mentések partíciónként történik, a partíció összes szereplőjének állapotáról biztonsági mentés készül (és a helyreállítás hasonló, és partíciónként fog történni). Biztonsági mentés/visszaállítás végrehajtásához a szolgáltatás tulajdonosának létre kell hoznia egy egyéni aktor szolgáltatásosztályt, amely az ActorService osztályból származik, majd a megbízható szolgáltatásokhoz hasonló biztonsági mentést/visszaállítást kell végrehajtania az előző szakaszokban leírtak szerint.

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

Amikor létrehoz egy egyéni aktor szolgáltatásosztály, regisztrálnia kell, hogy az aktor regisztrálásakor is.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

A Reliable Actors alapértelmezett `KvsActorStateProvider`állapotszolgáltatója a . A növekményes `KvsActorStateProvider`biztonsági mentés alapértelmezés szerint nincs engedélyezve a esetén. A növekményes biztonsági mentést úgy engedélyezheti, hogy `KvsActorStateProvider` létrehozza a megfelelő beállítást a konstruktorban, majd átadja azt az ActorService konstruktornak a következő kódrészletben látható módon:

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

Miután a növekményes biztonsági mentés engedélyezve lett, a növekményes biztonsági mentés végrehajtása sikertelen lehet a FabricMissingFullBackupException-tel a következő okok valamelyike miatt, és a növekményes biztonsági mentés végrehajtása előtt teljes biztonsági mentést kell készítenie:

  - A replika soha nem vett teljes biztonsági mentést, mivel elsődleges lett.
  - A legutóbbi biztonsági mentés óta a naplórekordok egy része csonkolva lett.

Ha a növekményes biztonsági mentés engedélyezve van, `KvsActorStateProvider` nem használ körkörös puffert a naplórekordok kezelésére, és rendszeresen csonkolja azt. Ha a felhasználó 45 percig nem készít biztonsági másolatot, a rendszer automatikusan csonkolja a naplórekordokat. Ez az időköz konfigurálható `KvsActorStateProvider` a konstruktorban megadott `logTruncationIntervalInMinutes` módon (hasonlóan a növekményes biztonsági mentés engedélyezéséhez). A naplórekordok is csonkulhatnak, ha az elsődleges kópiának az összes adat elküldésével egy másik replika létrehozásához kell.

A Megbízható szolgáltatásokhoz hasonló biztonsági mentési láncból történő visszaállítás során a BackupFolderPath-nak olyan alkönyvtárakat kell tartalmaznia, amelyek egy alkönyvtára teljes biztonsági mentést, a többi alkönyvtár pedig növekményes biztonsági mentést(i)t tartalmaz. A visszaállítási API a FabricException-et a megfelelő hibaüzenettel hozza el, ha a biztonsági mentési lánc érvényesítése sikertelen. 

> [!NOTE]
> `KvsActorStateProvider`jelenleg figyelmen kívül hagyja a RestorePolicy.Safe lehetőséget. A funkció támogatását egy közelgő kiadásban tervezik.
> 

## <a name="testing-back-up-and-restore"></a>Tesztelés Biztonsági másolatot és visszaállítás
Fontos annak biztosítása, hogy a kritikus adatokról biztonsági másolatot lehessen kapni, és visszaállíthatók. Ez úgy érhető el, `Start-ServiceFabricPartitionDataLoss` hogy a PowerShell ben lévő parancsmag meghívásával, amely egy adott partíción adatvesztést okozhat annak tesztelésére, hogy a szolgáltatás adatbiztonsági és-visszaállítási funkciója a várt módon működik-e.  Az is lehetséges, hogy programozott módon hívja adatvesztés és visszaállítása, hogy az esemény is.

> [!NOTE]
> A biztonsági mentési és visszaállítási funkciók mintaimplementációját a GitHub webreferencia-alkalmazásában találja. Kérjük, `Inventory.Service` tekintse meg a szolgáltatást a további részletekért.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>A motorháztető alatt: további részletek a biztonsági mentésről és a visszaállításról
Íme néhány további részlet a biztonsági mentésről és a visszaállításról.

### <a name="backup"></a>Backup
A Megbízható állapotkezelő lehetővé teszi, hogy konzisztens biztonsági mentések létrehozása nélkül letiltása olvasási vagy írási műveleteket. Ehhez egy ellenőrzőpontot és naplóperziós mechanizmust használ.  A megbízható állapotkezelő bizonyos pontokon fuzzy (könnyű) ellenőrzőpontokat vesz igénybe a tranzakciós napló nyomásának enyhítésére és a helyreállítási idő javítására.  A `BackupAsync` megbízható állapotkezelő utasítja az összes megbízható objektumot, hogy másolja a legújabb ellenőrzőpont-fájlokat egy helyi biztonsági mentési mappába.  Ezután a Megbízható állapotkezelő átmásolja az összes naplórekordot, kezdve a "start pointer" a legújabb naplórekord a biztonsági mentési mappába.  Mivel a legutóbbi naplórekordig minden naplórekord szerepel a biztonsági mentésben, és a Reliable State Manager megőrzi az írási`CommitAsync` előre történő naplózást, a Reliable State Manager garantálja, hogy a véglegesített (sikeresen visszaadott) tranzakciók szerepelnek a biztonsági mentésben.

Minden olyan tranzakció, `BackupAsync` amely véglegesítése után már hívott lehet, hogy nem lehet a biztonsági mentésben.  Miután a helyi biztonsági mentési mappát a platform feltöltötte (azaz a helyi biztonsági mentést a futásidő befejezi), a szolgáltatás biztonsági mentési visszahívását a rendszer meghívja.  Ez a visszahívás felelős a biztonsági mentési mappa áthelyezéséért egy külső helyre, például az Azure Storage-ba.

### <a name="restore"></a>Visszaállítás
A Megbízható állapotkezelő lehetővé teszi a biztonsági `RestoreAsync` másolatból az API használatával történő visszaállítást.  
A `RestoreAsync` metódus `RestoreContext` csak a `OnDataLossAsync` metóduson belül hívható meg.
A bool `OnDataLossAsync` visszaazt jelzi, hogy a szolgáltatás visszaállította az állapotát egy külső forrásból.
Ha `OnDataLossAsync` a függvény igaz értéket ad vissza, a Service Fabric újraépíti az összes többi replikák az elsődleges. A Service Fabric biztosítja, hogy `OnDataLossAsync` a replikák, amelyek fogadásra hívás első átmenet az elsődleges szerepkörbe, de nem kapnak olvasási állapot vagy írási állapot.
Ez azt jelenti, hogy a `RunAsync` StatefulService `OnDataLossAsync` implementers, nem lesz meghívva, amíg sikeresen befejeződik.
Ezután `OnDataLossAsync` a lesz meghívva az új elsődleges.
Amíg egy szolgáltatás sikeresen be nem fejezi ezt az API-t (igaz vagy hamis visszaadással), és be nem fejezi a megfelelő újrakonfigurálást, az API-t továbbra is egyenként hívják meg.

`RestoreAsync`először eldobja az összes meglévő állapotot az elsődleges replika, amely a levolt hívva. Ezután a Megbízható állapotkezelő létrehozza az összes megbízható objektumot, amely a biztonsági mentési mappában található. Ezután a Megbízható objektumok utasítást kap, hogy állítsa vissza a biztonsági mentési mappában lévő ellenőrzőpontokról. Végül a Megbízható állapotkezelő helyreállítja a saját állapotát a biztonsági mentési mappában lévő naplórekordokból, és helyreállítást hajt végre. A helyreállítási folyamat részeként a "kiindulási ponttól" kezdődő, a biztonsági másolat mappában naplórekordokat véglegesítő műveleteket a rendszer újra lejátssza a Megbízható objektumoknak. Ez a lépés biztosítja, hogy a helyreállított állapot konzisztens legyen.

## <a name="next-steps"></a>További lépések
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Megbízható szolgáltatások rövid útmutató](service-fabric-reliable-services-quick-start.md)
  - [Megbízható szolgáltatásokról szóló értesítések](service-fabric-reliable-services-notifications.md)
  - [Megbízható szolgáltatások konfigurációja](service-fabric-reliable-services-configuration.md)
  - [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

