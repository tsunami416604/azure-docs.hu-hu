---
title: Azure Traffic Manager | Microsoft Docs
description: Ez a cikk az Azure Traffic Managerről nyújt áttekintést. Ismerje meg, hogy ez-e a megfelelő választás az alkalmazáshoz érkező felhasználói forgalom terheléselosztásához.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: a8504cea3af6eb7633cf6738a73f757767d83138
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534316"
---
# <a name="what-is-traffic-manager"></a>Mi az a Traffic Manager?
Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít.

A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Traffic Manager különböző [forgalom-útválasztási módszereket](traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha Transport Layer Security (TLS) protokoll-lezárást („SSL-kiszervezés”) vagy per-HTTP/HTTPS kérelmeket vagy alkalmazásréteg-feldolgozást keres, tekintse át az [Application Gatewayt](../application-gateway/application-gateway-introduction.md) ismertető cikket. Ha regionális terheléselosztás keres, tekintse át [Load Balancer](../load-balancer/load-balancer-overview.md). A végpontok közötti forgatókönyvek esetében előnyt jelenthet ezen megoldások igény szerinti kombinációja.

A TRAFFIC Manager a következő funkciókat kínálja:

## <a name="increase-application-availability"></a>Alkalmazás rendelkezésre állásának növelése

A Traffic Manager lehetővé teszi a kritikus fontosságú alkalmazások magas rendelkezésre állását azáltal, hogy figyeli a végpontokat, és automatikus feladatátvételt biztosít, ha egy végpont leáll.
    
## <a name="improve-application-performance"></a>Az alkalmazásteljesítmény javítása

Az Azure lehetővé teszi a felhőszolgáltatások vagy webhelyek futtatását a világ különböző pontjain található adatközpontokban. A Traffic Manager javítja az alkalmazások válaszkészségét azáltal, hogy az ügyfél számára a legalacsonyabb hálózati késéssel rendelkező végpontra irányítja a forgalmat.

## <a name="perform-service-maintenance-without-downtime"></a>Állásidő nélküli rendszerkarbantartás végrehajtása

Állásidő nélkül hajthat végre tervezett karbantartási műveleteket az alkalmazásokon. A TRAFFIC Manager forgalom alternatív végpontokat irányíthatók, amíg folyamatban van a karbantartás.

## <a name="combine-hybrid-applications"></a>Hibrid alkalmazások kombinációja

A Traffic Manager támogatja a külső, nem Azure-végpontokat, ami lehetővé teszi a hibrid felhőalapú és helyszíni telepítéssel történő használatot, beleértve a „[felhőbe irányítás](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”, „felhőbe migrálás” és „felhő általi feladatátvétel” eseteket is.

## <a name="distribute-traffic-for-complex-deployments"></a>Forgalom elosztása összetett üzemelő példányokhoz

Használatával [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md), többféle forgalom-útválasztási módszer kombinálható is nagyobb, összetettebb központi telepítések igényei szerint méretezhető kifinomult és rugalmas szabályok létrehozásához.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg a [Traffic Manager díjszabását](https://azure.microsoft.com/pricing/details/traffic-manager/) ismertető részt.


## <a name="next-steps"></a>További lépések

- Útmutató a [Traffic Manager-profil létrehozásához](traffic-manager-create-profile.md).
- Útmutató a [Traffic Manager működéséhez](traffic-manager-how-it-works.md).
- Tekintse meg a [gyakori kérdések](traffic-manager-FAQs.md) részt a Traffic Managerrel kapcsolatban.




