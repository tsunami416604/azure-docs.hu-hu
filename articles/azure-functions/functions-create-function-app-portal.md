---
title: Függvényalkalmazás létrehozása az Azure Portal használatával
description: Hozzon létre egy új Function alkalmazást az Azure-ban a portálon.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985014"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Függvényalkalmazás létrehozása az Azure Portal használatával

Ebből a témakörből megtudhatja, hogyan hozhat létre egy Function-alkalmazást a Azure Portal a Azure Functions használatával. A függvényalkalmazás olyan tároló, amely a különálló függvények végrehajtását futtatja. 

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

A függvényalkalmazás létrehozása után létrehozhatja a különálló függvényeket egy vagy több nyelven. A [portál használatával](functions-create-first-azure-function.md#create-function), [folyamatos üzembe helyezéssel](functions-continuous-deployment.md), vagy [FTP-s feltöltéssel](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) is létrehozhatja a függvényeket.

## <a name="service-plans"></a>Szolgáltatáscsomagok

Azure Functions három különböző szolgáltatási csomaggal rendelkezik: fogyasztási terv, Prémium csomag és dedikált (App Service) csomag. A Function app létrehozásakor ki kell választania a szolgáltatási tervet, és azt később nem lehet módosítani. További információ: [Azure Functions szolgáltatási csomag kiválasztása](functions-scale.md).

Ha a JavaScript-függvények dedikált (App Service) csomagon való futtatását tervezi, válasszon egy kevesebb magot tartalmazó csomagot. További információ: [JavaScript-referencia a Functionshöz](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. A Functions a Storage-ot használja olyan belső műveletekre, mint például az eseményindítók kezelése és a függvénykivételek naplózása. Egyes Storage-fiókok, mint például a csak blobok tárolására alkalmas tárfiókok, az Azure Premium Storage és a ZRS-replikációval rendelkező általános célú tárolófiókok nem támogatják az üzenetsorokat és a táblákat. Ezek a fiókok nem jelennek meg a Storage-fiók panelen a függvényalkalmazások létrehozásakor.

>[!NOTE]
>A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>További lépések

Míg a Azure Portal megkönnyíti a függvények létrehozását és kipróbálását, javasoljuk a [helyi fejlesztést](functions-develop-local.md). Miután létrehozta a Function alkalmazást a portálon, továbbra is hozzá kell adnia egy függvényt. 

> [!div class="nextstepaction"]
> [HTTP által aktivált függvény hozzáadása](functions-create-first-azure-function.md#create-function)
