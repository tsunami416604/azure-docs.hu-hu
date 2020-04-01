---
title: Blob-pillanatkép létrehozása és kezelése a .NET szolgáltatásban – Azure Storage
description: Ismerje meg, hogyan hozhat létre csak olvasható pillanatképet egy blobról a blobadatok egy adott időpontban való biztonsági másolatot készíteni.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474093"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Blob-pillanatkép létrehozása és kezelése a .NET-ben

A pillanatkép egy blob egy adott időpontban készített, csak olvasható verziója. A pillanatképek a blobok biztonsági mentésénekhez hasznosak. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet blob-pillanatképeket a [.NET Azure Storage-ügyfélkódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)használatával.

## <a name="about-blob-snapshots"></a>A blobpillanatképek –

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Egy blob pillanatképe megegyezik az alap blob, azzal a különbséggel, hogy a blob URI **egy DateTime** értéket a blob URI-hoz csatolva, hogy a pillanatkép készítése időpontját. Ha például egy lapblob `http://storagesample.core.blob.windows.net/mydrives/myvhd`URI-ja, a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`pillanatkép URI-ja hasonló a hoz.

> [!NOTE]
> Minden pillanatkép megosztja az alapblob URI-ját. Az egyetlen különbség az alap blob és a pillanatkép a hozzáfűzve **DateTime** érték.
>

Egy blob hozhat tetszőleges számú pillanatképek. A pillanatképek mindaddig megmaradnak, amíg explicit módon nem törlődnek, ami azt jelenti, hogy egy pillanatkép nem élheti túl az alapblobját. Az alapblobhoz társított pillanatképek számbavétele az aktuális pillanatképek nyomon követéséhez.

Amikor létrehoz egy pillanatképet egy blobról, a blob rendszertulajdonságai ugyanazzal az értékekkel kerülnek a pillanatképbe. Az alapblob metaadatai is átmásolva vannak a pillanatképbe, kivéve, ha a pillanatkép létrehozásakor külön metaadatokat ad meg. A pillanatkép létrehozása után elolvashatja, másolhatja vagy törölheti, de nem módosíthatja.

Az alapblobhoz társított címbérletek nincsenek hatással a pillanatképre. Pillanatképbérlet nem szerezhető be.

A virtuális merevlemez-fájl a virtuális gép lemezének aktuális információinak és állapotának tárolására szolgál. Leválaszthat egy lemezt a virtuális gépről, vagy leállíthatja a virtuális gép, majd pillanatképet a virtuális merevlemez-fájl. Ezt a pillanatképfájlt később a virtuális merevlemez-fájl lekéréséhez az adott időpontban, és újra a virtuális gép.

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Blokkblob pillanatképének létrehozásához használja az alábbi módszerek egyikét:

- [Pillanatkép létrehozása](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

A következő kód példa bemutatja, hogyan hozhat létre pillanatképet. Ez a példa további metaadatokat ad meg a pillanatkép létrehozásának időpontjában.

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
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
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

## <a name="delete-snapshots"></a>Pillanatképek törlése

Blob törléséhez először törölnie kell az adott blob pillanatképeit. Törölheti a pillanatképet külön-külön, vagy megadhatja, hogy az összes pillanatkép törlődjön a forrásblob törlésekor. Ha olyan blobot próbál törölni, amely még mindig rendelkezik pillanatképekkel, hibaüzenetet kell elérnie.

Blob-pillanatképek törléséhez használja az alábbi blob törlési módszerek egyikét, és adja meg a [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) enum.

- [Szabályzat](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

A következő példa kód bemutatja, hogyan lehet törölni egy `blockBlob` blobot és pillanatképeit a .NET-ben, ahol egy [CloudBlockBlob][dotnet_CloudBlockBlob]típusú objektum található:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Az abszolút URI visszaküldése pillanatképbe

A következő kód példa létrehoz egy pillanatképet, és írja ki az abszolút URI az elsődleges hely.

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

## <a name="understand-how-snapshots-accrue-charges"></a>A pillanatképek terhelési felhalmozási módjának ismertetése

Egy pillanatkép létrehozása, amely egy blob csak olvasható másolata, további adattárolási költségeket eredményezhet a fiókjában. Az alkalmazás tervezésekor fontos tisztában lenni azzal, hogy ezek a költségek felhalmozódhatnak, így minimalizálhatja a költségeket.

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok

Az alábbi lista a pillanatkép létrehozásakor figyelembe vesszen kulcsfontosságú pontokat.

- A tárfiók díjat számít fel az egyedi blokkokért vagy lapokért, függetlenül attól, hogy a blobban vagy a pillanatképben vannak-e. A fiók nem vállal további díjakat a blobhoz társított pillanatképekért, amíg nem frissíti azt a blobot, amelyen alapulnak. Az alapblob frissítése után eltér a pillanatképektől. Ha ez megtörténik, az egyes blobokban vagy pillanatképekben lévő egyedi blokkokért vagy lapokért díjat számítunk fel.
- Ha lecserél egy blokkot egy blokkblobon belül, akkor a blokk ot később egyedi blokkként terheli meg a rendszer. Ez akkor is igaz, ha a blokk rendelkezik ugyanazokkal a blokkazonosítóval és adatokkal, mint a pillanatképben. Miután a blokk újra véglegesítésre került, minden pillanatfelvételben eltér a megfelelőjétől, és az adatokért díjat számítunk fel. Ugyanez igaz az azonos adatokkal frissített lapblobok egy lapjára is.
- A blokkblob lecserélése az [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]vagy [UploadFromByteArray][dotnet_UploadFromByteArray] metódus hívásával lecseréli a blob összes blokkját. Ha rendelkezik egy pillanatkép társított blob, az összes blokk az alap blob és pillanatkép most eltérnek, és mindkét blobok minden blokk díjat számítunk fel. Ez akkor is igaz, ha az adatok az alap blob és a pillanatkép azonos marad.
- Az Azure Blob szolgáltatás nem rendelkezik egy eszközzel annak megállapítására, hogy két blokk azonos adatokat tartalmaznak. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatokat és azonos blokkazonosítót rendelkezik. Mivel az egyedi blokkok díjai halmozódnak fel, fontos figyelembe venni, hogy egy blob frissítése, amely egy pillanatkép további egyedi blokkokat és további díjakat eredményez.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizálja a költségeket a pillanatkép-kezeléssel

Javasoljuk, hogy gondosan kezelje a pillanatképeket, hogy elkerülje a többletköltségeket. Az alábbi gyakorlati tanácsokat követve minimalizálhatja a pillanatképek tárolásával kapcsolatban felmerülő költségeket:

- Törölje, és hozzon létre újra pillanatképeket társított blob, amikor frissíti a blobot, akkor is, ha frissíti az azonos adatokat, kivéve, ha az alkalmazás kialakítása megköveteli, hogy a pillanatképek karbantartása. A blob pillanatképeinek törlésével és újbóli létrehozásával biztosíthatja, hogy a blob és a pillanatképek ne térjenek el egymástól.
- Ha egy blob pillanatképeit tartja karban, ne hívja meg [az UploadFromFile,][dotnet_UploadFromFile] [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]vagy [UploadFromByteArray metódust][dotnet_UploadFromByteArray] a blob frissítéséhez. Ezek a módszerek a blob összes blokkját lecserélik, ami az alapblob és a pillanatképek jelentősen eltérnek. Ehelyett frissítse a lehető legkevesebb blokkot a [PutBlock][dotnet_PutBlock] és a [PutBlockList][dotnet_PutBlockList] metódusokkal.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép számlázási forgatókönyvek

A következő forgatókönyvek bemutatják, hogyan díjak halmozódnak fel egy blokk blob és a pillanatképek.

#### <a name="scenario-1"></a>1. példa

Az 1.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>2. példa

2. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3-as blokk frissítve lett, és bár ugyanazokat az adatokat és azonosítókat tartalmazza, nem ugyanaz, mint a 3-as blokk a pillanatképben. Ennek eredményeképpen a fiók négy blokkért kerül felszámolásra.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>3. példa

A 3. A 3-as blokkot az alapblob 4-es blokkja váltotta fel, de a pillanatkép továbbra is a 3-as blokkot tükrözi. Ennek eredményeképpen a fiók négy blokkért kerül felszámolásra.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>4. példa

4. forgatókönyv, az alap blob teljesen frissítve lett, és nem tartalmazza az eredeti blokkokat. Ennek eredményeképpen a fiók mind a nyolc egyedi blokkért felszámolásra kerül. Ez a forgatókönyv akkor fordulhat elő, ha olyan frissítési módszert használ, mint [az UploadFromFile,][dotnet_UploadFromFile] [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]vagy [UploadFromByteArray][dotnet_UploadFromByteArray], mert ezek a módszerek egy blob teljes tartalmát felülírják.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>További lépések

- A virtuális gép (VM) lemezpillanatképeinek használatával kapcsolatos további információkat az [Azure nem felügyelt virtuálisgép-lemezeinek biztonsági másolatot készíteniin növekményes pillanatképeksegítségével talál.](../../virtual-machines/windows/incremental-snapshots.md)

- A Blob storage használatával további kódpéldákat az [Azure Code Samples (Azure Code Samples) (További kódminták) (Azure Code Samples) (További kódminták) (Azure Code Samples) (További](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)kód Letölthet egy mintaalkalmazást, és futtathatja azt, vagy böngészhet a kódban a GitHubon.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
