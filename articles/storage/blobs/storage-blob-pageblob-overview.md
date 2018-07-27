---
title: Egyedi jellemzőinek oldala az Azure-blobok |} A Microsoft Docs
description: Oldala az Azure-blobok és azok részesülnek – beleértve a használati esetek-mintaszkriptek áttekintése.
services: storage
author: anasouma
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: wielriac
ms.component: blobs
ms.openlocfilehash: a215771b0126e9048b7d9da4ed1d6073c8e960a4
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265900"
---
# <a name="unique-features-of-azure-page-blobs"></a>Egyedi jellemzőinek oldala az Azure-blobok

Az Azure Storage a blob storage háromféle biztosít: Blokkblobokat, hozzáfűző Blobok és lapblobok. A blokkblobok blokkokból állnak, és ideális megoldást szöveg vagy bináris fájlok tárolásához, valamint hatékonyan nagy fájlok feltöltése. Hozzáfűző blobokat is épülnek fel blokkok alkotják, de vannak optimalizálva fűzze hozzá a művelet, így ideális megoldás a naplózási forgatókönyvekben. Lapblobok épülnek fel az 512 bájtos oldalak és 8 TB a teljes méret és a gyakori véletlenszerű olvasási és írási műveletek tervezve. A lapblobok olyan Azure IaaS-lemezek alapját. Ez a cikk a funkciók és előnyök, a lapblobok elmagyarázza összpontosít.

A lapblobok 512 bájtos lapok, amely lehetővé teszi, hogy olvasási/írási bájt tetszőleges címtartományok gyűjteményei. Ezért lapblobok ideális megoldást jelentenek a virtuális gépek és adatbázisok index és a ritka adatstruktúrák például az operációs rendszer és az adatlemezek tárolása. Például Azure SQL DB használja a lapblobok az alapul szolgáló állandó tárolóként az adatbázisok számára. Ezenkívül lapblobok is gyakran használják tartományalapú frissítésekkel rendelkező fájlokat.  

Azure lapblobok fő funkciói a következők: a REST-felület, a tartósságra az alapul szolgáló tárolóról, és a zökkenőmentes áttelepítési képességek az Azure-bA. Ezek a szolgáltatások további részleteket a következő szakasz tárgyalja. Emellett oldala az Azure-blobok jelenleg támogatott a két tárolási típust kínál: Premium Storage- és Standard tárterület. A Premium Storage kifejezetten konzisztens nagy teljesítményű és kis késésű, így a prémium szintű lapblobok ideális a nagy teljesítményű adatokat tároló adatbázis igénylő számítási feladatokhoz készült.  Standard szintű Storage jelenleg költséghatékonyabb késleltetést toleráló számítási feladatok futtatásához.

## <a name="sample-use-cases"></a>Példa használati esetek

