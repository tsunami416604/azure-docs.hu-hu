---
title: Function-alkalmazás létrehozása az Azure Portalról
description: Hozzon létre egy új Function alkalmazást az Azure-ban a portálon.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230773"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Függvényalkalmazás létrehozása az Azure Portal használatával

Ebből a témakörből megtudhatja, hogyan hozhat létre egy Function-alkalmazást a Azure Portal a Azure Functions használatával. A függvényalkalmazás olyan tároló, amely a különálló függvények végrehajtását futtatja. 

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Ha függvényalkalmazást hoz létre, ügyeljen arra, hogy érvényes **alkalmazásnevet** adjon meg, amely csak betűket, számokat és kötőjelet tartalmazhat. Az aláhúzás ( **_** ) nem engedélyezett karakter.

A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. A tárfiók nevének egyedinek kell lennie az Azure rendszerben. 

A függvényalkalmazás létrehozása után létrehozhatja a különálló függvényeket egy vagy több nyelven. A [portál használatával](functions-create-first-azure-function.md#create-function), [folyamatos üzembe helyezéssel](functions-continuous-deployment.md), vagy [FTP-s feltöltéssel](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) is létrehozhatja a függvényeket.

## <a name="service-plans"></a>Szolgáltatáscsomagok

Azure Functions három különböző szolgáltatási csomaggal rendelkezik: fogyasztási terv, Prémium csomag és dedikált (App Service) csomag. A Function app létrehozásakor ki kell választania a szolgáltatási tervet, és azt később nem lehet módosítani. További információ: [Azure Functions szolgáltatási csomag kiválasztása](functions-scale.md).

Ha a JavaScript-függvények dedikált (App Service) csomagon való futtatását tervezi, válasszon egy kevesebb magot tartalmazó csomagot. További információ: [JavaScript-referencia a Functionshöz](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. A Functions a Storage-ot használja olyan belső műveletekre, mint például az eseményindítók kezelése és a függvénykivételek naplózása. Egyes Storage-fiókok, mint például a csak blobok tárolására alkalmas tárfiókok, az Azure Premium Storage és a ZRS-replikációval rendelkező általános célú tárolófiókok nem támogatják az üzenetsorokat és a táblákat. Ezek a fiókok nem jelennek meg a Storage-fiók panelen a függvényalkalmazások létrehozásakor.

>[!NOTE]
>A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Következő lépések

Míg a Azure Portal megkönnyíti a függvények létrehozását és kipróbálását, javasoljuk a [helyi fejlesztést](functions-develop-local.md). Miután létrehozta a Function alkalmazást a portálon, továbbra is hozzá kell adnia egy függvényt. 

> [!div class="nextstepaction"]
> [HTTP által aktivált függvény hozzáadása](functions-create-first-azure-function.md#create-function)
