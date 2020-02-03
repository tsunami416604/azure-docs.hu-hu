---
title: A Azure Functions tárolási szempontjai
description: Ismerje meg a Azure Functions tárolási követelményeit és a tárolt adat titkosítását.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 353fdd3bf7775e3bc7a9d017a9e8dd8238b09830
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964981"
---
# <a name="storage-considerations-for-azure-functions"></a>A Azure Functions tárolási szempontjai

A Azure Functions egy Azure Storage-fiókot igényel a Function App-példány létrehozásakor. A Function app a következő tárolási szolgáltatásokat használhatja:


|Tárolási szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob Storage](/storage/blobs/storage-blobs-overview.md)     | Kötések állapotának és funkcióbillentyűk megtartása.  <br/>[A Durable Functionsban a feladatok hubok](durable/durable-functions-task-hubs.md)is használják. |
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

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions üzemeltetési lehetőségeiről.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)


