---
title: A blob csak olvasható pillanatkép létrehozása az Azure Storage-ban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy pillanatképet egy blobról biztonsági másolatok blob egy adott pillanatban időben. Ismerje meg, hogyan számlázzuk a pillanatképek és a használatukkal kapacitás költségek minimalizálása érdekében.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ac13b40ae58054b091963de198213c1a68fcdc05
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244844"
---
# <a name="create-a-blob-snapshot"></a>Blob-pillanatkép létrehozása

Egy pillanatképet egy BLOB egy időben végrehajtott csak olvasható verziója is. A pillanatképek hasznosak blobok biztonsági mentéséről. Miután létrehozott egy pillanatkép, olvassa el, másolja, vagy törölje azt, de nem módosíthatja.

Egy pillanatképet egy blobról megegyezik a kiindulási blob, azzal a különbséggel, hogy a blob URI-t egy **DateTime** az érték hozzáadódik a blob URI-t, amellyel a pillanatkép időpontját jelzi. Például ha egy lap blob URI-t, `http://storagesample.core.blob.windows.net/mydrives/myvhd`, a pillanatkép-URI-ja hasonló `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Összes pillanatkép megosztása a base sablonblob URI azonosítója. A csak az alap blob és a pillanatkép megkülönböztetése a hozzáfűzött **DateTime** értéket.
>

Egy blob pillanatképeinek tetszőleges számú rendelkezhet. A pillanatképek továbbra is fennáll, addig, amíg explicit módon törlődnek. A pillanatkép nem outlive az alap blob. A pillanatképek nyomon követéséhez az aktuális pillanatképek alap blob társított enumerálása.

Amikor létrehoz egy pillanatképet egy blobról, a blob rendszer tulajdonságai a pillanatkép ugyanazon értékekkel lesz másolva. Ha nem ad meg, a pillanatkép külön metaadatok létrehozásakor az alap blob metaadatait is másolja a pillanatkép.

Az alap blob társított bármely bérleteket nem befolyásolják a pillanatkép. Ön nem szerez érvényesül a Bérlés a pillanatkép.

A VHD-fájl az aktuális adatait és a Virtuálisgép-lemez állapotának tárolására szolgál. A virtuális Gépen belül a lemez leválasztása vagy állítsa le a virtuális gép, és majd pillanatképet a VHD-fájl. Használhatja a pillanatkép-fájlt később kérje le a VHD fájlt ezen a ponton az időben, és hozza létre újra a virtuális Gépet.

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása
Az alábbi példakód bemutatja, hogyan hozhat létre egy pillanatképet a [Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/). Ebben a példában létrehozásakor adja meg a további metaadatok elkészíteni a pillanatképet.

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

## <a name="copy-snapshots"></a>A pillanatképek másolása
Másolja a blobok és a pillanatfelvételeket kövesse ezeket a szabályokat érintő műveletek:

* Az alap BLOB pillanatkép másolhatja. A kiinduló BLOB helyére pillanatkép támogatásával, visszaállíthatja egy blob egy korábbi verzióját. A pillanatkép marad, de az alapszintű blob felülírja a pillanatkép írható másolatát.
* Egy forrásblobot, más néven pillanatkép másolhatja. Az eredményül kapott forrásblobot egy írható blob és a egy pillanatkép nem.
* Ha a forrásblob másolja, bármely a forrás blob pillanatképeinek nem másolódnak át a cél. Cél blob másolatot felülírja, ha az eredeti forrásblobot társított bármely pillanatképek változatlanok maradnak.
* A blokkblobok pillanatképet hoz létre, ha a blob véglegesített tiltólista is másolja a pillanatkép. Minden nem véglegesített blokkok nem másolódnak át.

## <a name="specify-an-access-condition"></a>Adja meg a hozzáférési állapot
Meghívásakor [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], megadhat egy hozzáférés egy feltétele, hogy a pillanatkép létrehozása csak akkor, ha egy feltétel teljesül. A hozzáférési állapot megadásához használja a [AccessCondition] [ dotnet_AccessCondition] paraméter. Ha a megadott feltétel nem teljesül, a pillanatkép nem jön létre, és a Blob service-állapotkódot adja vissza [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>A pillanatképek törlése
A pillanatképekkel rendelkező blobok nem törölhető, kivéve, ha a pillanatképeket is törlődik. Pillanatkép törlése egyenként, vagy adja meg, hogy az összes pillanatképet törölni kell a forrás blob törlése. Ha továbbra is pillanatképekkel rendelkező blobok törléséhez, egy hibát eredményez.

Az alábbi példakód bemutatja, hogyan törölni egy blobot, és annak pillanatképei a .NET-ben, ahol `blockBlob` típusú objektum [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Az Azure Premium Storage-pillanatképeket
A pillanatképek a Premium Storage használatakor a következő szabályok érvényesek:

* A premium storage-fiók lap blobonkénti pillanatképek maximális száma pedig a 100. Ha túllépi ezt a korlátot, a Blob pillanatkép műveletet adja vissza a 409 hibakód (`SnapshotCountExceeded`).
* Elvégezhető egy lapblob egy pillanatképet egy prémium szintű storage-fiókban 10 percenként egyszer. Ha adott aránya túllépi a határértéket, a Blob pillanatkép műveletet adja vissza a 409. Hibakód: (`SnapshotOperationRateExceeded`).
* Olvasható pillanatkép, használhatja a Blob másolásához művelet pillanatkép másolása egy másik lapblob a fiókban. A cél blob másolási művelet nem kell rendelkeznie minden olyan meglévő pillanatképeket. Ha a cél blob pillanatképeinek rendelkezik, akkor a Blob másolásához műveletet ad vissza, hibakód: 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Az abszolút URI-t térjen vissza egy pillanatképet
A C# példakód létrehoz egy pillanatképet, és írja meg az abszolút URI Azonosítónak a elsődleges helyhez.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Megismerheti, hogyan pillanatképek előfizetéstípusok
Pillanatkép, amely egy blobot egy csak olvasható példányát, létrehozás eredményezhet további adatok tárolási díjakat a fiókjához. Az alkalmazás tervezésekor fontos figyelembe venni ezeket előfordulhat, hogy díjszabásokkal úgy, hogy csökkentheti minimálisra a költségeket.

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok
Az alábbi lista tartalmazza az alapvető szempontokat figyelembe kell venni egy pillanatkép létrehozásához.

* A tárfiók költségeket terhel egyedi blokkok vagy lapok, attól a blob vagy a pillanatkép. A fiók nem jár további díjakat a társított blob, amíg nem frissíti a blob-alapú, amelyre pillanatképekkel. Miután frissítette az alap blob, a pillanatképek eltér. Ha ez történik, az egyedi blokkok vagy minden egyes blob vagy pillanatkép oldalak díjkötelesek.
* Ha lecseréli a blokkolás belül a blokkblobok, kódrészleten ezt követően egy egyedi blokként kell fizetnie. Ez igaz, akkor is, ha a blokk blokk ugyanazzal az Azonosítóval, és ugyanazokat az adatokat, mert a pillanatkép. A blokk véglegesítése után újra, bármilyen pillanatképet a párjukhoz eltér, és fizetni az adatokat. Ugyanez érvényes a lap a egy lapblob, amely azonos adatokkal frissül.
* Cserélje le a blokkblob meghívásával a [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], vagy [UploadFromByteArray] [ dotnet_UploadFromByteArray] metódus váltja fel a blob minden blokkokat. Ha egy adott blob társított pillanatkép, mostantól a kiindulási blob és a pillanatkép összes blokkokat tér el, és, mindkét blobok minden blokkokat kell fizetnie. Ez igaz, akkor is, ha a kiindulási blob és a pillanatkép adatainak azonos marad.
* Az Azure Blob szolgáltatás nem rendelkezik a módszerrel határozza meg, hogy a két blokk azonos adatokat tartalmaz. Minden feltöltött és véglegesített blokk egyedi, számít, még akkor is, ha ugyanazokat az adatokat és ugyanezzel az azonosítóval letiltása. Egyedi blokkolja a díjakat, mert fontos figyelembe venni, hogy frissítése egy blobot, amely rendelkezik egy pillanatkép további egyedi blokkok és további díjakat eredményez.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizálja a költségeket a pillanatkép-kezelése

Azt javasoljuk, körültekintően a többletköltségek elkerülése érdekében a pillanatképek kezelése. Követheti az ajánlott eljárások a pillanatképek tárolását felmerült költségek minimálisra csökkentése érdekében:

* Törölje és hozza létre újból a pillanatképek társított blob, amikor frissíti a blob, még akkor is, ha frissíti az azonos adatokat, kivéve, ha az alkalmazás-tervezés szükséges, hogy továbbra is a pillanatképek. Törlésével és ismételt létrehozása a blob-pillanatfelvételek, akkor biztosíthatja, hogy a blob és a pillanatképekhez tér el.
* Ha egy blob pillanatképének is fenntartják, elkerülheti a hívása [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], vagy [UploadFromByteArray] [ dotnet_UploadFromByteArray] frissíteni a blobot. Ezek a metódusok cserélje le az összes blob: az alap blob és a pillanatképek tér el jelentősen okoz a blokkok. Ehelyett a lehető legkevesebb blokkok használatával módosítsa a [PutBlock] [ dotnet_PutBlock] és [PutBlockList] [ dotnet_PutBlockList] módszereket.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép számlázási forgatókönyvek
Az alábbi forgatókönyvek bemutatják a blokkblobok és annak pillanatképei vonatkozó díjszabásokkal.

**1. forgatókönyv**

Az 1. forgatókönyv az alap blob nem lett frissítve után a pillanatkép, így csak az 1, 2 és 3 egyedi blokkok számítunk fel díjat.

![Az Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**2. forgatókönyv**

A 2. forgatókönyv az alap blobot frissítve lett, de még nem a pillanatképre. Blokk 3 frissítve lett, és annak ellenére, hogy ugyanazokat az adatokat, és ugyanazzal az Azonosítóval tartalmaz, nem ugyanaz, mint a pillanatkép 3 letiltása. Ennek eredményeképpen a fiók díjszabásának négy blokkokat.

![Az Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**3. forgatókönyv**

3. forgatókönyv az alap blobot frissítve lett, de még nem a pillanatképre. Blokk 3 váltotta fel az alap blob 4 blokkja, de a pillanatkép továbbra is tükrözi 3 letiltása. Ennek eredményeképpen a fiók díjszabásának négy blokkokat.

![Az Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**4. forgatókönyv**

4. forgatókönyv az alap blob teljesen frissítve lett, és tartalmazza az eredeti blokkok egyike. Ennek eredményeképpen a fiók összes nyolc egyedi blokkok díjszabásának. Ez akkor fordulhat elő, ha például egy frissítési módszer használ [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], vagy [UploadFromByteArray][dotnet_UploadFromByteArray], mert ezek a metódusok cserélje le az összes blob tartalmát.

![Az Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>További lépések

* További információ a virtuális gép (VM) pillanatképeit használata annak [biztonsági mentése az Azure nem felügyelt Virtuálisgép-lemezek növekményes pillanatképekkel](../../virtual-machines/windows/incremental-snapshots.md)

* Blob storage-dzsal további példákért lásd [Azure-Kódminták](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Töltse le a mintaalkalmazást, és futtathatja, vagy tallózással keresse meg a kódot a Githubon.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx
