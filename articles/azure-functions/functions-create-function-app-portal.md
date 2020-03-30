---
title: Függvényalkalmazás létrehozása az Azure Portalról
description: Hozzon létre egy új függvényalkalmazást az Azure-ban a portálról.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368757"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Függvényalkalmazás létrehozása az Azure Portal használatával

Ez a témakör bemutatja, hogyan azure functions segítségével hozzon létre egy függvényalkalmazást az Azure Portalon. A függvényalkalmazás olyan tároló, amely a különálló függvények végrehajtását futtatja. 

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

A függvényalkalmazás létrehozása után létrehozhatja a különálló függvényeket egy vagy több nyelven. A [portál használatával](functions-create-first-azure-function.md#create-function), [folyamatos üzembe helyezéssel](functions-continuous-deployment.md), vagy [FTP-s feltöltéssel](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) is létrehozhatja a függvényeket.

## <a name="service-plans"></a>Szolgáltatáscsomagok

Az Azure Functions három különböző szolgáltatási csomaggal rendelkezik: fogyasztási csomag, prémium csomag és dedikált (App Service) csomag. A szolgáltatáscsomagot a függvényalkalmazás létrehozásakor kell kiválasztania, és ezt követően nem módosítható. További információ: [Azure Functions szolgáltatási csomag kiválasztása](functions-scale.md).

Ha javascript-függvények dedikált (App Service) csomagon való futtatását tervezi, válasszon egy kevesebb maggal rendelkező csomagot. További információ: [JavaScript-referencia a Functionshöz](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Függvényalkalmazás létrehozásakor létre kell hoznia, vagy egy általános célú Azure Storage-fiókot kell létrehoznia, amely támogatja a Blob, a Várólista és a Table storage-ot. A Functions a Storage-ot használja olyan belső műveletekre, mint például az eseményindítók kezelése és a függvénykivételek naplózása. Egyes Storage-fiókok, mint például a csak blobok tárolására alkalmas tárfiókok, az Azure Premium Storage és a ZRS-replikációval rendelkező általános célú tárolófiókok nem támogatják az üzenetsorokat és a táblákat. Ezek a fiókok nem jelennek meg a Storage-fiók panelen a függvényalkalmazások létrehozásakor.

>[!NOTE]
>A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>További lépések

Bár az Azure Portal megkönnyíti a Függvények létrehozását és kipróbálását, [helyi fejlesztést](functions-develop-local.md)ajánlunk. Miután létrehozott egy függvényalkalmazást a portálon, továbbra is hozzá kell adnia egy függvényt. 

> [!div class="nextstepaction"]
> [HTTP-vel aktivált függvény hozzáadása](functions-create-first-azure-function.md#create-function)
