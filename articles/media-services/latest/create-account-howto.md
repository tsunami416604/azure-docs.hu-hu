---
title: Azure Media Services-fiók létrehozása
description: Ez az oktatóanyag bemutatja az Azure Media Services-fiók létrehozásának lépéseit.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478798"
---
# <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

A médiatartalmak Azure-beli titkosításának, kódolásának, elemzésének, kezelésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. A Media Services-fiókot egy vagy több tárfiókhoz kell társozni.

> [!NOTE]
> A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.

Ez a cikk az új Azure Media Services-fiók létrehozásának lépéseit ismerteti. Válasszon az alábbi lapok közül.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az Azure Portal segítségével gyorsan létrehozhat egy Azure Media Services-fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben.

Jelenleg használhatja az [Azure Portalon:](https://portal.azure.com/)

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése, 
* nézet (nem kezeli) v3 [Eszközök,](assets-concept.md) 
* [az API-k elérésével kapcsolatos információk.](access-api-portal.md) 

Minden más felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://aka.ms/ams-v3-rest-ref)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

Ez a cikk bemutatja, hogyan hozhat létre egy Media Services-fiókot az Azure Portalhasználatával.

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson **a +Forrás létrehozása** > **Media** > **Media Services gombra.**
1. A **Media Services-fiók létrehozása** csoportban adja meg a szükséges értékeket.
    
    | Név | Leírás |
    | ---|---|
    |**Fiók neve**|Adja meg az új Media Services-fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.|
    |**Előfizetés**|Ha egynél több előfizetéssel rendelkezik, válasszon egyet az On List of Azure-előfizetések listájából, amelyekhez hozzáférése van.|
    |**Erőforráscsoport**|Válassza ki az új vagy meglévő erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. Tudjon meg többet [itt](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Helyen**|Válassza ki azt a földrajzi régiót, amelyet a Media Services-fiók média- és metaadatrekordjainak tárolására használni fog. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. |
    |**Tárfiók**|Válasszon ki egy tárfiókot a Media Services-fiókból származó médiatartalom blobtár-tárolásának biztosításához. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.<br/><br/>Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. Az Azure Portal használatával másodlagos tárfiókok hozzáadása. További információ: [Azure Storage-fiókok Azure Media Services-fiókokkal.](storage-account-concept.md)<br/><br/>A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.|
    
1. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
1. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    Amikor a Media Services-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez és a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)előnyeinek kihasználásához a streamelési végpontnak, amelyből tartalmat szeretne streamelni, **futó** állapotban kell lennie. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport létrehozásához használja az alábbi parancsot. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az erőforrásokat (például az Azure Media Services-fiókokat és a kapcsolódó Storage-fiókokat).

Helyettesítheti `amsResourceGroup` az értékét.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Create a storage account

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](storage-account-concept.md).

Rendelkeznie kell egy **elsődleges** tárfiókkal, és a Media Services-fiókhoz társítva tetszőleges számú **másodlagos** tárfiókkal rendelkezhet. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../../storage/common/storage-account-options.md) című témakört. 

Ebben a példában létrehozunk egy általános célú v2, Standard LRS-fiókot. Ha tárfiókokkal szeretne kísérletezni, használja a használatát. `--sku Standard_LRS` Azonban, ha kiválaszt egy termékváltozat `--sku Standard_RAGRS`a termelés érdemes, amely földrajzi replikációt biztosít az üzletmenet folytonosságát. További információt a [tárfiókok című témakörben talál.](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)
 
Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. `amsResourceGroup`meg kell egyeznie az előző lépésben az erőforráscsoporthoz megadott értékkel. A tárfiók nevének 24-nél rövidebbnek kell lennie.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket `amsaccount` `storageaccountforams` helyettesítheti: (meg kell egyeznie a `amsResourceGroup` tárfiókhoz megadott értékkel), és (meg kell egyeznie az erőforráscsoporthoz megadott értékkel).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Lásd még

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Másodlagos tárterület csatolása Media Services-fiókhoz](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
