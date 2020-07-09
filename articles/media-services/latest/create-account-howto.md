---
title: Azure Media Services-fiók létrehozása
description: Ez az oktatóanyag végigvezeti egy Azure Media Services fiók létrehozásának lépésein.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79478798"
---
# <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az Azure-ban tárolt médiatartalmak titkosításának, kódolásának, elemzésének, kezelésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services fiókot egy vagy több Storage-fiókhoz kell társítani.

> [!NOTE]
> A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.

Ez a cikk új Azure Media Services-fiók létrehozásának lépéseit ismerteti. Válasszon a következő lapok közül.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

A Azure Portal segítségével gyorsan létrehozhat egy Azure Media Services fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben.

Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése 
* v3- [eszközök](assets-concept.md)megtekintése (nem felügyelt) 
* [az API-k elérésére vonatkozó információk beolvasása](access-api-portal.md). 

Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.

Ez a cikk bemutatja, hogyan hozhat létre egy Media Services fiókot a Azure Portal használatával.

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson **az + erőforrás létrehozása**  >  **adathordozó**-  >  **Media Services**elemre.
1. A **Media Services fiók létrehozása** szakaszban adja meg a szükséges értékeket.
    
    | Name | Description |
    | ---|---|
    |**Account Name**|Adja meg az új Media Services fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.|
    |**Előfizetés**|Ha egynél több előfizetéssel rendelkezik, válasszon egyet azon Azure-előfizetések listájából, amelyekhez hozzáfér.|
    |**Erőforráscsoport**|Válassza ki az új vagy meglévő erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információ [itt](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Hely**|Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. |
    |**Tárfiók**|Válassza ki azt a Storage-fiókot, amely a Media Services fiókjából származó médiatartalom blob Storage-tárolóját adja meg. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.<br/><br/>Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Azure Portal másodlagos Storage-fiókok hozzáadására is használható. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](storage-account-concept.md).<br/><br/>A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.|
    
1. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
1. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A Media Services fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport létrehozásához használja az alábbi parancsot. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az erőforrásokat (például az Azure Media Services-fiókokat és a kapcsolódó Storage-fiókokat).

Helyettesítheti az `amsResourceGroup` értékét.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](storage-account-concept.md).

Rendelkeznie kell egy **elsődleges** Storage-fiókkal, és tetszőleges számú **másodlagos** Storage-fiók társítható a Media Services fiókjához. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../../storage/common/storage-account-options.md) című témakört. 

Ebben a példában egy általános célú v2 standard LRS-fiókot hozunk létre. Ha a Storage-fiókokkal szeretne kísérletezni, használja a következőt: `--sku Standard_LRS` . Ha azonban az üzemi célú SKU-t választotta, érdemes figyelembe vennie az `--sku Standard_RAGRS` üzleti folytonosságot, amely földrajzi replikációt tesz lehetővé. További információ: Storage- [fiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. `amsResourceGroup`az előző lépésben az erőforráscsoport számára megadott értéknek kell megegyeznie. A Storage-fiók nevének 24-nél rövidebbnek kell lennie.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket lehet lecserélni: `amsaccount` `storageaccountforams` (meg kell egyeznie a Storage-fiókhoz megadott értékkel), és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport számára megadott értékkel).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Lásd még

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Másodlagos tároló csatolása Media Services-fiókhoz](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
