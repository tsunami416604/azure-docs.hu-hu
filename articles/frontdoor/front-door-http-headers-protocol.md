---
title: A HTTP-fejlécek protokoll-támogatása az Azure bejárati ajtóban | Microsoft Docs
description: Ez a cikk a bejárati ajtó által támogatott HTTP-fejlécek protokollait ismerteti.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 6864a854215d899043607b3d01cffbd343ee7751
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399514"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>A HTTP-fejlécek protokoll-támogatása az Azure-beli bejárati ajtón
Ez a cikk azt a protokollt ismerteti, amelyet a bevezető ajtó támogat a hívási útvonal részeivel (lásd a képet). A következő szakaszokban további információkat talál a bejárati ajtó által támogatott HTTP-fejlécekről.

![Azure bejárati ajtó HTTP-fejlécek protokollja][1]

>[!IMPORTANT]
>A bejárati ajtó nem tanúsít olyan HTTP-fejléceket, amelyek nincsenek dokumentálva.

## <a name="client-to-front-door"></a>Ügyfélről a bejárat felé
A bejárati ajtó fogadja a legtöbb fejlécet a bejövő kérelemből a módosítás nélkül. Egyes fenntartott fejlécek el lesznek távolítva a bejövő kérelemből, ha el vannak küldve, beleértve az X-FD-* előtaggal rendelkező fejléceket is.

## <a name="front-door-to-backend"></a>A háttérbe való bejárati

A bejárati ajtó a bejövő kérelmek fejléceit is tartalmazza, kivéve, ha a korlátozások miatt el van távolítva. A bejárati ajtó a következő fejléceket is hozzáadja:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Keresztül |  A: 1,1 Azure-on keresztül </br> A bejárati ajtó hozzáadja az ügyfél HTTP-verzióját, majd az *Azure* -t a on keresztül fejléc értékeként. Ez a fejléc azt jelzi, hogy az ügyfél HTTP-verziója, és a bejárati ajtó egy köztes címzett volt az ügyfél és a háttér közötti kérelemhez.  |
| X – Azure – Ügyfélip | X-Azure-Ügyfélip: 127.0.0.1 </br> A feldolgozott kérelemhez társított ügyfél IP-címét jelöli. Egy proxytól érkező kérés például hozzáadhatja az X által továbbított-for fejlécet, hogy jelezze az eredeti hívó IP-címét. |
| X – Azure – SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Azt a szoftvercsatorna IP-címet jelöli, amely a jelenlegi kérelemből származó TCP-kapcsolathoz tartozik. Előfordulhat, hogy a kérelem ügyfél-IP-címe nem egyenlő a szoftvercsatorna IP-címével, mert azt egy felhasználó önkényesen felülírhatja.|
| X – Azure-ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Egy egyedi hivatkozási sztring, amely a bejárati ajtó által kiszolgált kéréseket azonosítja. A rendszer a hozzáférési naplók keresésére, valamint a hibaelhárítás szempontjából kritikus fontosságú megoldására szolgál.|
| X – Azure – RequestChain |  X-Azure-RequestChain: ugrások = 1 </br> Az a fejléc, amelyet a bejárati ajtó használ a kérési hurkok észlelésére, és a felhasználók nem hozhatnak függőséget. |
| X – továbbított – a következőhöz: | X – továbbított – a következőhöz: 127.0.0.1 </br> Az X-továbbított-for (XFF) HTTP-fejléc mező gyakran azonosítja a webkiszolgálóhoz a HTTP-proxyn vagy a Load balanceren keresztül csatlakozó ügyfél származó IP-címét. Ha van meglévő XFF-fejléc, akkor a bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címét, vagy hozzáadja a XFF-fejlécet az ügyfél szoftvercsatorna IP-címéhez. |
| X-továbbított-gazdagép | X-továbbított-gazdagép: contoso.azurefd.net </br> Az X-Forwarded-Host HTTP-fejléc mező az ügyfél által a gazdagép HTTP-kérelmének fejlécében kért eredeti gazdagép azonosítására szolgáló közös módszer. Ennek az az oka, hogy az előtérben lévő gazdagép neve eltérhet a kérést kezelő háttér-kiszolgálótól. |
| X – továbbított – proto | X – továbbított – proto: http </br> Az X-továbbított-proto HTTP-fejléc mező gyakran használatos a HTTP-kérések kezdeményező protokolljának azonosítására, mert a bejárati ajtó a konfiguráció alapján a HTTPS használatával kommunikálhat a háttérrel. Ez akkor is igaz, ha a fordított proxyra irányuló kérelem HTTP. |
| X-FD-HealthProbe | Az X-FD-HealthProbe HTTP-fejléc mező a bejárati állapot azonosítására szolgál. Ha ez a fejléc 1 értékre van beállítva, a kérelem állapota az állapot. Akkor használhatja, ha szigorú hozzáférést szeretne elérni az adott bejárati ajtóról az X-Forwarded-Host fejléc mezővel. |
|X – Azure – FDID | X-Azure-FDID fejléc: 437c82cd-360A-4a54-94c3-5ff707647783 </br> Ez a mező olyan frontdoorID tartalmaz, amelyek segítségével azonosítható, hogy a bejövő kérelem melyik bejárati ajtótól származik. Ezt a mezőt a bejárati ajtó szolgáltatás tölti fel. | 

## <a name="front-door-to-client"></a>Első ajtó az ügyfélnek

A háttérbeli bejáratra küldött fejlécek is át lesznek adva az ügyfélnek. A következőkben a bejárati ajtóról az ügyfeleknek továbbított fejlécek találhatók.

| Fejléc  | Példa |
| ------------- | ------------- |
| X – Azure-ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy egyedi hivatkozási karakterlánc, amely a bejárati ajtó által kiszolgált kéréseket azonosítja. Ez kritikus fontosságú a hibaelhárításhoz, mivel a hozzáférési naplók keresésére szolgál.|

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
