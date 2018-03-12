---
title: "Csak olvasható pillanatkép létrehozása a blob az Azure Storage |} Microsoft Docs"
description: "Útmutató: a pillanatkép létrehozása a blob adatainak biztonsági mentése blob idő az adott pillanatban. Ismerje meg, hogyan számlázása a pillanatképek és a használatukat kapacitás költségek minimalizálása érdekében."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 1a27dfd61850d9dfa1f232eacf7f09d66202cafe
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-blob-snapshot"></a>Blob-pillanatkép létrehozása

Pillanatkép egy blobot, amely egy adott időben csak olvasható verziója telepítve. A pillanatképek biztonsági mentési bináris objektumok hasznosak. Pillanatkép létrehozása után olvassa el, másolja, vagy törölje azt, de nem módosíthatja.

Egy blob pillanatképe megegyezik az alap blob, azzal a különbséggel, hogy a blob URI egy **DateTime** érték hozzáfűzi a blob URI-t, amellyel során létrehozott pillanatképen időpontját jelzi. Például ha egy lap blob URI van `http://storagesample.core.blob.windows.net/mydrives/myvhd`, a pillanatkép URI hasonlít `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Minden pillanatkép megosztása a base blob URI. A kiinduló blob és a pillanatkép csak megkülönböztetése a hozzáfűzött **DateTime** érték.
>

Blob állhat tetszőleges számú pillanatkép. A pillanatképek továbbra is fennáll, addig, amíg explicit módon törlődnek. A pillanatkép nem outlive az alap blob. A pillanatképek nyomon követéséhez a jelenlegi pillanatképek alap blob társított enumerálása.

Amikor létrehoz egy blob pillanatképet, a blob tulajdonságai ugyanazokat az értékeket a pillanatkép lesz másolva. A kiinduló blob metaadatait is másolja a pillanatképet, ha nem ad meg, a pillanatkép külön metaadatok létrehozásakor.

Az alap blob társított bármely címbérleteket nem befolyásolják a pillanatkép. Nem olvasható be a pillanatkép bérleti idejét.

A VHD-fájl az aktuális adatait és a Virtuálisgép-lemez állapota tárolására szolgál. Válassza le a lemezt a virtuális Gépen belül, vagy állítsa le a virtuális gép, és majd pillanatkép készítése a VHD-fájlt. A pillanatkép-fájlt később használhatja kérhető le a VHD-fájl ezen a ponton az időben, és hozza létre újra a virtuális Gépet.

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása
Az alábbi példakód bemutatja, hogyan hozzon létre egy pillanatképet a [Azure Storage ügyféloldali kódtára a .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Ebben a példában adja meg a pillanatkép metaadatokat létrehozásakor.

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
Blobok és a pillanatképek másolási műveletek kövesse ezeket a szabályokat:

* Az alap blob keresztül pillanatkép másolhatja. A kiinduló blob pozícióját az aktuális támogatásával, egy blobot egy korábbi verzióját is helyreállíthatja. A pillanatkép marad, de a következő blob felülírja a pillanatkép írható másolatát.
* Egy forrásblobot más néven pillanatkép másolhatja. Az eredményül kapott cél blob írható blob és a pillanatkép nem.
* Forrás blob másolását követően a forrás BLOB meglévő pillanatképeket a rendszer nem másolja a cél. Cél blob másolatot felülírja, ha a meglévő pillanatképeket, az eredeti cél blob társított változatlanok maradnak.
* Amikor egy blokkblob pillanatképet hoz létre, a blob véglegesített tiltólista is másolja a pillanatkép. A nem véglegesített téglalapok nem kerülnek.

## <a name="specify-an-access-condition"></a>Adjon meg egy hozzáférési állapot
A hívás esetén [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], megadhatja a hozzáférés egy feltétele, hogy a pillanatkép létrehozása csak akkor, ha egy feltétel teljesül. A hozzáférési állapot megadásához használja a [AccessCondition] [ dotnet_AccessCondition] paraméter. Ha a megadott feltétel nem teljesül, a pillanatkép nem jön létre, és a Blob szolgáltatás adja vissza. állapotkód: [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Törölje a pillanatképeket
Egy blobot a pillanatképek nem törölhető, kivéve, ha a pillanatképek is törlődnek. Pillanatkép törlése egyenként, vagy adja meg, hogy minden pillanatképet törlődik a forrás blob törlődik. Ha a blob, amely még a pillanatképeket törölni próbál, hibát okoz.

Az alábbi példakód bemutatja, hogyan törli a blob és a pillanatképeket a .NET, ahol `blockBlob` típusú objektum [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Prémium szintű Azure Storage pillanatképei
Prémium szintű Storage, pillanatképek használatakor a következő szabályok érvényesek:

* A maximális számú pillanatképpel oldalakra vonatkozó blob egy prémium szintű tárfiók / 100. Ha túllépi ezt a határértéket, a pillanatkép-Blob visszaadja hibakód 409 (`SnapshotCountExceeded`).
* Akkor is készítsen pillanatképet oldalakra vonatkozó blob egy prémium szintű tárfiók a 10 percenként egyszer. Ha adott aránya túllépi a határértéket, a pillanatkép-Blob művelet adja vissza hibakód 409 (`SnapshotOperationRateExceeded`).
* Olvassa el a pillanatképet, használhatja a Blob másolási művelet pillanatkép átmásolása a fiók egy másik oldalakra vonatkozó blob. A másolási művelet a rendeltetési blob nem lehet a meglévő pillanatképeket. Ha a cél blob pillanatképekkel rendelkezik, akkor a Blob másolási művelet hibakódot 409 adja vissza (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Térjen vissza a abszolút URI pillanatkép
A C# Kódpélda pillanatképet hoz létre, és írja meg az elsődleges hely abszolút URI.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Hogyan pillanatképek keletkeznek költségek ismertetése
Egy pillanatképet, amely egy blob csak olvasható másolatát, létrehozása eredményezhet további adatok tárolási költségek a fiókjához. Az alkalmazás megtervezéséhez fontos tudni, hogyan lehet, hogy keletkeznek ezeket a díjakat, így minimalizálhatja a költségeket.

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok
Az alábbi lista tartalmazza a legfontosabb megfontolandó a pillanatkép létrehozásához.

* A tárfiók terhel egyedi blokkok vagy lapok, hogy vannak-e a blob vagy a pillanatképet. A fiók nem többletköltségei a pillanatképek társított blob, amíg nem frissíti a blob, amelyeken alapulnak. Miután frissítette az alap blob, a pillanatképek eltér. Ez akkor fordul elő, ha van szó, az egyedi blokkok vagy lapok minden egyes blob vagy a pillanatképet.
* A blokkolás a blokkblob belül cseréjekor, amelyek blokkolják ezt követően és egyedi blokk számítjuk fel. Ez igaz, akkor is, ha a blokk blokk azonos Azonosítóval és ugyanazokat az adatokat, mert a pillanatkép. A blokk véglegesítése után újra, ez eltér a párjukhoz bármely pillanatfelvétel, és a saját adataikhoz fizetnie kell. Ugyanez érvényes a lap az oldalakra vonatkozó blob, amely azonos adatokkal rendelkező frissül.
* A mag cseréje a blokkblob meghívásával a [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], vagy [UploadFromByteArray] [ dotnet_UploadFromByteArray] metódus található blokkok váltja fel. Ha, hogy a blob társított pillanatkép, az alap blob és a pillanatkép blokkok most tér el, és mindkét blobok blokkokról sávszélességért fizetnie kell. Ez igaz, akkor is, ha az alap blob és a pillanatkép adatainak azonos maradnak.
* Az Azure Blob szolgáltatás nem rendelkezik segítségével határozza meg, hogy a két blokk azonos adatokat tartalmaz. Minden feltöltött és véglegesített blokk rendszer egyedi, még akkor is, ha ugyanazokat az adatokat, és a blokk megegyezik. Díjak egyedi blokk keletkeznek, mert fontos figyelembe venni, hogy egy blobot, amely rendelkezik egy pillanatkép eredmények további egyedi blokkok és további díjakat frissítése.

### <a name="minimize-cost-with-snapshot-management"></a>Pillanatkép-kezeléssel költségek minimalizálása érdekében

Azt javasoljuk, alaposan további költségek elkerülése érdekében a pillanatképek kezeléséhez. Az alábbi gyakorlati tanácsok a felmerült a pillanatképek tárolási költségek csökkentése érdekében kövesse:

* Törölje, és hozza létre a pillanatképek társított blob amikor frissíti a blob, még akkor is, ha frissíti azonos adatokkal, kivéve, ha az alkalmazás tervhez szükséges, hogy a pillanatképek karbantartása. Törlésével és ismételt létrehozása a blob-pillanatképekkel, akkor biztosíthatja, hogy a blob és a pillanatfelvételeket tér el.
* Ha a blob-pillanatképek fenntartásának ne hívása [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], vagy [UploadFromByteArray] [ dotnet_UploadFromByteArray] a blob frissítése. Ezek a módszerek a BLOB, amely az alapszintű blob és a pillanatképek tér el jelentősen blokkok cseréjét. Ehelyett frissítése a lehető legkevesebb blokkok használatával a [PutBlock] [ dotnet_PutBlock] és [PutBlockList] [ dotnet_PutBlockList] módszerek.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép számlázási forgatókönyvek
A következő esetekben bemutatják, hogyan költségek letiltása blob és a pillanatképek keletkeznek.

**1. forgatókönyv**

Az 1. forgatókönyv az alap blob nem lett frissítve után során létrehozott pillanatképen, így díjak sem merülnek fel, csak az egyedi blokkok 1, 2 és 3.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**2. forgatókönyv**

A 2. forgatókönyv az alap blob frissítve lett, de nem rendelkezik a pillanatkép. Blokk 3 frissítették, és annak ellenére, hogy ugyanazokat az adatokat, és ugyanazzal az Azonosítóval tartalmaz, nincs megegyezik a pillanatkép 3 letiltása. Ennek eredményeképpen a fiók négy blokk számítjuk fel.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**3. forgatókönyv**

3. forgatókönyv az alap blob frissítve lett, de nem rendelkezik a pillanatkép. Blokk 3 váltotta fel a kiinduló blob 4 blokkja, de a pillanatkép továbbra is tükrözi blokk 3. Ennek eredményeképpen a fiók négy blokk számítjuk fel.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**4. forgatókönyv**

A 4. forgatókönyv az alap blob teljesen frissült, ezért az eredeti blokkok egyike sem tartalmazza. Ennek eredményeképpen a fiók összes nyolc egyedi blokk számítjuk fel. Ez akkor fordulhat elő, ha egy frissítési módszerét használja, mint [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], vagy [UploadFromByteArray][dotnet_UploadFromByteArray], mert ezek a módszerek cseréjét a blob tartalmát.

![Azure Storage-erőforrások](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>További lépések

* További információ a virtuális gép (VM) lemez pillanatképek használata található [készítsen biztonsági másolatot az Azure nem felügyelt méretű lemezek növekményes pillanatképek](../../virtual-machines/windows/incremental-snapshots.md)

* Kód Blob storage használatával, tekintse meg a [Azure mintakódok](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Töltse le a mintaalkalmazást, majd futtassa, vagy keresse meg a kódot a Githubon.

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