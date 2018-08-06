---
title: Azure Traffic Manager | Microsoft Docs
description: Ez a cikk az Azure Traffic Managerről nyújt áttekintést. Ismerje meg, hogy ez-e a megfelelő választás az alkalmazáshoz érkező felhasználói forgalom terheléselosztásához.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2018
ms.author: kumud
ms.openlocfilehash: 236137b87351e3c3a95c1103f7464256f41b9159
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397224"
---
# <a name="what-is-traffic-manager"></a>Mi az a Traffic Manager?
Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít.

A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Traffic Manager különböző [forgalom-útválasztási módszereket](traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha Transport Layer Security (TLS) protokoll-lezárást („SSL-kiszervezés”) vagy per-HTTP/HTTPS kérelmeket vagy alkalmazásréteg-feldolgozást keres, tekintse át az [Application Gatewayt](../application-gateway/application-gateway-introduction.md) ismertető cikket. Ha regionális elosztást keres, tekintse át a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikket. A végpontok közötti forgatókönyvek esetében előnyt jelenthet ezen megoldások igény szerinti kombinációja.

A Traffic Manager a következő funkciókat tartalmazza:

## <a name="increase-application-availability"></a>Alkalmazás rendelkezésre állásának növelése

A Traffic Manager lehetővé teszi a kritikus fontosságú alkalmazások magas rendelkezésre állását azáltal, hogy figyeli a végpontokat, és automatikus feladatátvételt biztosít, ha egy végpont leáll.
    
## <a name="improve-application-performance"></a>Az alkalmazásteljesítmény javítása

Az Azure lehetővé teszi a felhőszolgáltatások vagy webhelyek futtatását a világ különböző pontjain található adatközpontokban. A Traffic Manager javítja az alkalmazások válaszkészségét azáltal, hogy az ügyfél számára a legalacsonyabb hálózati késéssel rendelkező végpontra irányítja a forgalmat.

## <a name="perform-service-maintenance-without-downtime"></a>Állásidő nélküli rendszerkarbantartás végrehajtása

Állásidő nélkül hajthat végre tervezett karbantartási műveleteket az alkalmazásokon. A Traffic Manager alternatív végpontokra irányítja a forgalmat, amíg a karbantartás folyamatban van.

## <a name="combine-hybrid-applications"></a>Hibrid alkalmazások kombinációja

A Traffic Manager támogatja a külső, nem Azure-végpontokat, ami lehetővé teszi a hibrid felhőalapú és helyszíni telepítéssel történő használatot, beleértve a „[felhőbe irányítás](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”, „felhőbe migrálás” és „felhő általi feladatátvétel” eseteket is.

## <a name="distribute-traffic-for-complex-deployments"></a>Forgalom elosztása összetett üzemelő példányokhoz

[Beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md) használatával a forgalom-útválasztási módszerek kombinálhatóak úgy, hogy kifinomult és rugalmas szabályokat hozzanak létre a nagyobb, összetettebb üzemelő példányok igényeinek megfelelően.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg a [Traffic Manager díjszabását](https://azure.microsoft.com/pricing/details/traffic-manager/) ismertető részt.


## <a name="next-steps"></a>További lépések

- Útmutató a [Traffic Manager-profil létrehozásához](traffic-manager-create-profile.md).
- Útmutató a [Traffic Manager működéséhez](traffic-manager-how-it-works.md).
- Tekintse meg a [gyakori kérdések](traffic-manager-FAQs.md) részt a Traffic Managerrel kapcsolatban.




