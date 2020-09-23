---
title: Oktatóanyag – az Azure Spring Cloud integrálása az Azure Load Balance-megoldásokkal
description: Az Azure Spring Cloud integrálása az Azure Load Balance-megoldásokkal
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 02772f153cdda7e3f3c866c727d589e755e19033
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906932"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Az Azure Spring Cloud és az Azure Load Balancer-megoldások integrálása

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud támogatja a szolgáltatásait az Azure-ban.  A növekvő vállalkozások több adatközpontot is igényelhetnek az Azure Spring Cloud több példányának felügyeletével.

Az Azure már különböző terheléselosztási megoldásokat biztosít. Az Azure Spring Cloud az Azure Load-Balance megoldásokkal való integrálására három lehetőség áll rendelkezésre:

1.  Az Azure Spring Cloud integrálása az Azure Traffic Manager
2.  Az Azure Spring Cloud integrálása az Azure app Gateway használatával
3.  Az Azure Spring Cloud integrálása az Azure bejárati ajtóval

## <a name="prerequisites"></a>Előfeltételek

* Azure Spring-felhő: [Azure Spring Cloud-szolgáltatás létrehozása](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart)
* Azure Traffic Manager: [Traffic Manager létrehozása](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure app Gateway: [Application Gateway létrehozása](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Azure bejárati ajtó: [bejárati ajtó létrehozása](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Az Azure Spring Cloud integrálása az Azure Traffic Manager

Az Azure Spring Cloud és a Traffic Manager együttes integrálásához adja hozzá nyilvános végpontait Traffic Manager-végpontként, majd konfigurálja a Traffic Manager és az Azure Spring Cloud egyéni tartományát.

### <a name="add-endpoint-in-traffic-manager"></a>Végpont hozzáadása Traffic Manager
Végpontok hozzáadása a Traffic Managerben:
1.  *Külső végpontként*adja meg a **típust** .
1.  Minden Azure Spring Cloud nyilvános végpont teljes tartományneve (FQDN).
1. Kattintson az **OK** gombra.

    ![Traffic Manager 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Egyéni tartomány konfigurálása
A konfiguráció befejezése:
1.  Jelentkezzen be a tartományi szolgáltató webhelyére, és hozzon létre egy CNAME rekord-hozzárendelést az egyéni tartományból a Traffic Manager Azure alapértelmezett tartománynevére.
1.  Kövesse [az egyéni tartomány hozzáadása az Azure Spring Cloud-hoz](spring-cloud-tutorial-custom-domain.md)című témakör utasításait.
1. Adja hozzá a fenti egyéni tartományi kötést a Traffic Managerhez az Azure Spring Cloud megfelelő app Service-hez, és töltse fel az SSL-tanúsítványt.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Az Azure Spring Cloud integrálása az Azure app Gateway használatával

Az Azure Spring Cloud Service-sel való integrációhoz végezze el a következő konfigurációkat:

### <a name="configure-backend-pool"></a>Háttérbeli készlet konfigurálása
1. Adja meg a **cél típusát** *IP-címként* vagy *FQDN*-ként.
1. Adja meg az Azure Spring Cloud nyilvános végpontját.

    ![1. alkalmazás-átjáró](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Egyéni mintavétel hozzáadása
1. Válassza az **állapot** -tesztek lehetőséget, majd a **Hozzáadás** gombra kattintva nyissa meg **az egyéni mintavétel** párbeszédpanelt. 
1. A lényeg az, hogy válassza az *Igen* lehetőséget az állomásnév kiválasztásához **a háttérbeli http-beállítások** lehetőségnél.

    ![2. alkalmazás-átjáró](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Http-beállítás konfigurálása
1.  Válassza a **http-beállítások** , majd a **Hozzáadás** lehetőséget a http-beállítás hozzáadásához.
1.  **Felülbírálás új állomásnévvel:** válassza az *Igen*lehetőséget.
1.  **Állomásnév felülbírálása**: válassza ki **az állomásnév kiválasztása a háttér**céljából lehetőséget.
1.  **Egyéni mintavétel használata**: válassza az *Igen* lehetőséget, és válassza ki a fent létrehozott egyéni mintavételt.

    ![3. alkalmazás-átjáró](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Az Azure Spring Cloud integrálása az Azure bejárati ajtóval

Az Azure Spring Cloud Service integrálásával és a háttérbeli készlet konfigurálásával 
1. **Háttér-készlet hozzáadása**.
1. Adja meg a háttér-végpontot a gazdagép hozzáadásával.

    ![Első ajtó 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Adja meg a **háttérbeli gazdagép típusát** *Egyéni gazdagépként*.
1.  Adja meg az Azure Spring Cloud nyilvános végpontok teljes tartománynevét a **háttérbeli gazdagép nevében**.
1.  Fogadja el az alapértelmezett **háttérbeli állomásfejléc-fejlécet** , amely megegyezik a **háttérbeli gazdagép nevével**.

    ![Bejárati ajtó 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Következő lépések
* [Traffic Manager létrehozása](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [Application Gateway létrehozása](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [Bejárati ajtó létrehozása](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
