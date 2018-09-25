---
title: Azure bejárati ajtajának szolgáltatás – HTTP-fejlécek protokoll támogatása |} A Microsoft Docs
description: Ez a cikk segít megérteni a HTTP-fejléc támogatott protokollok bejárati ajtajának szerint
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038850"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure bejárati ajtajának szolgáltatás – HTTP-fejlécek protokoll támogatása
Ez a dokumentum ismerteti a protokoll, amely Azure bejárati ajtajának Service támogatja a hívás elérési különböző részeivel folytatott leírtak szerint az alábbi képen. Az alábbi szakaszok részletesebb elemzéseket kaphat a HTTP-fejléceket, amely támogatja a bejárati ajtajának.

![Az Azure bejárati ajtó a HTTP-fejlécek protokoll][1]

>[!WARNING]
>Azure bejárati ajtajának szolgáltatás nem ad garanciák bármilyen HTTP-fejlécek, amelyek nem szerepelnek itt.

## <a name="1-client-to-front-door"></a>1. Ügyfél számára a bejárati ajtó
Bejárati ajtajának elfogadja a bejövő kérelem legtöbb fejléc (nélkül módosíthassák azokat), azonban nincsenek néhány fenntartott fejléc, ha a Küldés a bejövő kérelem eltávolítani. Ez magában foglalja a fejlécek az az alábbi előtagokat:
 - X-FD *
 - X-MS *

## <a name="2-front-door-to-backend"></a>2. Bejárati ajtajának háttérrendszerhez

Bejárati ajtajának a fejlécek, a bejövő kérelem fogja tartalmazni, kivéve, ha a fent említett korlátozások miatt eltávolította azokat. Bejárati ajtajának is hozzáad a következő fejléceket:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Ez egy karakterláncérték egyedi hivatkozás, amely azonosítja a bejárati ajtajának által kiszolgált kérés. Rendkívül fontos, így a hozzáférési naplók keresése a hibaelhárításhoz.|
| X-MS-RequestChain |  *X-MS-RequestChain: ugrások = 1* </br> Ez egy fejlécet, amely bejárati ajtajának kérelem hurkok észleléséhez használja, és azon felhasználók függőség sem tarthat tovább. |
| X-MS-n keresztül |  *X-MS-n keresztül: Azure* </br> Ez jelzi, hogy az Azure/bejárati ajtajának volt-e az ügyfél és a háttérrendszer között a kérelem egy köztes címzett bejárati ajtajának által hozzáadott. |

## <a name="3-front-door-to-client"></a>3. Ügyfél számára a bejárati ajtajának

Az alábbiakban az ügyfelek számára a bejárati ajtajának küldött fejlécek. A háttérrendszer a bejárati ajtajának küldött fejlécek, valamint az ügyfél keresztül továbbítódnak.

| Fejléc  | Példa |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Ez egy karakterláncérték egyedi hivatkozás, amely azonosítja a bejárati ajtajának által kiszolgált kérés. Rendkívül fontos, így a hozzáférési naplók keresése a hibaelhárításhoz.|

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png