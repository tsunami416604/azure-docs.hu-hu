---
title: "Függvény-alkalmazás létrehozása az Azure portálról |} Microsoft Docs"
description: "Létrehoz egy új funkció alkalmazást az Azure App Service-ben a portálról."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 3e4eef12c1d19be6e0f1051caaa5cf2e98626aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Függvény-alkalmazás létrehozása az Azure-portálon

Azure függvény alkalmazások az Azure App Service-infrastruktúrát használja. Ez a témakör bemutatja, hogyan függvény alkalmazás létrehozása az Azure portálon. Egy függvény alkalmazást tartalmazó egyéni függvényei végrehajtása. Az App Service üzemeltetési terv egy függvény alkalmazást hoz létre, amikor a függvény app használhatja az App Service összes funkcióját.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Ha létrehoz egy függvény alkalmazást, adja meg egy érvényes **alkalmazásnév**, amely csak betűket, számokat és kötőjeleket tartalmazhat. Az aláhúzás (**_**) nem engedélyezett karakter.

A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. A tárfiók nevének egyedinek kell lennie az Azure rendszerben. 

A függvény alkalmazás létrehozása után létrehozhat egyéni függvényei egy vagy több különböző nyelveken. Hozzon létre funkciók [a portál használatával](functions-create-first-azure-function.md#create-function), [folyamatos üzembe helyezés](functions-continuous-deployment.md), vagy a [FTP-vel feltöltése](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Service-csomagok

Az Azure Functions van két különböző service-csomagokról: fogyasztás terv és az App Service-csomag. A felhasználási terv automatikusan lefoglalja számára a számítási teljesítményt, ha a kódja fut, a skála kibővített terhelés kezelése érdekében szükség szerint, és majd méretezik-a kód nem futtatásakor. Az App Service-csomag a függvény alkalmazás hozzáférést biztosít az App Service minden lehetőséget. Ki kell választania a service-csomag, a függvény alkalmazás létrehozása, és azt jelenleg nem módosul. További információkért lásd: [válassza ki az Azure Functions üzemeltetési terv](functions-scale.md).

Ha azt tervezi, JavaScript-funkcióként futhat az App Service-csomagot, válasszon egy tervet a kevesebb magok. További információkért lásd: a [függvények JavaScript hivatkozás](functions-reference-node.md#choose-single-core-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Tárolási fiókra vonatkozó követelmények

Egy függvény alkalmazást az App Service létrehozásakor vagy létre kell hoznia egy általános célú Azure Storage-fiók, amely támogatja a Blob, Queue és Table storage kapcsolódik. Belsőleg funkciók tárolást használ műveletek, például eseményindítók kezelése és naplózási funkciót végrehajtások. Néhány tárfiókok nem támogatják az üzenetsorok és táblák, például csak a blob storage-fiókok, a prémium szintű Azure Storage és a ZRS replikáció általános célú tárfiókok esetében. Ezek a fiókok kiszűri kívüli a Storage-fiók panelen egy függvény alkalmazás létrehozásakor.

>[!NOTE]
>A felhasználás üzemeltetési terv használatakor függvény kód és a kötés konfigurációs fájljainak Azure File storage a fő tárfiókban vannak tárolva. A fő storage-fiók törlésekor a tartalom törlődik, és nem állítható helyre.

Tárfióktípusokat kapcsolatos további információkért lásd: [az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#introducing-the-azure-storage-services). 

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]



