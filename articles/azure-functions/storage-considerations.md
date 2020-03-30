---
title: Az Azure Functions tárolási szempontjai
description: Ismerje meg az Azure Functions tárolási követelményeit és a tárolt adatok titkosítását.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276581"
---
# <a name="storage-considerations-for-azure-functions"></a>Az Azure Functions tárolási szempontjai

Az Azure Functions egy Azure Storage-fiókot igényel, amikor létrehoz egy függvényalkalmazás-példányt. A függvényalkalmazás a következő tárolási szolgáltatásokat is igénybe veheti:


|Tárolási szolgáltatás  | Függvények használata  |
|---------|---------|
| [Azure Blob-tárhely](../storage/blobs/storage-blobs-introduction.md)     | A kötések állapotának és a funkciókulcsoknak karbantartása.  <br/>A [tartós függvények feladatközpontjai](durable/durable-functions-task-hubs.md)is használják. |
| [Azure Files](../storage/files/storage-files-introduction.md)  | A függvényalkalmazás kódjának tárolására és futtatására használt fájlmegosztás [a felhasználási tervben.](functions-scale.md#consumption-plan) |
| [Azure-várólista-tárolás](../storage/queues/storage-queues-introduction.md)     | A [feladatközpontok használják a Tartós függvények funkcióban.](durable/durable-functions-task-hubs.md)   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  A [feladatközpontok használják a Tartós függvények funkcióban.](durable/durable-functions-task-hubs.md)       |

> [!IMPORTANT]
> A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Függvényalkalmazás létrehozásakor létre kell hoznia, vagy egy általános célú Azure Storage-fiókot kell létrehoznia, amely támogatja a Blob, a Várólista és a Table storage-ot. Ennek az az oka, hogy a Függvények az Azure Storage-ra támaszkodik olyan műveletekhez, mint az eseményindítók kezelése és a naplózási függvény-végrehajtások. Egyes tárfiókok nem támogatják a várólistákat és táblákat. Ezek a fiókok közé tartozik a blob-csak tárfiókok, az Azure Premium Storage és az általános célú storage-fiókok ZRS replikációval. Ezek a nem támogatott fiókok kivannak szűrve a Storage Account panelből egy függvényalkalmazás létrehozásakor.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services). 

Bár egy meglévő tárfiókot a függvényalkalmazás, győződjön meg arról, hogy megfelel ezeknek a követelményeknek. A függvényalkalmazás létrehozása folyamat részeként létrehozott tárfiókok garantáltan megfelelnek ezeknek a tárfiók-követelményeknek.  

## <a name="storage-account-guidance"></a>A tárfiókhoz való útmutatás

Minden függvényalkalmazás működéséhez tárfiók szükséges. Ha ez a fiók törlődik a függvényalkalmazás nem fog futni. A tárolással kapcsolatos problémák elhárításáról A [tárolással kapcsolatos problémák elhárítása](functions-recover-storage-account.md)című témakörben látható. A következő további szempontok a függvényalkalmazások által használt Storage-fiókra vonatkoznak.

### <a name="storage-account-connection-setting"></a>Tárfiók kapcsolatának beállítása

A tárfiók-kapcsolat az [AzureWebJobsStorage alkalmazásbeállításban](./functions-app-settings.md#azurewebjobsstorage)marad meg. 

A tárfiók kapcsolati karakterláncát frissíteni kell a tárolókulcsok újragenerálásakor. [Tudjon meg többet a tárolási kulcs kezelése itt](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Megosztott tárfiókok

Lehetséges, hogy több függvényalkalmazások ugyanazt a tárfiókot, problémák nélkül. A Visual Studio-ban például több alkalmazást is fejleszthet az Azure Storage-emulátorral. Ebben az esetben az emulátor úgy viselkedik, mint egy tárfiók. A függvényalkalmazás által használt tárfiók is használható az alkalmazásadatok tárolására. Ez a megközelítés azonban nem mindig jó ötlet éles környezetben.

### <a name="optimize-storage-performance"></a>A tárolási teljesítmény optimalizálása

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Tárolási adatok titkosítása

Az Azure Storage titkosítja az összes adatot egy tárolófiókban inaktív. További információ: [Azure Storage titkosítás a nyugalmi adatok.](../storage/common/storage-service-encryption.md)

Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat a blob- és fájladatok titkosításához. Ezeknek a kulcsoknak meg kell jelen lenniük az Azure Key Vault a funkciók a tárfiók eléréséhez. További információ: [Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure Portalon.](../storage/common/storage-encryption-keys-portal.md)  

## <a name="mount-file-shares-linux"></a>Fájlmegosztások csatlakoztatása (Linux)

Meglévő Azure Files-megosztásokat csatlakoztathat a Linux-függvényalkalmazásokhoz. A Linux függvényalkalmazásmegosztás csatlakoztatásával kihasználhatja a meglévő gépi tanulási modelleket vagy a függvényekben lévő egyéb adatokat. A [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) paranccsal egy meglévő megosztást csatlakoztathat a Linux függvényalkalmazáshoz. 

Ebben a `share-name` parancsban a meglévő Azure Files-megosztás neve, és `custom-id` bármilyen karakterlánc lehet, amely egyedileg határozza meg a megosztást, amikor a függvényalkalmazáshoz van csatlakoztatva. Is, `mount-path` az elérési út, ahonnan a megosztás érhető el a függvényalkalmazásban. `mount-path`formátumban kell `/dir-name`lennie, és nem kezdheti el. `/home`

Egy teljes példa, tekintse meg a parancsfájlok [a Python-függvény létrehozása app, és csatlakoztatni egy Azure Files share.](scripts/functions-cli-mount-files-storage-linux.md) 

Jelenleg csak `storage-type` egy `AzureFiles` támogatott. Csak öt megosztást csatlakoztathat egy adott függvényalkalmazáshoz. A fájlmegosztás csatlakoztatása legalább 200-300 ms-ra növelheti a hidegindítási időt, vagy még többet, ha a tárfiók egy másik régióban van.

A csatlakoztatott megosztás a `mount-path` megadott funkciókódhoz érhető el. Ha például `mount-path` `/path/to/mount`a , hozzáférhet a célkönyvtárfájlrendszer API-k, mint a következő Python példa:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>További lépések

További információ az Azure Functions üzemeltetési beállításairól.

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetése](functions-scale.md)


