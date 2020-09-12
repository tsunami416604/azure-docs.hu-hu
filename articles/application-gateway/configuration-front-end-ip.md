---
title: Azure Application Gateway előtér-IP-cím konfigurálása
description: Ez a cikk az Azure Application Gateway előtér-IP-címének konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: d6cfac7f0fb3939e57ce64f552556138ce9feacd
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652976"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway előtér-IP-cím konfigurálása

Beállíthatja, hogy az Application Gateway nyilvános IP-címmel, magánhálózati IP-címmel vagy mindkettővel rendelkezzen. Egy nyilvános IP-címet kell megadni, ha a háttérrendszer futtatásakor az ügyfeleknek internetre irányuló virtuális IP-címmel (VIP) keresztül kell elérniük az internetet.

## <a name="public-and-private-ip-address-support"></a>Nyilvános és magánhálózati IP-cím támogatása

A Application Gateway v2 jelenleg nem támogatja csak a magánhálózati IP-módot. A következő kombinációkat támogatja:

* Magánhálózati IP-cím és nyilvános IP-cím
* Csak nyilvános IP-cím

További információ: [Application Gatewayokkal kapcsolatos gyakori kérdések](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Nem szükséges egy nyilvános IP-cím egy olyan belső végponthoz, amely nem érhető el az interneten. Ezt nevezzük *belső terheléselosztó-* (ILB-) végpontnak vagy privát előtér-IP-nek. Az Application Gateway ILB olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. A többrétegű alkalmazások szolgáltatásai és szintjei is hasznosak egy olyan biztonsági határon belül, amely nem érhető el az interneten, azonban a ciklikus multiplexelés, a munkamenet-stickiion vagy a TLS-lezárást igényli.

Csak egy nyilvános IP-cím vagy egy magánhálózati IP-cím támogatott. Az előtérbeli IP-címet az Application Gateway létrehozásakor választhatja ki.

- Nyilvános IP-cím esetén létrehozhat egy új nyilvános IP-címet, vagy használhat egy meglévő nyilvános IP-címet az Application Gateway szolgáltatással megegyező helyen. További információ: statikus vagy [dinamikus nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Privát IP-cím esetén megadhat egy magánhálózati IP-címet abban az alhálózatban, amelyben az Application Gateway létrejött. Ha nem ad meg ilyet, a rendszer automatikusan kijelöl egy tetszőleges IP-címet az alhálózatból. A kiválasztott IP-cím típusa (statikus vagy dinamikus) később nem módosítható. További információ: [Application Gateway létrehozása belső terheléselosztó használatával](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Az előtér-IP-cím egy *figyelőhöz*van társítva, amely az ELŐTÉR-IP-címen érkező kérelmeket ellenőrzi.

## <a name="next-steps"></a>Következő lépések

- [A figyelő konfigurációjának megismerése](configuration-listeners.md)