---
title: Erőforrás-fájlok létrehozása és használata – Azure Batch | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Batch forrásfájlokat különböző bemeneti forrásokból.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 9c55b22d1cb85fb645087cf48b54f9d5ac12d58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322180"
---
# <a name="creating-and-using-resource-files"></a>Erőforrás-fájlok létrehozása és használata

Egy Azure Batch feladatnak gyakran kell valamilyen adattípust feldolgoznia. Az erőforrásfájl azt jelenti, hogy az adatok a Batch virtuális gépre (VM) feladaton keresztül adhatók meg. Az összes típusú feladat támogatja az erőforrás-fájlokat: feladatok, indítási feladatok, feladat-előkészítési feladatok, feladat-felszabadítási feladatok stb. Ez a cikk néhány gyakori módszert ismertet az erőforrás-fájlok létrehozásával és a virtuális gépekre való elhelyezésével kapcsolatban.  

Az erőforrás-fájlok egy olyan mechanizmus, amellyel az adatkötegbe helyezhető egy virtuális gépre, de az adattípusa és a használatuk módja rugalmas. Vannak azonban néhány gyakori felhasználási eset:

1. Általános fájlok kiépítése az egyes virtuális gépeken egy indítási tevékenységben lévő erőforrás-fájlok használatával
1. A tevékenységek által feldolgozandó bemeneti adatok kiépítése

Gyakori fájlok lehetnek például egy indítási feladatban lévő fájlok, amelyek a tevékenységek által futtatott alkalmazások telepítéséhez használatosak. A bemeneti adatok lehetnek nyers képek vagy videók adatai, illetve a Batch által feldolgozandó összes adat.

## <a name="types-of-resource-files"></a>Az erőforrásfájl típusai

Az erőforrás-fájlok létrehozásához néhány különböző lehetőség áll rendelkezésre. Az erőforrás-fájlok létrehozási folyamata attól függően változik, hogy hol tárolja az eredeti adatforrást.

Az erőforrásfájl létrehozásának lehetőségei:

