---
title: Létrehozásával és használatával a resource files – Azure Batch |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure Batch Erőforrásfájlok bemeneti különböző forrásokból.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: f6693de861658c7174614e80906ee83633fcb313
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501091"
---
# <a name="creating-and-using-resource-files"></a>Létrehozásával és használatával Erőforrásfájlok

Az Azure Batch-feladat gyakran valamilyen adatok feldolgozásához szükséges. Erőforrás-fájlok az eszközt, a Batch virtuális gép (VM) egy feladattal ezeket az adatokat. Feladatok minden típusú erőforrás-fájlok támogatásához: feladatok, indítsa el a feladatokat, a feladat-előkészítési tevékenységeket, a feladatkiadási tevékenységeket, stb. Ez a cikk ismerteti, hogyan hozhat létre az erőforrásfájlokat, és a virtuális gép elhelyezése néhány gyakran használt módszerek.  

Erőforrásfájlok adatokat egy virtuális Gépet, a Batch szolgáltatásban az alakzatot kell egy mechanizmust, de a típusát és azok felhasználási módjáról kvórummodellje rugalmas. Vannak, azonban néhány gyakori alkalmazási helyzetek:

1. Az egyes virtuális Gépeken, az indítási tevékenység erőforrás-fájlok használata közös fájlok üzembe helyezése
1. Feladatok által feldolgozandó bemeneti adatokat kiépítése

Közös fájlok lehet például a tevékenységek által futtatott alkalmazások telepítéséhez használt az indítási tevékenység fájlokat. A bemeneti adatok lehet nyers kép vagy videó adatok vagy a Batch általi feldolgozásának információkat.

## <a name="types-of-resource-files"></a>Erőforrásfájlok típusai

Nincsenek Erőforrásfájlok létrehozásához elérhető néhány különböző lehetőségek. Az erőforrás-fájlok létrehozásának folyamatát az eredeti adatok tárolására függően változik.

Erőforrás-fájl létrehozásával beállítások:

