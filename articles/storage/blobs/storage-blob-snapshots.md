---
title: BLOB írásvédett pillanatképének létrehozása az Azure Storage-ban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy blob pillanatképét egy adott pillanatban a blob-adatok biztonsági mentéséhez. Megtudhatja, hogyan számítja ki a pillanatképeket, és hogyan használhatja őket a kapacitási díjak minimalizálására.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0da3373ba2c13bd6a00a92a6b38bead86fc9a5ea
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897010"
---
# <a name="create-a-blob-snapshot"></a>Blob-pillanatkép létrehozása

A pillanatképek egy adott időpontban végrehajtott blob írásvédett verziója, amely egy adott időpontban történik. A pillanatképek a Blobok biztonsági mentéséhez hasznosak. A pillanatkép létrehozása után elolvashatja, másolhatja vagy törölheti, de nem módosítható.

A blob pillanatképe azonos az alap blobtal, azzal a különbséggel, hogy a blob URI-ja a blob URI-hoz fűzött **datetime** értékkel rendelkezik, hogy jelezze a pillanatkép készítésének időpontját. Ha például egy oldal blob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd`-ja, a pillanatkép URI-ja `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`hasonló a következőhöz:.

> [!NOTE]
> Minden pillanatkép megosztja az alap blob URI-JÁT. Az alap blob és a pillanatkép közötti egyetlen különbség a hozzáfűzött **datetime** érték.
>

A Blobok tetszőleges számú pillanatképet tartalmazhatnak. A pillanatképek mindaddig megmaradnak, amíg explicit módon nem törlik őket. Egy pillanatkép nem tudja kiélni az alap blobját. Az alap blobhoz társított Pillanatképek enumerálásával nyomon követheti az aktuális pillanatképeket.

Egy blob pillanatképének létrehozásakor a rendszer a blob rendszertulajdonságait a pillanatképre másolja, és ugyanazokat az értékeket. Az alap blob metaadatait a rendszer a pillanatképbe is másolja, kivéve, ha a létrehozáskor külön metaadatokat ad meg a pillanatképhez.

Az alap blobhoz társított bérletek nem érintik a pillanatképet. Nem lehet bérletet beszerezni egy pillanatképen.

Egy VHD-fájl tárolja a virtuálisgép-lemezek aktuális információit és állapotát. Leválaszthat egy lemezt a virtuális gépről, vagy leállíthatja a virtuális gépet, majd pillanatképet készíthet a VHD-fájlról. Ezt a pillanatkép-fájlt később is használhatja a VHD-fájl lekéréséhez az adott időpontban, és újból létrehozhatja a virtuális gépet.

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása
A következő mintakód bemutatja, hogyan hozhat létre pillanatképet az [Azure Storage .net-hez készült ügyféloldali kódtára](/dotnet/api/overview/azure/storage/client)használatával. Ez a példa további metaadatokat határoz meg a pillanatkép létrehozásakor.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Pillanatképek másolása
Blobokat és pillanatképeket érintő másolási műveletek a következő szabályoknak felelnek meg:

* A pillanatképek az alap blobon keresztül másolhatók. Egy pillanatképnek az alap blob pozícióba való előléptetésével a blob egy korábbi verzióját állíthatja vissza. A pillanatkép továbbra is fennáll, de a rendszer felülírja az alap blobot a pillanatkép írható másolatával.
* A pillanatképeket átmásolhatja egy másik néven megadott cél blobba. Az eredményül kapott cél blob egy írható blob, nem pillanatkép.
* A forrás Blobok másolásakor a rendszer nem másolja a forrás blob pillanatképeit a célhelyre. Ha a cél blobot egy másolattal felülírják, az eredeti cél blobhoz társított Pillanatképek érintetlenek maradnak.
* A blokkos Blobok pillanatképének létrehozásakor a rendszer a blob véglegesített blokkolási listáját is átmásolja a pillanatképbe. A nem véglegesített blokkok nem másolódnak át.

## <a name="specify-an-access-condition"></a>Hozzáférési feltétel meghatározása
A [CreateSnapshotAsync][dotnet_CreateSnapshotAsync]meghívásakor megadhat egy hozzáférési feltételt, hogy a pillanatkép csak akkor legyen létrehozva, ha teljesül egy feltétel. Hozzáférési feltétel megadásához használja a [AccessCondition][dotnet_AccessCondition] paramétert. Ha a megadott feltétel nem teljesül, a pillanatkép nem jön létre, és a Blob service a [HTTPStatusCode][dotnet_HTTPStatusCode]állapotkódot adja vissza. PreconditionFailed.

## <a name="delete-snapshots"></a>Pillanatképek törlése
Pillanatképeket csak akkor törölhet, ha a pillanatképek is törlődnek. A pillanatképeket egyenként is törölheti, vagy megadhatja, hogy a rendszer az összes pillanatképet törli a forrás blob törlésekor. Ha olyan blobot próbál törölni, amely még tartalmaz pillanatképeket, akkor a hiba eredménye.

A következő mintakód bemutatja, hogyan törölhet egy blobot és annak pillanatképeit a .net- `blockBlob` ben, ahol a egy [CloudBlockBlob][dotnet_CloudBlockBlob]típusú objektum:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Pillanatképek az Azure Premium Storage
A pillanatképek Premium Storage használatával történő használatakor a következő szabályok érvényesek:

* A prémium szintű Storage-fiókban a pillanatképek maximális száma a 100. Ha túllépi a korlátot, a pillanatkép-blob művelet a 409 (`SnapshotCountExceeded`) hibakódot adja vissza.
* Az oldal blobjának pillanatképét 10 percenként készítheti el egy Premium Storage-fiókban. Ha túllépi a sebességet, a pillanatkép-blob művelet a 409 (`SnapshotOperationRateExceeded`) hibakódot adja vissza.
* A pillanatképek olvasásához a blob másolása művelettel másolhatja a pillanatképet egy másik oldal blobba a fiókban. A másolási művelet céljának blobja nem rendelkezhet meglévő pillanatképekkel. Ha a cél blobhoz Pillanatképek tartoznak, akkor a blob másolása művelet a 409 (`SnapshotsPresent`) hibakódot adja vissza.

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Abszolút URI visszaadása egy pillanatképhez
Ez C# a kódrészlet egy pillanatképet hoz létre, és kiírja az elsődleges hely abszolút URI-ját.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>A pillanatképek felmerülési módjának ismertetése
Pillanatkép létrehozása, amely egy blob írásvédett példánya, további adattárolási díjat eredményezhet a fiókjához. Az alkalmazás tervezésekor fontos tisztában lennie azzal, hogy ezek a díjak hogyan merülhetnek fel, így csökkentheti a költségeket.

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok
A következő lista a pillanatképek létrehozásakor megfontolandó főbb pontokat tartalmazza.

* A Storage-fiók az egyedi blokkok vagy lapok díját terheli, függetlenül attól, hogy azok a blobban vagy a pillanatképben vannak-e. A fiókja nem számít fel további díjat a blobokhoz társított pillanatképekhez, amíg nem frissíti azt a blobot, amelyen alapulnak. Az alap blob frissítése után az a pillanatképtől eltér. Ebben az esetben az egyes Blobok vagy Pillanatképek egyedi blokkait vagy lapjait kell fizetnie.
* Ha egy blokkon belüli blokkot cserél le, a rendszer ezt a blokkot egy egyedi blokkként számítja fel. Ez akkor is igaz, ha a blokk ugyanazzal a blokk-AZONOSÍTÓval és ugyanazokkal az adatokkal rendelkezik, mint a pillanatképben. A blokk újbóli elkövetése után a rendszer az adott pillanatképtől eltér, és az adatokért kell fizetnie. Ugyanez a helyzet igaz egy olyan oldal blobján, amely azonos adattal frissült.
* A [UploadFromFile][dotnet_UploadFromFile], a [UploadText][dotnet_UploadText], a [UploadFromStream][dotnet_UploadFromStream]vagy a [UploadFromByteArray][dotnet_UploadFromByteArray] metódus meghívásával lecserél egy blokk-blobot a blob összes blokkjának cseréjére. Ha az adott blobhoz pillanatkép van társítva, akkor az alap blobban és a pillanatképben lévő összes blokk már eltér egymástól, és a rendszer az összes blokkot felszámítja mindkét blobban. Ez akkor is igaz, ha az alap blobban lévő adatok és a pillanatkép azonos marad.
* Az Azure Blob service nem határozza meg, hogy két blokk tartalmaz-e azonos adathalmazt. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatblokkokat és AZONOSÍTÓkat is tartalmazta. Mivel a díjak egyedi blokkokból állnak, fontos figyelembe venni, hogy a pillanatképet tartalmazó Blobok frissítése további egyedi blokkokat és további díjakat eredményez.

### <a name="minimize-cost-with-snapshot-management"></a>A költséghatékonyság csökkentése a Snapshot Management szolgáltatással

Javasoljuk, hogy gondosan kezelje a pillanatképeket a további költségek elkerülése érdekében. Az alábbi ajánlott eljárásokat követve csökkentheti a pillanatképek tárolásához felmerülő költségeket:

* Egy blobhoz társított Pillanatképek törlése és újbóli létrehozása minden alkalommal, amikor frissíti a blobot, még akkor is, ha az alkalmazás megtervezése nem igényli a pillanatképek karbantartását. A blob Pillanatképek törlésével és újbóli létrehozásával biztosíthatja, hogy a blob és a pillanatképek ne legyenek elválasztva.
* Ha pillanatképeket tart fenn egy blobhoz, a blob frissítéséhez ne hívja meg a [UploadFromFile][dotnet_UploadFromFile], a [UploadText][dotnet_UploadText], a [UploadFromStream][dotnet_UploadFromStream]vagy a [UploadFromByteArray][dotnet_UploadFromByteArray] metódust. Ezek a metódusok a blob összes blokkját lecserélik, így az alap blob és a pillanatképek jelentősen eltérhetnek. Ehelyett frissítse a lehető legkevesebb blokkot a [PutBlock][dotnet_PutBlock] és a [PutBlockList][dotnet_PutBlockList] metódus használatával.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép-számlázási forgatókönyvek
A következő forgatókönyvek azt mutatják be, hogyan merülhetnek fel a díjak a blokkos blobok és a pillanatképek esetében.

**1. forgatókönyv**

Az 1. forgatókönyvben az alap blob nem frissült a pillanatkép készítése után, ezért a díjak csak az 1., 2. és 3. egyedi blokkok esetében merülnek fel.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**2. forgatókönyv**

A 2. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk frissült, annak ellenére, hogy ugyanazokat az adatokkal és ugyanazzal az AZONOSÍTÓval rendelkezik, nem ugyanaz, mint a 3. blokk a pillanatképben. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**3. forgatókönyv**

A 3. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk lecserélve a 4-es blokkra az alap blobban, de a pillanatkép továbbra is a 3. blokkot tükrözi. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**4. forgatókönyv**

A 4. forgatókönyvben az alap blob teljesen frissítve lett, és az eredeti blokk egyikét sem tartalmazza. Ennek eredményeképpen a fiók minden nyolc egyedi blokk után díjat számít fel. Ez a forgatókönyv akkor fordulhat elő, ha olyan frissítési módszert használ, mint például a [UploadFromFile][dotnet_UploadFromFile], a [UploadText][dotnet_UploadText], a [UploadFromStream][dotnet_UploadFromStream]vagy a [UploadFromByteArray][dotnet_UploadFromByteArray], mert ezek a metódusok egy blob összes tartalmát lecserélik.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>További lépések

* További információt a virtuális gép (VM) Pillanatképek használatáról az [Azure nem felügyelt virtuális gépek biztonsági mentése növekményes pillanatképekkel](../../virtual-machines/windows/incremental-snapshots.md) című témakörben talál.

* A blob Storage-t használó további Példákért lásd: [Azure Code Samples](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Letölthet egy minta alkalmazást, és futtathatja, vagy megkeresheti a kódot a GitHubon.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext