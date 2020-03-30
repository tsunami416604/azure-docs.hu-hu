---
title: Az Azure-lapblobok áttekintése | Microsoft dokumentumok
description: Az Azure-lapblobok és azok előnyeinek áttekintése, beleértve a mintaparancsfájlokkal rendelkező használati eseteket is.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985618"
---
# <a name="overview-of-azure-page-blobs"></a>Az Azure-lapblobok áttekintése

Az Azure Storage háromféle blobstorage-tárolót kínál: blokkblobokat, hozzáfűző blobokat és lapblobokat. A blokkblobok blokkokból állnak, és ideálisak szöveg- vagy bináris fájlok tárolására, valamint nagy fájlok hatékony feltöltésére. A hozzáfűző blobok blokkokból is állnak, de hozzáfűzési műveletekre vannak optimalizálva, így ideálisak a naplózási forgatókönyvek hez. A lapblobok összesen legfeljebb 8 TB méretű, 512 bájtos lapokból állnak, és gyakori véletlenszerű olvasási/írási műveletekre vannak tervezve. A lapblobok az Azure IaaS-lemezek alapját képezik. Ez a cikk a lapblobok szolgáltatásainak és előnyeinek ismertetése.

A lapblobok 512 bájtos lapok gyűjteményei, amelyek lehetővé teszik tetszőleges bájttartományok olvasását/írását. Ezért a lapblobok ideálisak az indexalapú és ritka adatstruktúrák, például az operációs rendszer és az adatlemezek tárolására a virtuális gépek és adatbázisok számára. Az Azure SQL DB például lapblobokat használ az adatbázisok alapjául szolgáló állandó tárolóként. Ezenkívül a lapblobokat gyakran használják a tartományalapú frissítéseket tartalmazó fájlokhoz is.  

Az Azure-lapblobok főbb jellemzői a REST-felület, az alapul szolgáló tároló tartóssága és az Azure-ba való zökkenőmentes áttelepítési képességek. Ezeket a funkciókat a következő szakasz részletesebben tárgyalja. Emellett az Azure-lapblobok jelenleg két típusú tárterületen támogatottak: prémium szintű storage és standard storage. A prémium szintű storage-ot kifejezetten olyan számítási feladatokhoz tervezték, amelyek konzisztens nagy teljesítményt és alacsony késést igényelnek, így a prémium szintű lapblobok ideálisak a nagy teljesítményű tárolási forgatókönyvekhez. A standard szintű tárfiókok költséghatékonyabbak a késés-in-érzéketlen számítási feladatok futtatásához.

## <a name="sample-use-cases"></a>Használati esetek minta

