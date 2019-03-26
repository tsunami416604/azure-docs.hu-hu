---
title: Az Azure bejárati ajtajának szolgáltatás – támogatja a HTTP-fejlécek |} A Microsoft Docs
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
ms.openlocfilehash: 40bfdfc3837da12f62864433508482a65def291c
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407317"
---
# <a name="azure-front-door-service---http-headers-support"></a>Az Azure bejárati ajtajának szolgáltatás – támogatja a HTTP-fejlécek
Ez a dokumentum ismerteti a protokoll, amely Azure bejárati ajtajának Service támogatja a hívás elérési különböző részeivel folytatott leírtak szerint az alábbi képen. Az alábbi szakaszok részletesebb elemzéseket kaphat a HTTP-fejléceket, amely támogatja a bejárati ajtajának.

![Az Azure bejárati ajtó a HTTP-fejlécek protokoll][1]

>[!WARNING]
>Azure bejárati ajtajának szolgáltatás nem ad garanciák bármilyen HTTP-fejlécek, amelyek nem szerepelnek itt.

## <a name="1-client-to-front-door"></a>1. Ügyfél számára a bejárati ajtó
Bejárati ajtajának elfogadja a bejövő kérelem legtöbb fejléc (nélkül módosíthassák azokat), azonban nincsenek néhány fenntartott fejléc, ha a Küldés a bejövő kérelem eltávolítani. Ez magában foglalja a fejlécek az az alábbi előtagokat:
 - X - FD-*

## <a name="2-front-door-to-backend"></a>2. Bejárati ajtajának háttérrendszerhez

Bejárati ajtajának a fejlécek, a bejövő kérelem fogja tartalmazni, kivéve, ha a fent említett korlátozások miatt eltávolította azokat. Bejárati ajtajának is hozzáad a következő fejléceket:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Ezzel |  *Keresztül: 1.1 azure* </br> Bejárati ajtajának hozzáadja az ügyfél HTTP-verzió Via fejléc értéke "Azure" követ. Azure bejárati ajtó hozzáadja azt jelzi, az ügyfél HTTP-verzió és az ügyfél és a háttérrendszer között a kérelem egy köztes címzett.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> A kérés feldolgozása folyamatban társított "ügyfél" IP-címet jelenti. Például egy proxy érkező kérelmet előfordulhat, hogy adja hozzá az X-továbbított – a fejléc jelzi az eredeti hívó IP-címét. |
| X-Azure-SocketIP | *X-Azure-SocketIP: 127.0.0.1* </br> Származik-e az aktuális kérelem TCP-kapcsolathoz társított szoftvercsatorna IP-címet jelenti. A kérés ügyfél IP-cím nem lehet egyenlő a szoftvercsatorna IP-címére, mivel lehet önkényesen felülírni azt a felhasználó által.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy karakterláncérték egyedi hivatkozás, amely azonosítja a bejárati ajtajának által kiszolgált kérés. Rendkívül fontos, így a hozzáférési naplók keresése a hibaelhárításhoz.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: ugrások = 1* </br> Ez egy fejlécet, amely bejárati ajtajának kérelem hurkok észleléséhez használja, és azon felhasználók függőség sem tarthat tovább. |
| X-továbbított-számára | *X-továbbított-esetén: 127.0.0.1* </br> A X-Forwarded-For (XFF) HTTP-fejléc mező az eredeti IP-címét, egy HTTP-proxy vagy terheléselosztó keresztül csatlakozó ügyfél azonosítására szolgáló gyakori módja. Ha egy meglévő XFF fejlécre, majd a bejárati ajtajának az ügyfél a szoftvercsatorna IP hozzáfűzi azt más hozzáadja az ügyfél IP-szoftvercsatorna XFF fejléc. |
| X-Forwarded-Host | *X továbbított állomás: contoso.azurefd.net* </br> A X továbbított fogadó HTTP-fejléc mező a módszert gyakran az eredeti gazdára, mivel a kérést a háttérkiszolgálónak eltérhetnek a bejárati ajtajának megadott állomás nevét a fogadó HTTP-kérés fejlécében, ügyfél által kért azonosításához. |
| X-Forwarded-Proto | *X továbbított Proto: http* </br> A X továbbított Proto HTTP-fejléc mező a módszert gyakran az eredeti protokoll HTTP-kérés azonosításához, mivel a konfigurációtól függően bejárati ajtajának kommunikálhatnak a HTTPS-en keresztül, akkor is, ha a kérés és a fordított proxy HTTP-háttérrendszerrel. |

## <a name="3-front-door-to-client"></a>3. Ügyfél számára a bejárati ajtajának

Az alábbiakban az ügyfelek számára a bejárati ajtajának küldött fejlécek. A háttérrendszer a bejárati ajtajának küldött fejlécek, valamint az ügyfél keresztül továbbítódnak.

| Fejléc  | Példa |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy karakterláncérték egyedi hivatkozás, amely azonosítja a bejárati ajtajának által kiszolgált kérés. Rendkívül fontos, így a hozzáférési naplók keresése a hibaelhárításhoz.|

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png