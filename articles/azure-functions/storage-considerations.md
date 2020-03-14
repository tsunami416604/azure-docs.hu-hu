---
title: A Azure Functions tárolási szempontjai
description: Ismerje meg a Azure Functions tárolási követelményeit és a tárolt adat titkosítását.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276581"
---
# <a name="storage-considerations-for-azure-functions"></a>A Azure Functions tárolási szempontjai

A Azure Functions egy Azure Storage-fiókot igényel a Function App-példány létrehozásakor. A Function app a következő tárolási szolgáltatásokat használhatja:


|Tárolási szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Kötések állapotának és funkcióbillentyűk megtartása.  <br/>[A Durable Functionsban a feladatok hubok](durable/durable-functions-task-hubs.md)is használják. |
| [Azure Files](../storage/files/storage-files-introduction.md)  | A függvény alkalmazás kódjának a használati csomagban való tárolásához és futtatásához használt [fájlmegosztás.](functions-scale.md#consumption-plan) |
| [Azure üzenetsor-tároló](../storage/queues/storage-queues-introduction.md)     | [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint például az eseményindítók és a naplózási függvények végrehajtásának kezelése. Egyes Storage-fiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok csak a blob Storage-fiókokat, az Azure-Premium Storageokat és a ZRS-replikációval rendelkező általános célú Storage-fiókokat tartalmazzák. A nem támogatott fiókokat a rendszer kiszűri a Storage-fiók panelről a Function-alkalmazás létrehozásakor.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services). 

Habár meglévő Storage-fiókot is használhat a Function alkalmazással, meg kell győződnie arról, hogy megfelel a követelményeknek. Az alkalmazás-létrehozási folyamat részeként létrehozott Storage-fiókok garantáltan megfelelnek a Storage-fiókra vonatkozó követelményeknek.  

## <a name="storage-account-guidance"></a>A Storage-fiók útmutatója

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törlődik, a Function alkalmazás nem fog futni. A tárolással kapcsolatos problémák elhárításával kapcsolatban lásd: [a tárolással kapcsolatos problémák elhárítása](functions-recover-storage-account.md). A következő további szempontok vonatkoznak a Function apps által használt Storage-fiókra.

### <a name="storage-account-connection-setting"></a>Storage-fiók kapcsolatainak beállítása

A Storage-fiók kapcsolatai a [AzureWebJobsStorage alkalmazás-beállításban](./functions-app-settings.md#azurewebjobsstorage)maradnak. 

A Storage-fiók kapcsolódási karakterláncát frissíteni kell a tárolási kulcsok újragenerálása során. [További információ a Storage Key managementről](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Megosztott Storage-fiókok

Több Function-alkalmazás is lehetséges, hogy problémák nélkül megoszthatja ugyanazt a Storage-fiókot. A Visual Studióban például több alkalmazás is fejleszthető az Azure Storage Emulator használatával. Ebben az esetben az emulátor egyetlen Storage-fiókhoz hasonlóan működik. A Function alkalmazás által használt Storage-fiók is használható az alkalmazásadatok tárolására. Azonban ez a megközelítés nem mindig jó ötlet az éles környezetben.

### <a name="optimize-storage-performance"></a>A tárterület teljesítményének optimalizálása

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Storage-adattitkosítás

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. További információ: az [Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md).

Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a blobok és a fájlok titkosításához. Ezeknek a kulcsoknak jelen kell lenniük a Azure Key Vaultban ahhoz, hogy a függvények hozzáférhessenek a Storage-fiókhoz. További információ: [az ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault használatával a Azure Portal használatával](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Csatlakoztatási fájlmegosztás (Linux)

Meglévő Azure Files-megosztásokat csatlakoztathat a Linux Function-alkalmazásaihoz. Ha egy megosztást csatlakoztat a linuxos Function-alkalmazáshoz, használhatja a meglévő gépi tanulási modelleket és a függvények más adatait. A [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) parancs használatával csatlakoztathat egy meglévő megosztást a Linux-függvény alkalmazásához. 

Ebben a parancsban a `share-name` a meglévő Azure Files-megosztás neve, és a `custom-id` bármely olyan karakterlánc lehet, amely egyedileg definiálja a megosztást a Function alkalmazáshoz való csatlakoztatáskor. Emellett a `mount-path` az az elérési út, amelyből a megosztás elérhető a Function alkalmazásban. a `mount-path` formátumának `/dir-name`nak kell lennie, és nem kezdődhet a `/home`.

Teljes példaként tekintse meg a Python- [függvény létrehozása és a Azure Files megosztás csatlakoztatása](scripts/functions-cli-mount-files-storage-linux.md)című témakörben található parancsfájlokat. 

Jelenleg csak `AzureFiles` `storage-type` támogatott. Csak öt megosztást lehet csatlakoztatni egy adott Function alkalmazáshoz. A fájlmegosztás csatlakoztatása növelheti a hideg kezdési időt legalább 200 300ms, vagy akár több is, ha a Storage-fiók egy másik régióban található.

A csatlakoztatott megosztás a megadott `mount-path`ban érhető el a függvény kódjában. Ha például a `mount-path` `/path/to/mount`, a cél könyvtárat a fájlrendszer API-jai segítségével érheti el, ahogy az a következő Python-példában látható:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions üzemeltetési lehetőségeiről.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)


