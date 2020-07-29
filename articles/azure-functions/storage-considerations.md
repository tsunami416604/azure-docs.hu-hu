---
title: A Azure Functions tárolási szempontjai
description: Ismerje meg a Azure Functions tárolási követelményeit és a tárolt adat titkosítását.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aefd9a35235a09d94973f383603349f6862bbdd9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318181"
---
# <a name="storage-considerations-for-azure-functions"></a>A Azure Functions tárolási szempontjai

A Azure Functions egy Azure Storage-fiókot igényel a Function App-példány létrehozásakor. A Function app a következő tárolási szolgáltatásokat használhatja:


|Tárolási szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Kötések állapotának és funkcióbillentyűk megtartása.  <br/>[A Durable Functionsban a feladatok hubok](durable/durable-functions-task-hubs.md)is használják. |
| [Azure Files](../storage/files/storage-files-introduction.md)  | A Function app-kód tárolásához és futtatásához használt fájlmegosztás a használati [tervben](functions-scale.md#consumption-plan) és a [prémium csomagban](functions-scale.md#premium-plan). |
| [Azure üzenetsor-tároló](../storage/queues/storage-queues-introduction.md)     | [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  [A feladatok hubok használják Durable Functionsban](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> A fogyasztási/prémium szintű üzemeltetési csomag használatakor a rendszer a fő Storage-fiókban tárolja a függvény kódját és a kötési konfigurációs fájlokat az Azure file Storage-ban. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint például az eseményindítók és a naplózási függvények végrehajtásának kezelése. Egyes Storage-fiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok csak a blob Storage-fiókokat, az Azure-Premium Storageokat és a ZRS-replikációval rendelkező általános célú Storage-fiókokat tartalmazzák. A nem támogatott fiókokat a rendszer kiszűri a Storage-fiók panelről a Function-alkalmazás létrehozásakor.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services). 

Habár meglévő Storage-fiókot is használhat a Function alkalmazással, meg kell győződnie arról, hogy megfelel a követelményeknek. Az alkalmazás-létrehozási folyamat részeként létrehozott Storage-fiókok garantáltan megfelelnek a Storage-fiókra vonatkozó követelményeknek.  

## <a name="storage-account-guidance"></a>A Storage-fiók útmutatója

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törlődik, a Function alkalmazás nem fog futni. A tárolással kapcsolatos problémák elhárításával kapcsolatban lásd: [a tárolással kapcsolatos problémák elhárítása](functions-recover-storage-account.md). A következő további szempontok vonatkoznak a Function apps által használt Storage-fiókra.

### <a name="storage-account-connection-setting"></a>Storage-fiók kapcsolatainak beállítása

A Storage-fiók kapcsolatai a [AzureWebJobsStorage alkalmazás-beállításban](./functions-app-settings.md#azurewebjobsstorage)maradnak. 

A Storage-fiók kapcsolódási karakterláncát frissíteni kell a tárolási kulcsok újragenerálása során. [További információ a Storage Key managementről](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Megosztott Storage-fiókok

Több Function-alkalmazás is lehetséges, hogy problémák nélkül megoszthatja ugyanazt a Storage-fiókot. A Visual Studióban például több alkalmazás is fejleszthető az Azure Storage Emulator használatával. Ebben az esetben az emulátor egyetlen Storage-fiókhoz hasonlóan működik. A Function alkalmazás által használt Storage-fiók is használható az alkalmazásadatok tárolására. Azonban ez a megközelítés nem mindig jó ötlet az éles környezetben.

### <a name="optimize-storage-performance"></a>A tárolási teljesítmény optimalizálása

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Storage-adattitkosítás

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>Csatlakoztatási fájlmegosztás (Linux)

Meglévő Azure Files-megosztásokat csatlakoztathat a Linux Function-alkalmazásaihoz. Ha egy megosztást csatlakoztat a linuxos Function-alkalmazáshoz, használhatja a meglévő gépi tanulási modelleket és a függvények más adatait. A [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) parancs használatával meglévő megosztásokat csatlakoztathat a Linux-függvény alkalmazásához. 

Ebben a parancsban a `share-name` létező Azure Files megosztás neve, és `custom-id` bármely olyan karakterlánc lehet, amely egyedileg definiálja a megosztást a Function alkalmazáshoz való csatlakoztatáskor. Emellett az az `mount-path` elérési út, amelyből a megosztás elérhető a Function alkalmazásban. `mount-path`formátumúnak kell lennie `/dir-name` , és nem kezdődhet a-vel `/home` .

Teljes példaként tekintse meg a Python- [függvény létrehozása és a Azure Files megosztás csatlakoztatása](scripts/functions-cli-mount-files-storage-linux.md)című témakörben található parancsfájlokat. 

Jelenleg csak a a `storage-type` `AzureFiles` támogatott. Csak öt megosztást lehet csatlakoztatni egy adott Function alkalmazáshoz. A fájlmegosztás csatlakoztatása növelheti a hideg kezdési időt legalább 200 300ms, vagy akár több is, ha a Storage-fiók egy másik régióban található.

A csatlakoztatott megosztás a megadott függvény kódjában érhető el `mount-path` . Ha például a (z `mount-path` ) `/path/to/mount` , a cél könyvtárat a fájlrendszer API-jai segítségével érheti el, ahogy az a következő Python-példában látható:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>További lépések

További információ a Azure Functions üzemeltetési lehetőségeiről.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)
