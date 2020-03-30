---
title: Erőforrásfájlok létrehozása és használata – Azure Batch
description: Ismerje meg, hogyan hozhat létre Batch erőforrásfájlokat különböző bemeneti forrásokból. Ez a cikk néhány gyakori módszert tartalmaz a virtuális gépek létrehozásához és helytasztásához.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531141"
---
# <a name="creating-and-using-resource-files"></a>Erőforrásfájlok létrehozása és használata

Az Azure Batch-feladat feldolgozása gyakran valamilyen adatfeldolgozási. Erőforrás-fájlok a módja annak, hogy ezeket az adatokat a Batch virtuális gép (VM) egy feladaton keresztül. Minden típusú feladat támogatja az erőforrásfájlokat: tevékenységek, kezdési feladatok, feladat-előkészítési feladatok, feladatkiadási feladatok stb. Ez a cikk néhány gyakori módszert tartalmaz az erőforrásfájlok létrehozásához és a virtuális gépre való helytázásához.  

Az erőforrásfájlok adatokat helyeznek el a batch-ben lévő virtuális gépre, de az adatok típusa és felhasználása rugalmas. Vannak azonban néhány gyakori használati esetek:

1. Közös fájlok kiépítése minden virtuális gépen erőforrásfájlok használatával egy indítási feladaton
1. Feladatok által feldolgozandó bemeneti adatok biztosítása

A gyakori fájlok lehetnek például a feladatok futtatásához használt alkalmazások telepítéséhez használt indítási feladat fájljai. A bemeneti adatok lehetnek nyers kép- vagy videoadatok, vagy a Batch által feldolgozandó információk.

## <a name="types-of-resource-files"></a>Az erőforrásfájlok típusai

Az erőforrásfájlok létrehozásához több különböző lehetőség áll rendelkezésre. Az erőforrásfájlok létrehozási folyamata az eredeti adatok tárolási helyétől függően változik.

Erőforrásfájl létrehozásának lehetőségei:

