---
title: Protokoll-támogatás a HTTP-fejlécekhez az Azure-beli előtérben szolgáltatásban | Microsoft Docs
description: Ez a cikk a bejárati szolgáltatás által támogatott HTTP-fejléceket ismerteti.
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
ms.openlocfilehash: 7c77527b7300c1149e96c94a4dbe122da226ac6d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382560"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>A HTTP-fejlécek protokoll-támogatása az Azure-beli előtérben szolgáltatásban
Ez a cikk azt a protokollt ismerteti, amelyet a bevezető ajtó szolgáltatás a hívási útvonal részeivel támogat (lásd a képet). A következő szakaszokban további információk találhatók a bejárati szolgáltatás által támogatott HTTP-fejlécekről.

![Azure bejárati ajtó szolgáltatás HTTP-fejlécek protokollja][1]

>[!IMPORTANT]
>A bejárati ajtó szolgáltatás nem tanúsít olyan HTTP-fejléceket, amelyek nincsenek dokumentálva.

## <a name="client-to-front-door-service"></a>Ügyfél és a bejárati ajtó szolgáltatás
A bejárati ajtó szolgáltatás a bejövő kérelem legtöbb fejlécét a módosítás nélkül fogadja el. Egyes fenntartott fejlécek el lesznek távolítva a bejövő kérelemből, ha el vannak küldve, beleértve az X-FD-* előtaggal rendelkező fejléceket is.

## <a name="front-door-service-to-backend"></a>Bejárati ajtó szolgáltatás a háttérrendszer számára

A befelé irányuló szolgáltatás a bejövő kérelmek fejléceit is tartalmazza, hacsak a korlátozások miatt nem távolítja el azokat. A bejárati ajtó a következő fejléceket is hozzáadja:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Keresztül |  A: 1,1 Azure-on keresztül </br> A bejárati ajtó hozzáadja az ügyfél HTTP-verzióját, majd az *Azure* -t a on keresztül fejléc értékeként. Ez azt jelzi, hogy az ügyfél HTTP-verziója, és a bejárati ajtó egy köztes címzett volt az ügyfél és a háttér közötti kérelemhez.  |
| X-Azure-ClientIP | X-Azure-Ügyfélip: 127.0.0.1 </br> A feldolgozott kérelemhez társított ügyfél IP-címét jelöli. Egy proxytól érkező kérés például hozzáadhatja az X által továbbított-for fejlécet, hogy jelezze az eredeti hívó IP-címét. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Azt a szoftvercsatorna IP-címet jelöli, amely a jelenlegi kérelemből származó TCP-kapcsolathoz tartozik. Előfordulhat, hogy a kérelem ügyfél-IP-címe nem egyenlő a szoftvercsatorna IP-címével, mert azt egy felhasználó önkényesen felülírhatja.|
| X-Azure-Ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Egy egyedi hivatkozási sztring, amely a bejárati ajtó által kiszolgált kéréseket azonosítja. A rendszer a hozzáférési naplók keresésére, valamint a hibaelhárítás szempontjából kritikus fontosságú megoldására szolgál.|
| X-Azure-RequestChain |  X-Azure-RequestChain: ugrások = 1 </br> Az a fejléc, amelyet a bejárati ajtó használ a kérési hurkok észlelésére, és a felhasználók nem hozhatnak függőséget. |
| X – továbbított – a következőhöz: | X – továbbított – a következőhöz: 127.0.0.1 </br> Az X-továbbított-for (XFF) HTTP-fejléc mező gyakran azonosítja a webkiszolgálóhoz a HTTP-proxyn vagy a Load balanceren keresztül csatlakozó ügyfél származó IP-címét. Ha van meglévő XFF-fejléc, akkor a bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címét, vagy hozzáadja a XFF-fejlécet az ügyfél szoftvercsatorna IP-címéhez. |
| X-továbbított-gazdagép | X-továbbított-gazdagép: contoso.azurefd.net </br> Az X-Forwarded-Host HTTP-fejléc mező az ügyfél által a gazdagép HTTP-kérelmének fejlécében kért eredeti gazdagép azonosítására szolgáló közös módszer. Ennek az az oka, hogy az előtérben lévő gazdagép neve eltérhet a kérést kezelő háttér-kiszolgálótól. |
| X-Forwarded-Proto | X – továbbított – proto: http </br> Az X-továbbított-proto HTTP-fejléc mező gyakran használatos a HTTP-kérések kezdeményező protokolljának azonosítására, mert a bejárati ajtó a konfiguráció alapján a HTTPS használatával kommunikálhat a háttérrel. Ez akkor is igaz, ha a fordított proxyra irányuló kérelem HTTP. |
| X-FD-HealthProbe | Az X-FD-HealthProbe HTTP-fejléc mező a bejárati állapot azonosítására szolgál. Ha ez a fejléc 1 értékre van beállítva, a kérelem állapota az állapot. Akkor használhatja, ha szigorú hozzáférést szeretne elérni az adott bejárati ajtóról az X-Forwarded-Host fejléc mezővel. |

## <a name="front-door-service-to-client"></a>A bejárati ajtó szolgáltatás az ügyfél felé

A háttérbeli bejáratra küldött fejlécek is át lesznek adva az ügyfélnek. A következőkben a bejárati ajtóról az ügyfeleknek továbbított fejlécek találhatók.

| Fejléc  | Példa |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy egyedi hivatkozási karakterlánc, amely a bejárati ajtó által kiszolgált kéréseket azonosítja. Ez kritikus fontosságú a hibaelhárításhoz, mivel a hozzáférési naplók keresésére szolgál.|

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
