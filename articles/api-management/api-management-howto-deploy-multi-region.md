---
title: Azure API-kezelési szolgáltatások üzembe helyezése több Azure-régióban
titleSuffix: Azure API Management
description: Ismerje meg, hogyan helyezhet üzembe egy Azure API Management szolgáltatáspéldányt több Azure-régióban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442663"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban

Az Azure API Management támogatja a több régióra kiterjedő telepítést, amely lehetővé teszi az API-közzétevők számára, hogy egyetlen Azure API-felügyeleti szolgáltatást osszanak ki tetszőleges számú támogatott Azure-régióban. A több régióból álló szolgáltatás segít csökkenteni a földrajzilag elosztott API-felhasználók által érzékelt kérelemkésést, és javítja a szolgáltatás rendelkezésre állását, ha egy régió offline állapotba kerül.

Egy új Azure API Management szolgáltatás kezdetben csak egy [egységet][unit] tartalmaz egyetlen Azure-régióban, az elsődleges régióban. További régiók adhatók hozzá az Elsődleges vagy másodlagos régiókhoz. Az API Management átjáró-összetevő minden kiválasztott elsődleges és másodlagos régióban üzembe kerül. A bejövő API-kérelmek automatikusan a legközelebbi régióba lesznek irányítva. Ha egy régió offline állapotba kerül, az API-kérelmek automatikusan a sikertelen régió körül a következő legközelebbi átjáróra lesz irányítva.

> [!NOTE]
> Csak az API Management átjáró-összetevője van telepítve az összes régióban. A szolgáltatáskezelési összetevő és a fejlesztői portál csak az Elsődleges régióban található. Ezért az elsődleges régió kimaradása esetén a fejlesztői portálhoz való hozzáférés és a konfiguráció módosításának képessége (pl. API-k hozzáadása, szabályzatok alkalmazása) károsodik, amíg az elsődleges régió újra online állapotba nem kerül. Amíg az elsődleges régió offline elérhető, a másodlagos régiók továbbra is a számukra elérhető legújabb konfiguráció használatával szolgálják ki az API-forgalmat.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>API-felügyeleti szolgáltatáspéldány központi telepítése új régióba

> [!NOTE]
> Ha még nem hozott létre API Management szolgáltatáspéldányt, olvassa el az API Management szolgáltatáspéldány létrehozása című [témakört.][create an api management service instance]

Az Azure Portalon keresse meg az API Management szolgáltatáspéldány **méretezési és díjszabási** lapját.

![Méretezés lap][api-management-scale-service]

Ha új régióra szeretne telepíteni, kattintson az eszköztár **+ Régió hozzáadása** gombjára.

![Terület hozzáadása][api-management-add-region]

Válassza ki a helyet a legördülő listából, és adja meg a csúszkával rendelkező egységek számát.

![Egységek megadása][api-management-select-location-units]

A **Hozzáadás** gombra kattintva helyezze el a kijelölést a Helyek táblában.

Ismételje ezt a folyamatot addig, amíg az összes hely nincs konfigurálva, és kattintson a **Mentés gombra** az eszköztárról a telepítési folyamat elindításához.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>API-felügyeleti szolgáltatáspéldány törlése egy helyről

Az Azure Portalon keresse meg az API Management szolgáltatáspéldány **méretezési és díjszabási** lapját.

![Méretezés lap][api-management-scale-service]

Az eltávolítani kívánt hely hez nyissa meg a helyi menüt a táblázat jobb végén található **...** gombbal. Válassza a **Törlés** lehetőséget.

Erősítse meg a törlést, és kattintson a **Mentés** gombra a módosítások alkalmazásához.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>API-hívások irányítása a regionális háttérszolgáltatásokhoz

Az Azure API Management csak egy háttérszolgáltatás URL-címét tartalmazza. Annak ellenére, hogy vannak Azure API Management-példányok a különböző régiókban, az API-átjáró továbbra is továbbítja a kérelmeket ugyanahhoz a háttérszolgáltatáshoz, amely csak egy régióban van telepítve. Ebben az esetben a teljesítménynyereség csak az Azure API Management ben gyorsítótárazott válaszokból származik a kérésre jellemző régióban, de a háttérrendszerrel való kapcsolatfelvétel a világ minden tájáról továbbra is nagy késést okozhat.

A rendszer földrajzi eloszlásának teljes kihasználásához az Azure API Management-példányokkal azonos régiókban üzembe helyezett háttérszolgáltatásokkal kell rendelkeznie. Ezután a `@(context.Deployment.Region)` szabályzatok és a tulajdon használatával a forgalmat a háttérterület helyi példányaihoz irányíthatja.

1. Keresse meg az Azure API Management-példányt, és kattintson a bal oldali menü **API-kra.**
2. Válassza ki a kívánt API-t.
3. Kattintson a **Bejövő feldolgozás**nyíl legördülő **menüKódszerkesztő** elemére.

    ![API-kódszerkesztő](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. A `set-backend` feltételes `choose` házirendekkel kombinálva megfelelő útválasztási `<inbound> </inbound>` házirendet hozhat létre a fájl szakaszában.

    Az alábbi XML-fájl például nyugat-amerikai és kelet-ázsiai régiókban működik:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> A háttérszolgáltatások az Azure [Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)irányítsa az API-hívásokat a Traffic Manager, és hagyja, hogy az útválasztás automatikusan oldja meg.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Egyéni útválasztás használata az API Management regionális átjáróihoz

Az API Management a kérelmeket a [legalacsonyabb késés](../traffic-manager/traffic-manager-routing-methods.md#performance)alapján egy regionális _átjáróhoz_ irányítja. Bár az API Management ben nem lehet felülbírálni ezt a beállítást, a saját Traffic Manager egyéni útválasztási szabályokkal is használható.

1. Saját [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)létrehozása.
1. Ha egyéni tartományt használ, [használja azt a Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) helyett az API Management szolgáltatás.
1. [Konfigurálja az API Management területi végpontjait a Traffic Manager programban.](../traffic-manager/traffic-manager-manage-endpoints.md) A regionális végpontok például `https://<service-name>-<region>-01.regional.azure-api.net` `https://contoso-westus2-01.regional.azure-api.net`az URL-mintát követik.
1. [Konfigurálja az API Management regionális állapotvégpontjait a Traffic Manager alkalmazásban.](../traffic-manager/traffic-manager-monitoring.md) A regionális állapotvégpontok például `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`az URL-mintát követik.
1. Adja meg a Traffic Manager [útválasztási módját.](../traffic-manager/traffic-manager-routing-methods.md)

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
