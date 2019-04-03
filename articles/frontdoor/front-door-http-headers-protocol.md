---
title: Protokoll támogatása az Azure bejárati ajtajának Service HTTP-fejlécek |} A Microsoft Docs
description: Ez a cikk ismerteti a HTTP-fejléc protokollok bejárati ajtajának szolgáltatás által támogatott.
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861842"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Az Azure bejárati ajtajának Service HTTP-fejlécek protokoll támogatása
Ez a cikk ismerteti a bejárati ajtajának Service támogatja. a hívás elérési útjának részei a protokollt (lásd a képen). A következő szakaszok további információ a bejárati ajtajának szolgáltatás által támogatott HTTP-fejléceket.

![Az Azure bejárati ajtó a HTTP-fejlécek protokoll][1]

>[!IMPORTANT]
>Bejárati ajtajának szolgáltatás bármely itt nem dokumentált HTTP-fejlécek nem igazolja.

## <a name="client-to-front-door-service"></a>Ügyfél számára a bejárati ajtajának szolgáltatás
Bejárati ajtajának szolgáltatás a bejövő kérelem legtöbb fejléc elfogadja őket módosítása nélkül. Néhány fenntartott fejléc el lesznek távolítva a bejövő kérelem elküldve, ha a x fejlécekkel együtt – FD-* előtag.

## <a name="front-door-service-to-backend"></a>Bejárati ajtajának szolgáltatás háttérrendszerhez

Bejárati ajtajának szolgáltatás tartalmaz egy bejövő kérésnek fejlécet, kivéve, ha az eltávolított korlátozásai miatt. Bejárati ajtajának is a következő fejléceket ad hozzá:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Ezzel |  Keresztül: 1.1 azure </br> Bejárati ajtajának hozzáadja az ügyfél HTTP-verzió követ *Azure* a Via fejléc értékeként. Ez azt jelzi, hogy az ügyfél HTTP-verzió és bejárati ajtó a kérelem, az ügyfél és a háttérrendszer között egy köztes címzettnek.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> A kérés feldolgozása folyamatban társított ügyfél IP-címet jelenti. Például egy proxy érkező kérelmet előfordulhat, hogy adja hozzá az X-továbbított – a fejléc jelzi az eredeti hívó IP-címét. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> A szoftvercsatorna IP-címet, amely az aktuális kérelem származik a TCP-kapcsolathoz társított jelenti. A kérés ügyfél IP-cím nem feltétlenül szoftvercsatorna IP-címének egyenlő, mert lehet önkényesen felülírni azt a felhasználó által.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> A kérés kiszolgálása az internetszolgáltatójuk által bejárati ajtajának azonosító egyedi hivatkozást karakterlánc. Hozzáférési naplók keresése szolgál, kritikus fontosságú hibakeresési.|
| X-Azure-RequestChain |  X-Azure-RequestChain: ugrások = 1 </br> Fejléc, hogy bejárati ajtajának kérelem hurkok észleléséhez használja, és azon felhasználók függőség sem tarthat tovább. |
| X-továbbított-számára | X-továbbított-esetén: 127.0.0.1 </br> A X-Forwarded-For (XFF) HTTP-fejléc mező gyakran azonosítja az ügyfél számára egy HTTP-proxy vagy terheléselosztó keresztül csatlakozik az eredeti IP-címét. Ha egy meglévő XFF fejlécet, bejárati ajtajának fűzi hozzá az ügyfél a szoftvercsatorna IP-Címét, vagy az ügyfél IP-szoftvercsatorna XFF fejléc hozzáadása. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> A X továbbított fogadó HTTP-fejléc mező a módszert gyakran az eredeti gazdára, a fogadó HTTP-kérés fejlécében az ügyfél által kért azonosítására szolgál. Ennek az oka a kérést a háttérkiszolgálónak eltérhetnek a bejárati ajtó a gazdagép nevét. |
| X-Forwarded-Proto | X továbbított Proto: http </br> Az X továbbított Proto HTTP fejlécmezőt gyakran használják a azonosítani az eredeti protokoll HTTP-kérés, mert a bejárati ajtajának alapján, előfordulhat, hogy kommunikálni a háttérrendszer HTTPS-en keresztül. Ez igaz, akkor is, ha a kérés és a fordított proxy HTTP. |

## <a name="front-door-service-to-client"></a>Ügyfél bejárati ajtajának szolgáltatás

A háttérrendszer a bejárati ajtajának küldött fejlécek is továbbítja a rendszer az ügyfél számára. A következő fejlécek az bejárati ajtajának az ügyfeleknek küldött.

| Fejléc  | Példa |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy karakterláncérték egyedi hivatkozás, amely azonosítja a bejárati ajtajának által kiszolgált kérés. Ez fontos a hibaelhárítási, hozzáférési naplók keresése szolgál.|

## <a name="next-steps"></a>További lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [Hogyan működik a bejárati ajtó](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png