- [Tároló URL-címe:](#storage-container-url)Erőforrásfájlt hoz létre az Azure bármely tárolótárolójából
- [Tároló neve:](#storage-container-name)Erőforrás-fájlt hoz létre egy tároló nevéből egy Batch-hez kapcsolódó Azure-tárfiókban
- [Webvégpont](#web-endpoint): Erőforrásfájlt hoz létre bármely érvényes HTTP-URL-címből

### <a name="storage-container-url"></a>Tároló URL-címe

A tároló URL-címének használatával a megfelelő engedélyekkel hozzáférhet az Azure bármely tárolójában lévő fájlokhoz. 

Ebben a C# példában a fájlok már feltöltve egy Azure storage blob storage-ként egy Azure storage-ba. Az erőforrásfájl létrehozásához szükséges adatok eléréséhez először hozzá kell férnünk a tárolótárolóhoz.

Hozzon létre egy közös hozzáférésű aláírás (SAS) URI-t a tárolótároló eléréséhez megfelelő engedélyekkel. Állítsa be a SAS lejárati idejét és engedélyeit. Ebben az esetben nincs megadva kezdési időpont, így a SAS azonnal érvényessé válik, és két órával a létrehozása után lejár.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> A tároló-hozzáféréshez `Read` mindkettővel `List` és engedélyekkel kell rendelkeznie, `Read` míg a blob-hozzáféréshez csak engedélyszükséges.

Az engedélyek konfigurálása után hozza létre a SAS-jogkivonatot, és formázza a SAS URL-címét a tárolótárolóhoz való hozzáféréshez. A tároló formázott SAS-URL-címének használatával [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)hozzon létre egy erőforrásfájlt a használatával.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

A SAS-URL-cím létrehozásának alternatívája a névtelen, nyilvános olvasási hozzáférés engedélyezése egy tárolóhoz és annak blobjaihoz az Azure Blob storage-ban. Ezzel írásvédett hozzáférést biztosíthat ezekhez az erőforrásokhoz a fiókkulcs megosztása és SAS megkövetelése nélkül. A nyilvános olvasási hozzáférés általában olyan esetekben használatos, ahol bizonyos blobok mindig elérhetők legyenek a névtelen olvasási hozzáféréshez. Ha ez a forgatókönyv megfelel a megoldásnak, tekintse meg a névtelen hozzáférést a [blobok](../storage/blobs/storage-manage-access-to-resources.md) cikket a blob adatokhoz való hozzáférés kezeléséről.

### <a name="storage-container-name"></a>Tároló tárolójának neve

SAS-URL-cím konfigurálása és létrehozása helyett használhatja az Azure storage-tároló nevét a blob adatok eléréséhez. A használt tárolónak a Batch-fiókhoz kapcsolódó Azure storage-fiókban kell lennie. Ezt a tárfiókot automatikus tárfióknak nevezzük. Az automatikus tárolási tároló használatával megkerülheti a SAS-URL-cím konfigurálását és létrehozását a tárolótároló eléréséhez.

Ebben a példában feltételezzük, hogy az erőforrásfájl létrehozásához használt adatok már a Batch-fiókhoz kapcsolódó Azure Storage-fiókban vannak. Ha nem rendelkezik automatikus tárolási fiókkal, tekintse meg a [Kötegfiók létrehozása](batch-account-create-portal.md) című témakör lépéseit a fiók létrehozásáról és csatolásáról.

Egy csatolt tárfiók használatával nem kell létrehoznia és konfigurálnia egy SAS URL-címet egy tárolótárolóhoz. Ehelyett adja meg a tároló a kapcsolódó tárfiókban a tároló nevét.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webes végpont

Az Azure Storage-ba nem feltöltött adatok továbbra is felhasználhatók erőforrásfájlok létrehozásához. A bemeneti adatokat tartalmazó érvényes HTTP-URL-címet megadhat. Az URL-cím a Batch API-hoz, majd az adatok at egy erőforrásfájl létrehozásához használja.

A következő C# példában a bemeneti adatok egy fiktív GitHub-végponton található. Az API lekéri a fájlt az érvényes webes végpontról, és létrehoz egy erőforrásfájlt, amelyet a feladat használ fel. Ehhez a forgatókönyvhöz nincs szükség hitelesítő adatokra.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

Minden Egyes Azure Batch-feladat másként használja a fájlokat, ezért a Batch rendelkezik a feladatok fájljainak kezelésére rendelkezésre álló lehetőségekkel. A következő forgatókönyvek nem célja, hogy átfogó, hanem néhány gyakori helyzetek és ajánlásokat.

### <a name="many-resource-files"></a>Számos erőforrásfájl

A kötegelt feldolgozás több olyan feladatot is tartalmazhat, amelyek mindegyike ugyanazokat a gyakori fájlokat használja. Ha a gyakori feladatfájlok sok feladat között vannak megosztva, jobb megoldás lehet, ha az alkalmazáscsomagot használja a fájlok tárolására az erőforrásfájlok használata helyett. Az alkalmazáscsomagok optimalizálást biztosítanak a letöltési sebességhez. Emellett az alkalmazáscsomagokban lévő adatok a feladatok között gyorsítótárazva vannak, így ha a feladatfájlok nem változnak gyakran, az alkalmazáscsomagok megfelelőek lehetnek a megoldáshoz. Az alkalmazáscsomagok segítségével nem kell manuálisan kezelnie több erőforrásfájlt, vagy SAS URL-címeket létrehoznia az Azure Storage-ban lévő fájlok eléréséhez. Batch működik a háttérben az Azure Storage tárolja és telepítse az alkalmazáscsomagok számítási csomópontok.

Ha minden feladatban sok fájl található, az erőforrásfájlok a legjobb megoldás, mivel az egyedi fájlokat használó feladatokat gyakran frissíteni vagy cserélni kell, ami nem olyan egyszerű az alkalmazáscsomagok tartalmával. Az erőforrásfájlok további rugalmasságot biztosítanak az egyes fájlok frissítéséhez, hozzáadásához vagy szerkesztéséhez.

### <a name="number-of-resource-files-per-task"></a>Erőforrásfájlok száma feladatonként

Ha egy tevékenységen több száz erőforrásfájl van megadva, előfordulhat, hogy a Batch túl nagyként utasítja el a feladatot. A legjobb, ha a feladatokat kicsiben tartja, minimalizálva magának a tevékenységnek az erőforrásfájljait.

Ha nincs mód a feladat által igényelt fájlok számának minimalizálására, optimalizálhatja a feladatot, ha egyetlen erőforrásfájlt hoz létre, amely az erőforrásfájlok tárolótárolójára hivatkozik. Ehhez helyezze az erőforrásfájlokat egy Azure Storage-tárolóba, és használja a különböző "tároló" [módszerek](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) erőforrásfájlok. A blob előtag beállításaival megadhatja a feladatokhoz letöltendő fájlok gyűjteményeit.

## <a name="next-steps"></a>További lépések

- Információ az [alkalmazáscsomagokról](batch-application-packages.md) az erőforrásfájlok alternatívájaként.
- Az erőforrásfájlok tárolóinak használatáról a [Tárolómunkaterhelések](batch-docker-container-workloads.md)című témakörben talál további információt.
- A feladatok kimeneti adatainak összegyűjtéséről és mentéséről a [Feladat és a feladat kimenetének megőrzése](batch-task-output.md)című témakörben olvashat.
- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