- [Storage-tároló URL-címe](#storage-container-url): Létrehoz egy erőforrás fájlt minden olyan storage-tárolóból az Azure-ban
- [Tároló neve](#storage-container-name): Egy erőforrás-fájlt hoz létre Batch csatolva az Azure storage-fiókban a tároló nevét.
- [Webes végpont](#web-endpoint): Bármely érvényes HTTP URL-címet hoz létre egy erőforrás-fájl

### <a name="storage-container-url"></a>Storage-tároló URL-címe

A storage-tároló URL-címe használatával azt jelenti, hogy minden olyan storage-tárolóba az Azure-beli fájlok elérhetők. A megfelelő engedélyekkel

A jelen C# a példában a fájlokat már feltöltötte egy Azure storage-tároló, blob storage-bA. Egy erőforrás-fájl létrehozásához szükséges adatok eléréséhez, először létre kell érheti el, a storage-tárolót.

Hozzon létre egy közös hozzáférésű jogosultságkód (SAS) URI-t a megfelelő engedélyekkel a storage-tároló eléréséhez. Állítsa be a lejárati idő és engedélyek megadása az SAS. Ebben az esetben nincs kezdési időpont van megadva, így azonnal hatályba lép, és a létrehozása után két órával lejár az SAS.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> A tároló eléréséhez, kell rendelkeznie mindkét `Read` és `List` engedélyeket, mivel a blob-hozzáférést, csak kell `Read` engedéllyel.

Az engedélyek konfigurálása után a SAS-token létrehozásához, és a storage-tárolót a hozzáférést a SAS URL-cím formátuma. A formázott SAS URL-címet használ a tároló létrehozásához az erőforrásfájl [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

A SAS URL-cím generálása helyett, hogy engedélyezze a névtelen, nyilvános olvasási hozzáférést a tárolóhoz és annak blobjaihoz, az Azure Blob storage-ban. Ezzel a módszerrel adhat a fiókkulcs megosztása nélkül, és anélkül, hogy a SAS csak olvasási hozzáféréssel ezekhez az erőforrásokhoz. Nyilvános olvasási hozzáférés jellemzően a forgatókönyvek egyes blobok névtelen olvasási hozzáférés meg a kívánt helyre. Ha ebben a forgatókönyvben a megoldás megfelelő, tekintse meg a [névtelen hozzáférés a blobokhoz](../storage/blobs/storage-manage-access-to-resources.md) cikk további információt a blobadatokhoz való hozzáférés kezelésére.

### <a name="storage-container-name"></a>Tároló neve

Konfigurálása és a egy SAS URL-cím létrehozása helyett használhatja az Azure storage-tároló neve a Blobadatok eléréséhez. A storage-tárolót az Azure storage-fiókot, amely kapcsolódik a Batch-fiók, más néven a autostorage fiókot kell használni. A tároló nevét egy autostorage fiók használatával lehetővé teszi, hogy átugorja konfigurálni, vagy létrehoz egy SAS URL-címet egy storage-tároló eléréséhez.

Ebben a példában feltételezzük, hogy az erőforrások fájl létrehozásához használt adatok már van Azure Storage-fiókban a Batch-fiókhoz csatolva. Ha autostorage fiók nem rendelkezik, tekintse meg a lépéseket a [Batch-fiók létrehozása](/create-a-batch-account.md) megtudhatja, hogyan hozhat létre, és a egy fiók számára.

A társított storage-fiók használatával nem kell létrehozni és konfigurálni egy storage-tároló SAS URL-CÍMÉT. Ehelyett adja meg a társított storage-fiókban a tároló nevét.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webes végpont

Az Azure Storage nem feltöltött adatok továbbra is használható erőforrás-fájlok létrehozása. Bármely érvényes HTTP URL-címet a bemeneti adatokat tartalmazó is megadhat. A Batch API megadott URL-CÍMÉT, és ezután az adatok segítségével hozzon létre egy erőforrás-fájlt.

A következő C# a bemeneti adatokat a példában kitalált GitHub-végpont-ban üzemel. Az API-t kérdezi le a fájl érvényes webes végpontról, és létrehoz egy erőforrás-fájlt a tevékenység által felhasznált. Nem szükségesek hitelesítő adatok ehhez a forgatókönyvhöz.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

Minden egyes Azure Batch-feladat fájlokat használ másképp, ezért a köteg fájlokat a feladatok kezeléséhez rendelkezésre álló lehetőségeket. A következő esetekben nem azt, hogy átfogó, de helyette terjed ki néhány gyakori helyzet és javaslatokat biztosít.

### <a name="many-resource-files"></a>Számos Erőforrásfájlok

A Batch-feladat a közös fájlok használó több feladatot is tartalmazhat. Általános feladat számos feladat között van megosztva, ha erőforrásfájlokat használata helyett a fájlok tárolásához egy alkalmazáscsomagot használatával jobb megoldás lehet. Az alkalmazáscsomagok adja meg a letöltési sebesség optimalizálása. Alkalmazáscsomagok adatok között a feladatok, gyorsítótáraz is, így a feladat-fájlok nem változnak gyakran, ha az alkalmazáscsomagok remekül beválik, ha a megoldás lehet. Az alkalmazáscsomagokkal nem kell manuálisan kezelése több Erőforrásfájlok vagy SAS URL-címeket, a fájlok az Azure Storage eléréséhez hozzon létre. A Batch a háttérben az Azure Storage tárolja, és üzembe helyezéséhez alkalmazáscsomagokkal számítási csomópontokra működik.

Minden feladathoz hozzá a feladat egyedi sok fájl van, ha erőforrásfájlokat várhatóan többnyire leginkább megfelelő opció kiválasztásához. Egyedi fájlok a gyakran használt feladatokat kell frissíteni vagy cserélni, která není ugyanilyen egyszerűen az alkalmazás csomagok tartalmát. Erőforrásfájlok frissítése, hozzáadásához és az egyes fájlok szerkesztésével további rugalmasságot biztosít.

### <a name="number-of-resource-files-per-task"></a>Feladatonként erőforrás fájlok száma

Ha egy tevékenységhez megadott több száz erőforrást fájlt, a Batch előfordulhat, hogy túl nagy, a tevékenység elutasítása. Célszerű, hogy maradjon kicsi a tevékenységek által maga a feladat erőforrás-fájlok számának minimalizálása.

Ha nem lehet minimálisra csökkentése érdekében a fájlok számát a feladat van szüksége, akkor a feladat által hivatkozott erőforrás-fájlok egy storage-tárolót egyetlen erőforrás-fájl létrehozásával optimalizálhatja. Ehhez az erőforrások fájljainak üzembe az Azure Storage-tárolókat, és erőforrásfájlok "Container" mód használata. A blob előtag beállítások segítségével adja meg a fájlok le kell tölteni a tevékenységek gyűjteményei.

## <a name="next-steps"></a>További lépések

- Ismerje meg [alkalmazáscsomagok](batch-application-packages.md) Erőforrásfájlok alternatívájaként.
- Az erőforrások fájljainak tárolók használatával kapcsolatos további információkért lásd: [tárolókhoz kapcsolódó számítási feladatok](batch-docker-container-workloads.md).
- Megtudhatja, hogyan gyűjthet, és mentse a kimeneti adatokat a feladatoktól, lásd: [feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md).
- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).