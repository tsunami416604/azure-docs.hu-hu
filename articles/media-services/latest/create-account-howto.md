---
title: Azure Media Services-fiók létrehozása
description: Ez az oktatóanyag végigvezeti egy Azure Media Services fiók létrehozásának lépésein.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93f5e4d659b94bd79345a5e687de14ab6a5e8ba6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267972"
---
# <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure-ban tárolt médiatartalmak titkosításának, kódolásának, elemzésének, kezelésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services fiókot egy vagy több Storage-fiókhoz kell társítani. Ez a cikk új Azure Media Services-fiók létrehozásának lépéseit ismerteti.

> [!NOTE]
> A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.

 Media Services-fiók létrehozásához használhatja a Azure Portal vagy a CLI-t. Válassza ki a használni kívánt metódus lapját.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portál](#tab/portal/)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
A Azure Portal segítségével gyorsan létrehozhat egy Azure Media Services fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben.

Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése 
* v3- [eszközök](assets-concept.md)megtekintése (nem felügyelt) 
* [az API-k elérésére vonatkozó információk beolvasása](./access-api-howto.md). 

Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Media Services-fiók létrehozása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson **az + erőforrás létrehozása**  >  **adathordozó**-  >  **Media Services**elemre.
1. A **Media Services fiók létrehozása** szakaszban adja meg a szükséges értékeket.

    | Név | Leírás |
    | ---|---|
    |**Fiók neve**|Adja meg az új Media Services fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.|
    |**Előfizetés**|Ha egynél több előfizetéssel rendelkezik, válasszon egyet azon Azure-előfizetések listájából, amelyekhez hozzáfér.|
    |**Erőforráscsoport**|Válassza ki az új vagy meglévő erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információ [itt](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Hely**|Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. |
    |**Storage-fiók**|Válassza ki azt a Storage-fiókot, amely a Media Services fiókjából származó médiatartalom blob Storage-tárolóját adja meg. Választhat egy, a Media Services-fiókkal azonos földrajzi régióban elhelyezkedő meglévő tárfiókot, vagy új fiókot is létrehozhat. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.<br/><br/>Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Azure Portal másodlagos Storage-fiókok hozzáadására is használható. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](storage-account-concept.md).<br/><br/>A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.|

1. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
1. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A Media Services fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie. 

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Lásd még

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Másodlagos tároló csatolása Media Services-fiókhoz](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
