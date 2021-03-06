---
title: Függvényalkalmazás létrehozása az Azure Portal használatával
description: Hozzon létre egy új Function alkalmazást az Azure-ban a portálon.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 8d19a269903de309bf219c2546fa70c3abe7be10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093589"
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

A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. A Functions a Storage-ot használja olyan belső műveletekre, mint például az eseményindítók kezelése és a függvénykivételek naplózása. Egyes Storage-fiókok, mint például a csak blobok tárolására alkalmas tárfiókok, az Azure Premium Storage és a ZRS-replikációval rendelkező általános célú tárolófiókok nem támogatják az üzenetsorokat és a táblákat. 

A nem támogatott típusú fiókokat a rendszer kiszűri, amikor létrehoz egy Function alkalmazást a Azure Portal. A portál lehetővé teszi a meglévő Storage-fiók használatát is, ha a fiók ugyanabban a régióban található, mint a létrehozandó Function alkalmazás. Ha valamilyen okból szeretné megsérteni a Function alkalmazás által az adott régióban használt Storage-fiókkal kapcsolatos bevált gyakorlatot, akkor a Function alkalmazást a portálon kívül kell létrehoznia. 

>[!NOTE]
>A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre. 

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Következő lépések

Míg a Azure Portal megkönnyíti a függvények létrehozását és kipróbálását, javasoljuk a [helyi fejlesztést](functions-develop-local.md). Miután létrehozta a Function alkalmazást a portálon, továbbra is hozzá kell adnia egy függvényt. 

> [!div class="nextstepaction"]
> [HTTP által aktivált függvény hozzáadása](functions-create-first-azure-function.md#create-function)
