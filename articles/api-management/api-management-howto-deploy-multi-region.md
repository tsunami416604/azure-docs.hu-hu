---
title: Az Azure API Management-szolgáltatások üzembe helyezése több Azure-régiók |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy Azure API Management-szolgáltatáspéldány több Azure-régióban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 82ae0ef72bb4f546a1f946f3127aa5d74bec3c3b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957759"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Az Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban

Az Azure API Management támogatja a több régióból álló üzemelő, amely lehetővé teszi tetszőleges számú kívánt Azure-régiók között oszthatja el a egyetlen Azure API management szolgáltatás API-kiadók. Ez segít csökkenteni a kérelem által által tapasztalt késés földrajzilag elosztott az API-fogyasztókat és szolgáltatás rendelkezésre állása is növeli, ha egy adott régióban offline állapotba kerül.

Egy új Azure API Management szolgáltatás tartalmaz kezdetben csak az egyik [egység] [ unit] egyetlen Azure-régióban, az elsődleges régióba. További régiók könnyen hozzáadhatók az Azure Portalon keresztül. Az API Management-átjáró kiszolgáló összes régióban telepíti, és hívás forgalmat a rendszer átirányítja a legközelebbi átjáró. Ha egy régió elérhetetlenné válik, a rendszer automatikusan átirányítja a forgalmat tovább legközelebbi átjáróhoz.

> [!NOTE]
> Az Azure API Management az API gateway összetevővel régióban replikálja. A szolgáltatás felügyeleti összetevőt csak az elsődleges régióban üzemel. Egy esetleges leállás az elsődleges régióban konfigurációs módosítások alkalmazása az Azure API Management szolgáltatáspéldányt, esetén nem lehet – beleértve a beállítások és szabályzatok frissítéseket.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Az API Management-szolgáltatáspéldány üzembe egy új régióban

> [!NOTE]
> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg [az API Management szolgáltatáspéldány létrehozása][Create an API Management service instance].

Az Azure Portalon lépjen a **skálázás és díjszabás** az API Management szolgáltatáspéldányhoz tartozó oldal. 

![Méretezés lapon][api-management-scale-service]

Egy új régióban üzembe helyezéséhez kattintson a **+ Hozzáadás régió** az eszköztáron.

![Régió hozzáadása][api-management-add-region]

A legördülő listából válassza ki a helyet, és állítsa a csúszkát az egységek száma.

![Adja meg a egységek][api-management-select-location-units]

Kattintson a **Hozzáadás** helyezi el a kijelölt a helyek táblában. 

Ismételje meg ezt a folyamatot, mindaddig, amíg az összes hely konfigurálva van, és kattintson a **mentése** az eszköztárról az üzembe helyezés elindításához.

## <a name="remove-region"> </a>Az API Management-szolgáltatáspéldány törölje egy helyéről

Az Azure Portalon lépjen a **skálázás és díjszabás** az API Management szolgáltatáspéldányhoz tartozó oldal. 

![Méretezés lapon][api-management-scale-service]

A helyre, amelyet szeretne eltávolítani, nyissa meg a helyi menü használatával a **...**  gomb a tábla jobb szélén. Válassza ki a **törlése** lehetőséget.

A törlés jóváhagyásához, és kattintson a **mentése** a módosítások életbe léptetéséhez.

## <a name="route-backend"> </a>Regionális háttérszolgáltatások útvonal API-hívások

Az Azure API Management funkciókat csak egy háttérkiszolgáló URL-címe. Annak ellenére, hogy a különböző régiókban vannak az Azure API Management példányok, az API-átjáró lesz kérelmeket továbbra is továbbítsa a azonos háttérszolgáltatáshoz, amely csak egy adott régióban üzemel. Ebben az esetben a teljesítmény csak gyorsítótárazza az Azure API Management egy régióban a kérelemre adott válasz fog érkezni, de vegye fel a kapcsolatot a háttérrendszer szerte a világon előfordulhat, hogy továbbra is magas késést eredményezhetnek.

Teljes mértékben kihasználhatja a rendszer földrajzi eloszlása, a Háttérszolgáltatásokhoz üzembe helyezett Azure API Management-példány az azonos régióban lévő kell rendelkeznie. Ezután házirendekkel és `@(context.Deployment.Region)` tulajdonság, a forgalom átirányítása a háttérrendszer helyi példányát.

1. Keresse meg az Azure API Management-példány, és kattintson a **API-k** a bal oldali menüből.
2. Válassza ki a kívánt API-hoz.
3. Kattintson a **Kódszerkesztő** a a nyíl legördülő listából a **bejövő feldolgozás**.

    ![API Kódszerkesztő](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Használja a `set-backend` feltételes kombinálva `choose` szabályzatokat hozhat létre a megfelelő útválasztási házirend a `<inbound> </inbound>` fájl szakaszában.

    Ha például az alábbi XML fájl akkor működik, az USA nyugati RÉGIÓJA és Kelet-Ázsia régióban:

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

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
