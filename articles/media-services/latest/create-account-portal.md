---
title: Azure Media Services-fiók létrehozása az Azure Portal használatával
description: Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével Azure Media Services-fiókot hozhat létre az Azure Portal használatával.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137194"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Media Services-fiók létrehozása a Azure Portal használatával

A Azure Portal segítségével gyorsan létrehozhat egy Azure Media Services fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben.

Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése 
* v3- [eszközök](assets-concept.md)megtekintése (nem felügyelt) 
* [az API-k elérésére vonatkozó információk beolvasása](access-api-portal.md). 

Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.

Ez a cikk bemutatja, hogyan hozhat létre egy Media Services fiókot a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Kattintson **az + erőforrás létrehozása** > **média** > **Media Services**elemre.
1. A **Media Services fiók létrehozása** szakaszban adja meg a szükséges értékeket.
    
    | Name (Név) | Leírás |
    | ---|---|
    |**Account Name** (Fióknév)|Adja meg az új Media Services fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.|
    |**Előfizetés**|Ha egynél több előfizetéssel rendelkezik, válasszon egyet azon Azure-előfizetések listájából, amelyekhez hozzáfér.|
    |**Erőforráscsoport**|Válassza ki az új vagy meglévő erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../../azure-resource-manager/management/overview.md#resource-groups) talál.|
    |**Hely**|Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. |
    |**Tárfiók**|Válassza ki azt a Storage-fiókot, amely a Media Services fiókjából származó médiatartalom blob Storage-tárolóját adja meg. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy új tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre. A tárfiókok elnevezési szabályai ugyanazok, mint a Media Services-fiókok esetében.<br/><br/>Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Azure Portal másodlagos Storage-fiókok hozzáadására is használható. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](storage-account-concept.md).<br/><br/>A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.|
    
1. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
1. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A Media Services-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie. 

## <a name="next-steps"></a>Következő lépések

Ha programozott módon szeretné elérni Media Services API-t, tekintse meg [a Azure Media Services API Azure ad-hitelesítéssel történő elérését](access-api-portal.md)ismertető témakört.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