Beszéljünk meg néhány használati esetet az Azure IaaS-lemezekkel kezdődő lapblobok esetében. Az Azure-lapblobok az Azure IaaS virtuális lemezplatformjának gerincét képezik. Az Azure operációs rendszer és az adatlemezek virtuális lemezekként vannak megvalósítva, ahol az adatok tartósan megőrződnek az Azure Storage platformon, majd a maximális teljesítmény érdekében a virtuális gépekre kerülnek. Az Azure Disks hyper-V [VHD formátumban](https://technet.microsoft.com/library/dd979539.aspx) megőrzött, és az Azure Storage-ban [lapblobként](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) tárolódik. Az Azure IaaS virtuális gépek virtuális lemezei mellett a lapblobok paas- és DBaaS-forgatókönyveket is engedélyeznek, például az Azure SQL DB-szolgáltatást, amely jelenleg lapblobokat használ az SQL-adatok tárolására, lehetővé téve az adatbázis gyors véletlenszerű olvasási és írási műveleteit. Egy másik példa az lenne, ha egy PaaS-szolgáltatás a megosztott média-hozzáférés az együttműködésen alapuló videoszerkesztő alkalmazások, lapblobok lehetővé teszik a gyors hozzáférést a véletlenszerű helyeken az adathordozón. Azt is lehetővé teszi a gyors és hatékony szerkesztése és egyesítése az azonos média több felhasználó. 

A külső Microsoft-szolgáltatások, például az Azure Site Recovery, az Azure Backup, valamint számos külső fejlesztő iparágvezető innovációkat valósítottak meg a lapblob REST felületének használatával. Az alábbiakban az Azure-ban megvalósított egyedi forgatókönyvek közül választhat: 

* Alkalmazás által irányított növekményes pillanatkép-kezelés: Az alkalmazások kihasználhatják a lapblob-pillanatképeket és a REST API-kat az alkalmazásellenőrzőpontok mentéséhez anélkül, hogy költséges adatátfedéseket kellene felhalmozniuk. Az Azure Storage támogatja a helyi pillanatképek a lapblobok, amelyek nem igényelnek másolása a teljes blob. Ezek a nyilvános pillanatkép API-k is lehetővé teszik a hozzáférés és a különbözetek pillanatképek közötti másolása.
* Az alkalmazás és az adatok élő áttelepítése a helyszíni felhőbe: Másolja a helyszíni adatokat, és a REST API-k használatával írjon közvetlenül egy Azure-lapblobba, miközben a helyszíni virtuális gép továbbra is fut. Miután a cél utolérte, gyorsan feladatátvétel az Azure virtuális gép az adatok használatával. Ily módon a virtuális gépek és a virtuális lemezek a helyszíni felhőbe minimális állásidő, mivel az adatáttelepítés történik a háttérben, miközben továbbra is használja a virtuális gépet, és a feladatátvételhez szükséges állásidő rövid lesz (percben).
* [SAS-alapú](../common/storage-sas-overview.md) megosztott hozzáférés, amely lehetővé teszi a forgatókönyvek, mint a több olvasóés egyírós támogatja az egyidejűség-szabályozás.

## <a name="page-blob-features"></a>Lapblobok funkciói

### <a name="rest-api"></a>REST API

A [lapblobok használatának fejlesztését](storage-dotnet-how-to-use-blobs.md)az alábbi dokumentumban ismerkedés imithet. Például tekintse meg, hogyan érheti el a lapblobokat a Storage Client Library for .NET használatával. 

Az alábbi ábra a fiók, a tárolók és a lapblobok közötti általános kapcsolatokat ismerteti.

![Képernyőkép a fiók, a tárolók és a lapblobok közötti kapcsolatokról](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Adott méretű üres lapblob létrehozása

Hozzon létre egy lapblobot, először hozzon létre egy **CloudBlobClient** objektumot, az alap URI-val a tárfiók blobtárának eléréséhez *(pbaccount* az 1. ábrán) a **StorageCredentialsAccountAndKey** objektummal együtt, ahogy az a következő példában látható. A példa ezután bemutatja egy **cloudblobcontainer** objektumra mutató hivatkozás létrehozását, majd a tároló (*testvhds*) létrehozását, ha még nem létezik. Ezután a **CloudBlobContainer** objektum használatával hozzon létre egy **cloudpageblob-objektumra** mutató hivatkozást a lapblob nevének (os4.vhd) megadásával. A lapblob létrehozásához hívja meg a [CloudPageBlob.Create metódust,](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)amely a blob létrehozásához megadott maximális méretet adja meg. A *blobSize* kell lennie egy több 512 bájt.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

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

Ha létrehozás után át szeretne méretezni egy lapblobot, használja az [Átméretezés](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) módszert. A kért méret nek 512 bájt többszörösének kell lennie.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Lapok írása lapblobba

Lapok írásához használja a [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) metódust.  Ez lehetővé teszi, hogy írjon egy egymást követő sor oldalak legfeljebb 4MBs. A beírt eltolásnak egy 512 bájtos határvonalon kell kezdődnie (kezdőHiba % 512 == 0), és egy 512-es határvonalon - 1 végződnie kell.  A következő példa kód bemutatja, hogyan hívható **writepages** egy blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Amint egy egymást követő lapkészlet írási kérelmet sikeresen a blob szolgáltatás, és replikálja a tartósság és a rugalmasság, az írási véglegesített, és a siker visszaadja az ügyfélnek.  

Az alábbi ábrán 2 különálló írási művelet látható:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Írási művelet 1024 bájt hosszúságú 0 eltolással kezdődően 
2.  Írási művelet 4096 eltolással kezdődően 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Oldalak olvasása lapblobból

Lapok olvasásához használja a [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) metódust a lapblob ból származó bájtok tartományának olvasásához. Ez lehetővé teszi a teljes blob vagy bájtok tartományának letöltését a blob bármely eltolásából kiindulva. Olvasáskor az eltolásnak nem kell az 512 többszörösén elindulnia. NUL-lap bájtjainak olvasásakor a szolgáltatás nulla bájtot ad vissza.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Az alábbi ábrán egy 256-os eltolású olvasási művelet látható, amelynek tartománymérete 4352. A visszaadott adatok narancssárga színnel vannak kiemelve. A nul-oldalak nulláit adja vissza a rendszer.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Ha ritkán lakott blobtal rendelkezik, célszerű csak letölteni az érvényes lapterületeket, hogy elkerülje a nulla bájtok kijutásának kifizetését és a letöltési késés csökkentését.  Az adatok által támogatott lapok meghatározásához használja a [CloudPageBlob.GetPageRanges parancsot.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges) Ezután felsorolhatja a visszaadott tartományokat, és letöltheti az egyes tartományok adatait. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Lapblob lelízingelése

A bérleti blob művelet létrehoz és kezeli a blob írási és törlési műveletek zárolását. Ez a művelet olyan esetekben hasznos, ahol egy lapblob több ügyfélről érhető el, így biztosítva, hogy egyszerre csak egy ügyfél írhat a blobba. Az Azure Disks például ezt a lízingmechanizmust használja annak biztosítására, hogy a lemezt csak egyetlen virtuális gép kezeli. A zár időtartama lehet 15-60 másodperc, vagy lehet végtelen. További részleteket a [dokumentációban](/rest/api/storageservices/lease-blob) talál.

A gazdag REST API-k mellett a lapblobok megosztott hozzáférést, tartósságot és fokozott biztonságot is biztosítanak. Ezeket az előnyöket a következő bekezdésekben részletesebben is kifogjuk fedni. 

### <a name="concurrent-access"></a>Párhuzamos hozzáférés

A lap blobok REST API és a lízing mechanizmus lehetővé teszi az alkalmazások számára, hogy a lap blob több ügyfélről. Tegyük fel például, hogy létre kell építenie egy elosztott felhőszolgáltatást, amely több felhasználóval osztja meg a tárolási objektumokat. Ez lehet egy webes alkalmazás szolgáló nagy gyűjteménye képek több felhasználó számára. Ennek megvalósításához az egyik lehetőség a csatlakoztatott lemezekkel rendelkező virtuális gép használata. Hátrányai közé tartozik, (i) a korlátozás, hogy a lemez csak akkor csatlakoztatható egyetlen virtuális gép, így korlátozza a méretezhetőséget, rugalmasságot és a növekvő kockázatokat. Ha probléma van a virtuális gép vagy a virtuális gép futó szolgáltatás, majd a bérlet miatt a rendszerkép nem érhető el, amíg a bérlet lejár, vagy megszakad; és (ii) Az IaaS virtuális gép birtoklásának többletköltsége. 

Egy másik lehetőség, hogy a lap blobok közvetlenül az Azure Storage REST API-kon keresztül. Ez a beállítás szükségtelenné teszi a költséges IaaS virtuális gépek, teljes rugalmasságot biztosít a közvetlen hozzáférés több ügyféltől, egyszerűsíti a klasszikus telepítési modell azáltal, hogy nem kell csatolni/leválasztani a lemezeket, és kiküszöböli a problémák kockázatát a virtuális gép. És ugyanolyan szintű teljesítményt nyújt a véletlenszerű olvasási/írási műveletekhez, mint a lemez

### <a name="durability-and-high-availability"></a>Tartósság és magas rendelkezésre állás

Mind a standard, mind a prémium szintű tárhely tartós tároló, ahol a lapblob-adatok mindig replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage redundanciájáról további információt ebben a [dokumentációban](../common/storage-redundancy.md)talál. Az Azure következetesen nagyvállalati szintű tartósságot biztosít az IaaS-lemezekhez és lapblobokhoz, az iparágvezető nulla százalékos [évesített hibaarányával.](https://en.wikipedia.org/wiki/Annualized_failure_rate)

### <a name="seamless-migration-to-azure"></a>Zökkenőmentes áttelepítés az Azure-ba

Azoknak az ügyfeleknek és fejlesztőknek, akik érdeklődnek a saját testreszabott biztonsági mentési megoldásuk megvalósítása iránt, az Azure növekményes pillanatképeket is kínál, amelyek csak a deltákat tartják. Ez a funkció elkerüli a kezdeti teljes másolat költségét, ami jelentősen csökkenti a biztonsági mentés költségét. A különbözeti adatok hatékony olvasása és másolása mellett ez egy másik hatékony képesség, amely még több újítást tesz lehetővé a fejlesztőktől, ami kategóriájában a legjobb biztonsági mentési és vész-helyreállítási (DR) élményt eredményez az Azure-ban. Beállíthatja a saját biztonsági mentési vagy DR-megoldás a virtuális gépek az Azure-ban a [Blob Snapshot](/rest/api/storageservices/snapshot-blob) a [Laptartományok begetése](/rest/api/storageservices/get-page-ranges) API és a [növekményes copy Blob](/rest/api/storageservices/incremental-copy-blob) API, amely segítségével könnyen másolhatja a növekményes adatok dr. 

Emellett számos vállalat kritikus számítási feladatok már fut a helyszíni adatközpontokban. A számítási feladatok felhőbe való migrálása esetén az egyik fő szempont az adatok másolásához szükséges állásidő, valamint az átállás utáni előre nem látható problémák kockázata. Sok esetben az állásidő lehet egy showstopper a felhőbe való áttelepítés. A LAP blobok REST API használatával az Azure úgy oldja meg ezt a problémát, hogy a kritikus fontosságú számítási feladatok minimális megszakításával engedélyezi a felhőmigrálást. 

Példákat, hogyan készíthet egy pillanatképet, és hogyan lehet visszaállítani egy lapblob egy pillanatképből, tekintse meg a [telepítő egy biztonsági mentési folyamat növekményes pillanatképek](../../virtual-machines/windows/incremental-snapshots.md) cikk használatával.
