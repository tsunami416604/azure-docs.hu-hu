---
title: Protokoll-támogatás http-fejlécekhez az Azure Front Door ban | Microsoft dokumentumok
description: Ez a cikk a Bejárati ajtó által támogatott HTTP-fejlécprotokollokat ismerteti.
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471676"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protokoll-támogatás http-fejlécekhez az Azure Bejárati ajtajában
Ez a cikk ismerteti a protokollt, amelyet a Bejárati ajtó a hívási útvonal egyes részeivel támogat (lásd a képet). A következő szakaszok további információt nyújtanak a Bejárati ajtó által támogatott HTTP-fejlécekről.

![Azure Bejárati ajtó HTTP-fejlécek protokollja][1]

>[!IMPORTANT]
>A Bejárati ajtó nem hitelesíti azokat a HTTP-fejléceket, amelyek itt nincsenek dokumentálva.

## <a name="client-to-front-door"></a>Ügyfél a bejárati ajtó
A Bejárati ajtó a legtöbb fejlécet elfogadja a bejövő kérelemből anélkül, hogy módosítaná őket. Egyes fenntartott fejlécek törlődnek a bejövő kérelemből, ha elküldték őket, beleértve az X-FD-* előtaggal rendelkező fejléceket is.

## <a name="front-door-to-backend"></a>Bejárati ajtó a háttér-tod

A Bejárati ajtó a bejövő kérelmek fejléceit tartalmazza, kivéve, ha korlátozások miatt távolítják el őket. Bejárati ajtó is hozzáteszi, a következő fejlécek:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Via |  Keresztül: 1.1 Azure </br> A Bejárati ajtó hozzáadja az ügyfél HTTP-verzióját, majd az *Azure-t* a Via fejléc értékeként. Ez a fejléc az ügyfél HTTP-verzióját jelzi, és azt, hogy a Bejárati ajtó az ügyfél és a háttérrendszer közötti kérelem köztes címzettje volt.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> A feldolgozás alatt lévő kérelemhez társított ügyfél IP-címét jelöli. Egy proxytól érkező kérelem például hozzáadhatja az X-Forwarded-For fejlécet az eredeti hívó IP-címének jelzésére. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> A TCP-kapcsolathoz társított szoftvercsatorna IP-címét jelöli, amelyből az aktuális kérelem származik. Előfordulhat, hogy a kérelem ügyfélIP-címe nem egyezik meg a szoftvercsatorna IP-címével, mert a felhasználó önkényesen felülírhatja.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAABKMMOJBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3Yzgy2QTMzYwYS00YTU0LTk0YzMTNZZM3Nzgz </br> Egyedi hivatkozási karakterlánc, amely azonosítja a Bejárati ajtó által kiszolgált kérelmet. A hozzáférési naplók keresésére és a hibaelhárítás kritikus állapotára szolgál.|
| X-Azure-RequestChain |  X-Azure-RequestChain: ugrás=1 </br> A Bejárati ajtó által a kérelemhurkok észlelésére használt fejléc, és a felhasználók nem vehetnek tőle függőséget. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Az X-Forwarded-For (XFF) HTTP fejlécmező gyakran azonosítja a webkiszolgálóhoz HTTP-proxyn vagy terheléselosztón keresztül csatlakozó ügyfél eredeti IP-címét. Ha van egy meglévő XFF-fejléc, majd a Bejárati ajtó hozzáfűzi az ügyfélszoftvercsatorna IP-címét, vagy hozzáadja az XFF-fejlécet az ügyfélszoftverip-címhez. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Az X-Forwarded-Host HTTP fejlécmező egy gyakori módszer az ügyfél által az állomás HTTP-kérelemfejlécében kért eredeti állomás azonosítására. Ennek az az oka, hogy a bejárati ajtóállomásneve eltérhet a kérést kezelő háttérkiszolgálóesetében. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Az X-Forwarded-Proto HTTP fejlécmező gyakran használják a HTTP-kérelmek eredeti protokolljának azonosítására, mivel a Bejárati ajtó konfiguráció alapján https használatával kommunikálhat a háttérrendszerrel. Ez akkor is igaz, ha a fordított proxyra vonatkozó kérelem HTTP. |
| X-FD-egészségügyi szonda | Az X-FD-HealthProbe HTTP fejlécmezője a Bejárati ajtó állapotminta azonosítására szolgál. Ha ez a fejléc 1-re van állítva, a kérelem állapotminta. Akkor használhatja, ha szigorú hozzáférést szeretne az adott bejárati ajtóról az X-Forwarded-Host fejlécmezővel. |

## <a name="front-door-to-client"></a>Bejárati ajtó az ügyfélnek

A háttérrendszerből a Bejárati ajtóra küldött fejlécek is átkerülnek az ügyfélhez. A következő fejlécek küldött bejárati ajtó az ügyfeleknek.

| Fejléc  | Példa |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAABKMMOJBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3Yzgy2QTMzYwYS00YTU0LTk0YzMTNZZM3Nzgz* </br> Ez egy egyedi hivatkozási karakterlánc, amely azonosítja a Bejárati ajtó által kiszolgált kérelmet. Ez kritikus fontosságú a hibaelhárítás, mivel a hozzáférési naplók keresésére használják.|

## <a name="next-steps"></a>További lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [A bejárati ajtó működése](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