- [Storage-tároló URL-címe](#storage-container-url): Létrehoz egy erőforráscsoportot az Azure-ban található bármely tárolóból
- [Storage-tároló neve](#storage-container-name): Létrehoz egy erőforráscsoportot egy, a köteghez csatolt Azure Storage-fiókban található tároló nevéből.
- [Webes végpont](#web-endpoint): Létrehoz egy erőforráscsoportot bármely érvényes HTTP URL-címről

### <a name="storage-container-url"></a>Storage-tároló URL-címe

A Storage-tároló URL-címe azt jelenti, hogy a megfelelő engedélyekkel férhet hozzá az Azure Storage-tárolóban található fájlokhoz.

Ebben C# a példában a fájlok már fel lettek töltve egy Azure Storage-tárolóba blob Storage-ként. Az erőforrásfájl létrehozásához szükséges adateléréshez először be kell szereznie a tárolóhoz való hozzáférést.

Hozzon létre egy közös hozzáférésű aláírás (SAS) URI-t a Storage-tároló eléréséhez szükséges megfelelő engedélyekkel. Állítsa be az SAS lejárati idejét és engedélyeit. Ebben az esetben nincs megadva kezdési időpont, így a SAS azonnal érvényes lesz, és a létrehozása után két órával lejár.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> A tárolók eléréséhez mind a, `Read` mind `List` az engedélyekkel kell rendelkeznie, míg a blob- `Read` hozzáféréssel rendelkezik, csak engedélyre van szüksége.

Az engedélyek konfigurálása után hozza létre az SAS-jogkivonatot, és formázza az SAS URL-címét a tárolóhoz való hozzáféréshez. A Storage-tárolóhoz tartozó formázott SAS URL-cím használatával állítson elő [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)egy erőforráscsoportot a következővel:.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Az SAS URL-cím generálásának alternatívája a névtelen, nyilvános olvasási hozzáférés engedélyezése egy tárolóhoz és a blobokhoz az Azure Blob Storage-ban. Így csak olvasási hozzáférést biztosíthat ezekhez az erőforrásokhoz anélkül, hogy meg kellene osztania a fiók kulcsát, és nem igényel SAS-t. A nyilvános olvasási hozzáférés jellemzően olyan forgatókönyvekhez használatos, amelyekben bizonyos Blobok mindig elérhetők a névtelen olvasási hozzáféréshez. Ha ez a forgatókönyv megfelel a megoldásnak, a blob-adataihoz való hozzáférés kezelésével kapcsolatos további tudnivalókért tekintse meg a Blobok [Névtelen elérését](../storage/blobs/storage-manage-access-to-resources.md) ismertető cikket.

### <a name="storage-container-name"></a>Storage-tároló neve

A SAS URL-cím konfigurálása és létrehozása helyett használhatja az Azure Storage-tároló nevét a blob-adatai eléréséhez. A használt Storage-tárolónak az Azure Storage-fiókban kell lennie, amely a Batch-fiókjához van csatolva, amelyet az autostorage-fióknak nevezünk. Az autostorage-fiók tárolási tárolójának használatával megkerülheti az SAS URL-cím konfigurálását és létrehozását a tárolók eléréséhez.

Ebben a példában feltételezzük, hogy az erőforrás-fájl létrehozásához használt adatforgalom már egy, a Batch-fiókhoz társított Azure Storage-fiókban van. Ha nem rendelkezik automatikus Storage-fiókkal, tekintse meg a [Batch-fiók létrehozása](batch-account-create-portal.md) című témakör lépéseit, amelyekkel megtudhatja, hogyan hozhat létre és kapcsolhat össze egy fiókot.

A társított Storage-fiók használatával nem kell SAS URL-címet létrehoznia és konfigurálnia a tárolóban. Ehelyett adja meg a Storage-tároló nevét a társított Storage-fiókban.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webes végpont

Az Azure Storage szolgáltatásba nem feltöltött adatfájlok továbbra is használhatók az erőforrások létrehozásához. A bemeneti adatokat tartalmazó érvényes HTTP URL-címet is megadhatja. A rendszer az URL-címet a Batch API számára adja meg, majd az adatfájlt használja az erőforrás létrehozásához.

A következő C# példában a bemeneti adatok a fiktív GitHub-végponton vannak tárolva. Az API lekérdezi a fájlt az érvényes webes végpontból, és létrehoz egy, a feladat által felhasználható forrásfájlt. Ehhez a forgatókönyvhöz nem szükségesek hitelesítő adatok.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

Az egyes Azure Batch-feladatok különböző fájlokat használnak, ezért a Batch olyan lehetőségeket kínál, amelyekkel a fájlok kezelhetők a feladatokban. A következő forgatókönyvek nem teljes körűek, hanem néhány gyakori szituációra vonatkoznak, és javaslatokat nyújtanak.

### <a name="many-resource-files"></a>Számos erőforrás-fájl

A Batch-feladat több olyan feladatot is tartalmazhat, amelyek mindegyike azonos, közös fájlokat használ. Ha az általános feladatsorok számos feladat között vannak megosztva, akkor a fájlok használata helyett a fájlokat tartalmazó alkalmazáscsomag használata jobb megoldás lehet. Az alkalmazás csomagjai a letöltési sebesség optimalizálását biztosítják. Emellett az alkalmazáscsomag adatai is gyorsítótárazva vannak a feladatok között, így ha a feladatsorok nem változnak gyakran, az alkalmazáscsomag hasznos lehet a megoldáshoz. Alkalmazáscsomag használata esetén nem kell manuálisan több erőforrást kezelnie, vagy SAS URL-címeket létrehoznia az Azure Storage-ban található fájlokhoz való hozzáféréshez. A Batch a háttérben működik együtt az Azure Storage szolgáltatással az alkalmazáscsomag számítási csomópontokon való tárolásához és üzembe helyezéséhez.

Ha az egyes feladatokhoz több fájl is tartozik, az erőforrás-fájlok többnyire a legjobb megoldásnak bizonyulnak. Az egyedi fájlokat használó feladatokat gyakran frissíteni kell vagy le kell cserélni, ami nem olyan egyszerű, mint az alkalmazás-csomagok tartalmának használata. Az erőforrás-fájlok további rugalmasságot biztosítanak az egyes fájlok frissítéséhez, hozzáadásához és szerkesztéséhez.

### <a name="number-of-resource-files-per-task"></a>Erőforrás-fájlok száma tevékenység alapján

Ha egy feladatban több száz erőforrás van megadva, a Batch elutasítja a feladatát, mert túl nagy. A feladatok minimalizálása érdekében a legjobb, ha a feladatban lévő erőforrás-fájlok számát minimálisra csökkenti.

Ha nincs mód a feladat által igényelt fájlok számának minimalizálására, akkor optimalizálhatja a feladatot úgy, hogy létrehoz egy erőforrás-tárolót, amely az erőforrás-fájlok tárolóhelyére hivatkozik. Ehhez helyezze el az erőforrás-fájlokat egy Azure Storage-tárolóba, és használja az erőforrás-fájlok különböző "Container" módjait. A blob-előtag beállításaival megadhatja a feladatokhoz letölthető fájlok gyűjteményeit.

## <a name="next-steps"></a>További lépések

- Ismerje meg az [alkalmazás csomagjait](batch-application-packages.md) az erőforrás-fájlok alternatívájaként.
- További információ a tárolók erőforrás-fájlokhoz való használatáról: [tároló](batch-docker-container-workloads.md)munkaterhelések.
- Ha szeretné megtudni, hogyan gyűjtheti és mentheti a tevékenységek kimeneti adatait, tekintse meg a feladatok [és tevékenységek kimenetének](batch-task-output.md)megtartása című témakört.
- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