Vizsgáljuk meg néhány használati esetek kezdve az Azure IaaS-lemezek lapblobok esetében. Azure lapblobok az Azure IaaS virtuális lemezek platform gerincét. Az Azure-OS és az adatlemezeket is az adatokat, az Azure Storage-platformon tartósan tárolja és kézbesítését a virtuális gépekhez, a maximális teljesítmény virtuális lemezek vannak megvalósítva. Az Azure Disks megmaradnak a Hyper-V [VHD formátumú](https://technet.microsoft.com/library/dd979539.aspx) tárolt, és a egy [lapblob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) az Azure Storage-ban. Virtuális lemezek az Azure IaaS virtuális gépek használatán, a lapblobok PaaS és DBaaS forgatókönyvek, például az Azure SQL Database szolgáltatást, amely a jelenleg használt lapblobok az adatbázis a gyors, véletlenszerű olvasási és írási műveletek engedélyezése az SQL-adatok tárolására szolgáló is engedélyezheti. Egy másik példa lenne, ha egy PaaS-szolgáltatás megosztott media Access for videó szerkesztési alkalmazások által biztosított együttműködési környezettel rendelkezik, a lapblobok véletlenszerű helyeken és az adathordozó gyors hozzáférés engedélyezése. Azt is lehetővé teszi, hogy gyors és hatékony szerkesztését, és a több felhasználó által ugyanazon adathordozó egyesítését. 

Belső Microsoft-szolgáltatások, az Azure Site Recovery, Azure biztonsági mentés, valamint számos külső fejlesztők valósította olyan piacvezető innovációkat lapblob a REST-felület használatával. Az alábbiakban néhány, az Azure-ban megvalósított egyedi forgatókönyvekről: 
* Alkalmazás által vezérelt növekményes pillanatkép-kezelő: alkalmazások kihasználhatják a blobpillanatképeket oldal és a REST API-k anélkül költséges másolása az adatok az alkalmazás ellenőrzőpontokat mentéséhez. Az Azure Storage támogatja a helyi pillanatképek a lapblobokhoz, amelyek nem igényelnek, a teljes blob másolása. Ezek nyilvános pillanatkép API-k is engedélyezheti elérése és -pillanatképek közötti eltérések szinte azonnali másolását.
* Élő áttelepítés alkalmazás és a helyszíni adatokat a felhőbe: a helyszíni adatok másolása és a REST API-k használata közben a helyszíni virtuális gép továbbra is fut a közvetlenül egy oldala az Azure-blobba való írásához. Ha a cél rendelkezik szerepelnek, is gyorsan használatával az adatokat az Azure virtuális géphez feladatátvétel. Ezzel a módszerrel a virtuális gépeket telepíthet át, és virtuális lemezek helyszíni tárolóból felhőbe minimális állásidővel, mivel az adatok migrálása a háttérben történik, miközben továbbra is használhatja a virtuális gép és a feladatátvételhez szükséges állásidő (percben) rövid lesz.
* [SAS-alapú](../common/storage-dotnet-shared-access-signature-part-1.md) megosztott hozzáférés, amely lehetővé teszi a több-olvasók és a egy-író egyidejűség-vezérlés támogatása.

## <a name="page-blob-features"></a>Lapblobok funkciói

### <a name="rest-api"></a>REST API
Első lépések az alábbi dokumentumában [fejlődő lapblobok használatával](storage-dotnet-how-to-use-blobs.md). Tegyük fel tekintse meg a .NET-keretrendszerhez készült Storage ügyféloldali kódtár használatával lapblobok elérése. 

A következő ábra a partner, a tárolók és a lapblobokat általános kapcsolatai ismerteti.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Egy megadott méretű üres lapblob létrehozása
Lapblob, hogy először hozzon létre egy **CloudBlobClient** , a blob storage, a tárfiók eléréséhez használt alap URI-azonosítójú objektum (*pbaccount* az 1. ábra) együtt a  **StorageCredentialsAccountAndKey** objektumot, az alábbi példában látható módon. A példa megjeleníti a hivatkozás létrehozása egy **CloudBlobContainer** objektumot, és ezután hozza létre a tárolót (*testvhds*) Ha még nem létezik. Majd használja a **CloudBlobContainer** objektumazonosító, hozzon létre egy hivatkozást egy **CloudPageBlob** objektum eléréséhez az oldal a blob nevét (os4.vhd) megadásával. A lapblob létrehozásához hívja [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) ad át a maximális mérete a létrehozni kívánt blob. A *blobSize* 512 bájt többszöröse lehet.

```csharp
using Microsoft.WindowsAzure.StorageClient;
long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Lapblob átméretezése
Méretezze át egy lapblob létrehozása után, használja a [átméretezése](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) API-t. A kért méret 512 bájt többszöröse lehet.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Lapok, egy lapblob írása
Írási oldalak, használja a [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx) metódust.  Ez lehetővé teszi, hogy legfeljebb 4MBs szekvenciális meg írni. Az eltolás ír egy 512 bájtos határhoz kell kezdődniük (startingOffset % 512 == 0), és a egy 512 határon – 1 teljes.  Az alábbi példakód bemutatja, hogyan hívhat meg **WritePages** egy BLOB:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Amint egy írási kérelmek oldalak egymást követő halmazának sikeres lesz, a blob service-ben, és a tartósság és a rugalmasság a rendszer replikálja, az írási véglegesítése megtörtént, és sikeres visszahozná az ügyfélhez.  

Az alábbi ábrán látható külön 2 írási műveletek:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Kezdőár írási művelet eltolás: 0 hosszúsága 1024 bájt 
2.  Kezdőár írási művelet 1024 hosszának 4096 eltolása 

#### <a name="reading-pages-from-a-page-blob"></a>Lapblob lapok olvasása
Lapok olvasása, használja a [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) bájttartomány olvasni a lapblob metódust. Ez lehetővé teszi, hogy töltse le a teljes blob vagy bájttartomány kezdve minden olyan eltolás a blobban. Olvasásakor, az eltolás nem rendelkezik az 512 többszöröse elindításához. Olvasott bájtok NUL oldal, ha a szolgáltatás nulla bájtot ad vissza.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
A következő ábrán látható 256 és a 4352 rangeSize BlobOffSet egy olvasási műveletet. Visszaadott adatok narancssárga van kiemelve. Nullák NUL oldalakat adja vissza

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Ha egy ritkásan feltöltött blobot, érdemes csak töltse le a érvényes régiók elkerülje 0 bájt egressing és letöltési késés csökkentése érdekében.  Annak megállapításához, hogy mely lapok élvezik adatokat, használjon [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Ezután a visszaadott tartományok enumerálásakor, és töltse le az adatokat az összes tartományt. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Bérlési egy lapblob
A Blobbérlet műveletet hoz létre, és egy BLOB egy zár kezeli az írási és törlési műveletek. Ez a művelet egy lapblob annak érdekében, hogy csak egy ügyfél egyszerre írhat a blob több ügyfél hozzáférnek ahol forgatókönyvekben hasznos. Azure-os lemezekre, például használja a bérlési mechanizmus biztosítására, hogy a lemez csak egyetlen virtuális gép kezeli. A Zárolás időtartama 15 és 60 másodperc is lehet, vagy lehet végtelen. A dokumentációt [Itt](/rest/api/storageservices/lease-blob) további részletekért.

> A következő hivatkozás használatával [Kódminták](/resources/samples/?service=storage&term=blob&sort=0 ) sok más alkalmazás-forgatókönyvek esetében. 

Mellett részletes REST API-k a lapblobok azt is megadhatja, közös hozzáférésű, tartósság és fokozott biztonságot. Ezek az előnyök részletesebben a következő bekezdésekben erről. 

### <a name="concurrent-access"></a>Egyidejű hozzáférés
A lapblobok REST API-t, és a bérlési mechanizmus lehetővé teszi az alkalmazások a lapblob érhetnek el több ügyfelet. Például tegyük fel, hogy egy elosztott felhőszolgáltatás, amely több felhasználó és a tárolási objektum közös kell létrehoznia. Egy webalkalmazás, a képek gyűjteménye szolgáltató több felhasználó lehet. Ennek megvalósításához az egyik lehetőség, hogy csatlakoztatott adatlemezekkel rendelkező virtuális Gépet használ. Több szempontból sem ajánlott a következők lehetnek az (i) a korlátozást, hogy a lemez így korlátozhatja a skálázhatóságot, rugalmasságot és kockázatok növelése egyetlen virtuális gép csak csatolható. Ha a probléma a virtuális gép vagy a szolgáltatás a virtuális gépen, majd a bérlet miatt a lemezkép nem érhető el addig, amíg a bérlet jár, vagy sérült; és (ii), hogy egy IaaS-beli virtuális gép további költség. 

Egy másik lehetőség, hogy közvetlenül az Azure Storage REST API-kon keresztül, a lapblobokat használnak. Ezt a beállítást kiküszöböli a költséges IaaS virtuális gépek, több ügyfél közvetlen hozzáférés teljes rugalmasságot kínál, a klasszikus üzemi modell leegyszerűsíti, így nem kell lemezek csatolása/leválasztása és megszünteti a virtuális gép problémák kockázatát. És a véletlenszerű olvasási és írási műveletek teljesítményének lemezként azonos szintű biztosít

### <a name="durability-and-high-availability"></a>Tartósság és magas rendelkezésre állás
Standard és prémium szintű storage olyan tartós tárolási, ahol a lap Blobadatok mindig replikáljuk, tartósság és magas rendelkezésre állás. Az Azure-tárhely-redundancia kapcsolatos további információkért lásd a [dokumentáció](../common/storage-redundancy.md). Az Azure rendelkezik következetesen szállított nagyvállalati szintű tartósságot IaaS-lemezek és lapblobok, az iparágvezető nulla % [érvényes évesített Hibaarány](https://en.wikipedia.org/wiki/Annualized_failure_rate). Azt jelenti az Azure soha nem megszakadt a felhasználói oldalon blob adatokat. 

### <a name="seamless-migration-to-azure"></a>Zökkenőmentes áttelepítés az Azure-bA
Az ügyfelek és a fejlesztőknek szól, akik a saját testre szabott biztonsági mentési megoldás megvalósítása az Azure-ban is elérhető növekményes pillanatképek csak tartsa meg az eltéréseket. Ez a funkció a kezdeti teljes másolat, ami jelentősen csökkenti a biztonsági mentési költségeinek elkerülhető. Hatékony olvasási és másolási különbözeti adatokat képes, és ez az egy másik hatékony képesség, amely lehetővé teszi a fejlesztők, vezető kategóriaelső a biztonsági mentési és vész-helyreállítási tapasztalattal az Azure-ban még több innovációkat. Beállíthatja a saját biztonsági mentési vagy a Vészhelyreállítási megoldást az Azure-beli virtuális gépek [Blob-pillanatkép](/rest/api/storageservices/snapshot-blob) együtt a [laptartomány-beolvasási](/rest/api/storageservices/get-page-ranges) API és a [növekményes másolás Blob](/rest/api/storageservices/incremental-copy-blob) API-t, amelyet használja a növekményes adatok könnyen másolni a vészhelyreállítás. 

Ezen kívül sok vállalat rendelkezik a helyszíni adatközpontokban már futó kritikus fontosságú számítási feladatokhoz. A számítási feladatok a felhőbe való migrálás, az egyik fő fontos szempont a váltás után az adatok és az előre nem látható problémák kockázatát másolása szükséges állásidő lenne. Sok esetben az állásidő lehet egy showstopper a felhőbe való migrálásra. REST API használatával az oldal blobok, -címek a probléma az Azure felhőbe a kritikus fontosságú munkaterhelésekhez minimális megszakítás való migrálást engedélyezésével. 

Pillanatkép készítése és a egy lapblob egy pillanatkép visszaállítása példákért tekintse meg a [beállítása a biztonsági mentési folyamat növekményes pillanatképekkel](../../virtual-machines/windows/incremental-snapshots.md) cikk.
