---
title: Azure API Management-szolgáltatások üzembe helyezése több Azure-régióban
titleSuffix: Azure API Management
description: Ismerje meg, hogyan helyezhet üzembe egy Azure API Management Service-példányt több Azure-régióban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442663"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban

Az Azure API Management támogatja a többrégiós telepítést, amely lehetővé teszi az API-közzétevők számára, hogy egyetlen Azure API Management-szolgáltatást terjesszen az összes támogatott Azure-régió között. A többrégiós szolgáltatás lehetővé teszi a földrajzilag elosztott API-fogyasztók által észlelt kérelmek késésének csökkentését és a szolgáltatás rendelkezésre állásának javítását, ha az egyik régió offline állapotú

Az új Azure API Management szolgáltatás kezdetben csak egyetlen [egységet][unit] tartalmaz egyetlen Azure-régióban, az elsődleges régióban. További régiók is hozzáadhatók az elsődleges vagy másodlagos régióhoz. A rendszer minden kiválasztott elsődleges és másodlagos régióban üzembe helyez egy API Management átjáró összetevőt. A bejövő API-kérelmek automatikusan a legközelebbi régióba lesznek irányítva. Ha egy régió offline állapotba kerül, az API-kérelmeket a rendszer automatikusan átirányítja a meghibásodott régió köré a következő legközelebbi átjáróra.

> [!NOTE]
> A rendszer csak a API Management átjáró összetevőjét telepíti minden régióra. A Service Management összetevőt és a fejlesztői portált csak az elsődleges régió tárolja. Ezért az elsődleges régió meghibásodása esetén a fejlesztői portálhoz való hozzáférés és a konfiguráció módosításának lehetősége (például az API-k hozzáadása, a házirendek alkalmazása) nem fog megjelenni, amíg az elsődleges régió újra online állapotba nem kerül. Míg az elsődleges régió offline állapotban elérhető másodlagos régiók, az API-forgalmat továbbra is az elérhető legújabb konfigurációval fogja kiszolgálni.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>API Management szolgáltatási példány üzembe helyezése egy új régióban

> [!NOTE]
> Ha még nem hozott létre API Management Service-példányt, tekintse meg [a API Management-szolgáltatás példányának létrehozása][create an api management service instance]című témakört.

A Azure Portal navigáljon az API Management Service-példány **méretezési és díjszabási** lapjához.

![Méretezés lap][api-management-scale-service]

Egy új régióban való üzembe helyezéshez kattintson a **+ régió hozzáadása** lehetőségre az eszköztáron.

![Régió hozzáadása][api-management-add-region]

Válassza ki a helyet a legördülő listából, és állítsa be az egységek számát a csúszkával.

![Egységek meghatározása][api-management-select-location-units]

Kattintson a **Hozzáadás** gombra a kijelölés a helyszínek táblában való elhelyezéséhez.

Ismételje meg a folyamatot, amíg nincs konfigurálva minden hely, majd kattintson a **Save (Mentés** ) gombra az eszköztárból a telepítési folyamat elindításához.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>API Management szolgáltatás példányának törlése egy helyről

A Azure Portal navigáljon az API Management Service-példány **méretezési és díjszabási** lapjához.

![Méretezés lap][api-management-scale-service]

Az eltávolítandó helyhez nyissa meg a helyi menüt a tábla jobb oldalán található **..** . gomb használatával. Válassza a **Törlés** lehetőséget.

Erősítse meg a törlést, és kattintson a **Mentés** gombra a módosítások alkalmazásához.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>API-hívások átirányítása a regionális háttérbeli szolgáltatásokhoz

Az Azure API Management csak egyetlen háttérbeli szolgáltatás URL-címét tartalmazza. Bár vannak Azure API Management-példányok különböző régiókban, az API-átjáró továbbra is továbbítja a kéréseket ugyanarra a háttér-szolgáltatásba, amely csak egy régióban van üzembe helyezve. Ebben az esetben a teljesítmény nyeresége csak az Azure-API Management gyorsítótárba helyezett válaszokból származik, a kéréshez tartozó régióban, de a háttérben való kapcsolatfelvétel továbbra is nagy késést okozhat.

A rendszer földrajzi eloszlásának teljes kihasználásához az Azure API Management-példányokkal megegyező régiókban kell telepíteni a háttér-szolgáltatásokat. Ezután a házirendek és `@(context.Deployment.Region)` a tulajdonság használatával átirányíthatja a forgalmat a háttér helyi példányaira.

1. Navigáljon az Azure API Management-példányhoz, és kattintson a bal oldali menü **API** -k elemére.
2. Válassza ki a kívánt API-t.
3. Kattintson a **Kódszerkesztő** gombra a **bejövő feldolgozás**nyíl legördülő menüjéből.

    ![API-kód szerkesztője](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. A feltételes `set-backend` `choose` házirendek együttes használatával megfelelő útválasztási házirendet hozhat létre a fájl `<inbound> </inbound>` szakaszában.

    Az alábbi XML-fájl például az USA nyugati régiójában és Kelet-Ázsia régióban fog működni:

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
> A háttér-szolgáltatásokat az [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)is megteheti, az API-hívásokat átirányítja a Traffic Managerra, és lehetővé teszi az Útválasztás automatikus feloldását.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Egyéni útválasztás használata API Management regionális átjárók számára

API Management a [legalacsonyabb késés](../traffic-manager/traffic-manager-routing-methods.md#performance)alapján átirányítja a kéréseket egy regionális _átjáróra_ . Bár ez a beállítás nem bírálható felül a API Managementban, saját Traffic Manager is használhat egyéni útválasztási szabályokkal.

1. Saját Azure- [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)létrehozása.
1. Ha egyéni tartományt használ, a API Management szolgáltatás helyett [használja a Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) .
1. [Konfigurálja a API Management regionális végpontokat a Traffic Managerban](../traffic-manager/traffic-manager-manage-endpoints.md). A regionális végpontok az URL-mintát követik `https://<service-name>-<region>-01.regional.azure-api.net`, például `https://contoso-westus2-01.regional.azure-api.net`:.
1. [Konfigurálja a API Management regionális állapotjelző végpontokat a Traffic Managerban](../traffic-manager/traffic-manager-monitoring.md). A területi állapot végpontok az URL-mintát követik `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, például `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`:.
1. A Traffic Manager [útválasztási módszerének](../traffic-manager/traffic-manager-routing-methods.md) meghatározása.

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
