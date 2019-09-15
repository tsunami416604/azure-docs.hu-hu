---
title: Tárolóbiztonság
titleSuffix: Azure Cognitive Services
description: Útmutató a tároló biztonságossá tételéhez
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994957"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services tároló biztonsága

Az alkalmazások fejlesztésekor a biztonságnak elsődleges fókusznak kell lennie. A biztonság fontossága a siker mérőszáma. Cognitive Services tárolókat tartalmazó szoftver-megoldás tervezésekor elengedhetetlen az Ön számára elérhető korlátozások és képességek megismerése. További információ: [Azure Security][az-security].

> [!IMPORTANT]
> Alapértelmezés szerint nincs *Biztonság* a Cognitive Services Container API-ban. Ennek az az oka, hogy a tároló a legtöbb esetben egy olyan Pod részeként fut, amely egy hálózati híddal kívülről van védve. Azonban engedélyezhető a hitelesítés, amely azonos módon működik a [felhőalapú Cognitive Serviceshoz][request-authentication]való hozzáféréskor használt hitelesítéssel.

Az alábbi ábra az alapértelmezett és a nem **biztonságos** megközelítést mutatja be:

![Tárolóbiztonság](../media/container-security.svg)

Alternatív és *biztonságos* megközelítésként a Cognitive Services tárolók felhasználói a tárolót egy előtérben lévő összetevővel bővíthetik, így a tároló végpontja magánjellegű marad. Vegyünk egy olyan forgatókönyvet, amelyben a [Istio][istio] -t bejövő átjáróként használjuk. A Istio támogatja a HTTPS/SSL és az ügyféltanúsítvány-alapú hitelesítést. Ebben a forgatókönyvben a Istio előtér teszi elérhetővé a tárolók hozzáférését, és bemutatja az Istio-mel előzetesen engedélyezett ügyféltanúsítványt.

[Nginx][nginx] egy másik népszerű választás ugyanabban a kategóriában. Mind a Istio, mind az Nginx Service meshként működik, és további funkciókat kínál, például a terheléselosztást, az útválasztást és a díjszabást.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
