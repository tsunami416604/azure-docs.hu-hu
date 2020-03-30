---
title: Az Azure Spring Cloud geokatasztrófa-helyreállítása | Microsoft dokumentumok
description: Ismerje meg, hogyan védheti meg tavaszi felhőalkalmazását a regionális kimaradásoktól
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279142"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Az Azure Spring Cloud vész-helyreállítási

Ez a cikk néhány stratégiát ismertet, amelyekkel megvédheti az Azure Spring Cloud-alkalmazásokat az állásidőtől.  Bármely régióban vagy adatközpontban előfordulhat állásidő okozta regionális katasztrófák, de gondos tervezés csökkentheti az ügyfelekre gyakorolt hatást.

## <a name="plan-your-application-deployment"></a>Az alkalmazás telepítésének megtervezése

Az Azure Spring Cloud-alkalmazások egy adott régióban futnak.  Az Azure világszerte számos földrajzi területen működik. Az Azure-földrajzi terület a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzi területen belüli terület, amely egy vagy több adatközpontot tartalmaz.  Minden Azure-régió párosítva van egy másik régióval ugyanazon a földrajzi területen belül, és együtt egy regionális pár. Az Azure szerializálja a platformfrissítéseket (tervezett karbantartást) a regionális párok között, biztosítva, hogy minden párban egyszerre csak egy régió frissüljön. Több régiót érintő kimaradás esetén minden párban legalább egy régió lesz rangsorolva a helyreállításhoz.

A katasztrófák magas rendelkezésre állásának és védelmének biztosításához a Spring Cloud-alkalmazásokat több régióra kell telepítenie.  Az Azure a [párosított régiók](../best-practices-availability-paired-regions.md) listáját tartalmazza, így megtervezheti a tavaszi felhőbeli üzembe helyezéseket a regionális párok számára.  Azt javasoljuk, hogy a mikroszolgáltatási architektúra tervezésekor három fő tényezőt vegye figyelembe: a régió elérhetőségét, az Azure-ban párosított régiókat és a szolgáltatás rendelkezésre állását.

*  Régió elérhetősége: Válasszon a felhasználókhoz közeli földrajzi területet a hálózati késés és az átviteli idő minimalizálása érdekében.
*  Azure párosított régiók: Válassza ki a párosított régiók a kiválasztott földrajzi területen belül, hogy összehangolt platformfrissítéseket és rangsorolva helyreállítási erőfeszítéseket, ha szükséges.
*  A szolgáltatás elérhetősége: Döntse el, hogy a párosított régióknak meleg/meleg, meleg/meleg vagy meleg/hideg legyen-e.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Forgalom irányítása az Azure Traffic Manager használatával

[Az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) DNS-alapú terheléselosztást biztosít, és több régióban is eloszthatja a hálózati forgalmat.  Az Azure Traffic Manager segítségével a legközelebbi Azure Spring Cloud szolgáltatáspéldányhoz irányíthatja ügyfeleit.  A legjobb teljesítmény és redundancia érdekében irányítsa az összes alkalmazásforgalmat az Azure Traffic Manageren keresztül, mielőtt elküldene az Azure Spring Cloud szolgáltatásnak.

Ha az Azure Spring Cloud-alkalmazások több régióban, az Azure Traffic Manager segítségével szabályozhatja a forgalom áramlását az alkalmazások az egyes régiókban.  Definiáljon egy Azure Traffic Manager-végpontot minden szolgáltatáshoz a szolgáltatás IP-címhasználatával. Az ügyfeleknek csatlakozniuk kell egy Azure Traffic Manager DNS-névhez, amely az Azure Spring Cloud szolgáltatásra mutat.  Az Azure Traffic Manager terhelési kiegyensúlyozza a forgalmat a meghatározott végpontok között.  Ha egy katasztrófa ütközik egy adatközpont, az Azure Traffic Manager irányítja a forgalmat, hogy a régió a pár, biztosítva a szolgáltatás folytonosságát.