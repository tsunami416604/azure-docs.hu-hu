---
title: "Alkalmazásátjáró létrehozása URL-útválasztási szabályok - Azure CLI 2.0 használatával |} Microsoft Docs"
description: "Ezen a lapon útmutatás létrehozásához és konfigurálásához a meglévő Alkalmazásátjáró URL-útválasztási szabályok használatával."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Alkalmazásátjáró létrehozása az Azure CLI 2.0 elérési-alapú útválasztási használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL-cím elérési út-alapú útválasztási, a HTTP-kérések URL-címe alapján útvonalak rendeli. Ellenőrzi, hogy egy olyan útvonalat, egy háttér-kiszolgálókészlethez az Alkalmazásátjáró szerepel az URL-lel konfigurálja, és ezután elküldi a hálózati forgalom a meghatározott készletbe. URL-cím elérési út-alapú útválasztási gyakori felhasználási-hoz való különböző háttér-kiszolgálófiók tartalom különböző érkező kérések elosztása.

Azure Application Gateway használ két szabály típusa: basic és URL-cím elérési út-alapú szabályok. Alapszintű szabálytípus a háttér-készletek ciklikus multiplexelés szolgáltatást biztosít. Elérési út-alapú szabályok ciklikus multiplexelés mellett is használatával az elérési út mintája a kérelem URL-adja meg a megfelelő háttér-készlethez.

## <a name="scenario"></a>Forgatókönyv

A következő példában Alkalmazásátjáró szolgálja ki a contoso.com forgalom két háttér-kiszolgálófiók rendelkezik: egy alapértelmezett kiszolgálókészletet és egy kép kiszolgálókészlethez.

Kérések a http://contoso.com/image * legyenek átirányítva a kép kiszolgálókészlet (**imagesBackendPool**). Ha az elérési út mintája nem egyezik, az Alkalmazásátjáró kiválasztja az alapértelmezett kiszolgáló-címkészlettel (**appGatewayBackendPool**).

![URL-cím útvonal](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a **Microsoft Azure parancssori** , jelentkezzen be:

```azurecli
az login -u "username"
```

> [!NOTE]
> Is `az login` az eszköz bejelentkezéshez írja be a kódot, aka.ms/devicelogin igénylő kapcsoló nélkül.

Miután megadta az előző parancs, kap egy kódot. Navigáljon a böngészőben a bejelentkezési folyamat folytatásához https://aka.ms/devicelogin.

![cmd megjelenítő eszköz-bejelentkezés][1]

A böngészőben írja be a kapott kódot. Ez átirányítja Önt egy bejelentkezési oldalára.

![Írja be a kódját a böngésző][2]

Írja be a kódot a bejelentkezéshez, és zárja be a böngészőt, a folytatáshoz.

![sikeres volt][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Elérési út alapú szabályokat adhat hozzá egy meglévő Alkalmazásátjáró

A következő lépések bemutatják a elérési utat alapú szabályokat adhat hozzá egy meglévő Alkalmazásátjáró.
### <a name="create-a-new-back-end-pool"></a>Háttér-készlet létrehozása

Az Alkalmazásbeállítás átjáró konfigurálása **imagesBackendPool** az elosztott terhelésű hálózati forgalmat a háttér-készletben. Ebben a példában beállítások különböző háttér-tárolókészlet az új háttér-készlet. Mindegyik háttér-készlet rendelkezhet saját beállítást. Elérési út-alapú szabályok beállításokkal háttér HTTP irányíthatja a forgalmat a megfelelő háttér-készlet tagjai számára. Ez határozza meg a protokoll és a forgalom a háttér-készlettag való küldés során használt port. A háttér HTTP beállításai határozzák meg a munkamenetek cookie-alapú is.  Ha engedélyezve van, munkamenet cookie-alapú kapcsolat küld forgalom az azonos háttér egyes csomagok az előző kérelmekben.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Hozzon létre egy új Alkalmazásátjáró előtér-port

Az előtér-port konfigurációs objektum szolgál egy figyelő milyen az Alkalmazásátjáró sem figyeli a forgalmat a figyelő portjának megadása.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Hozzon létre egy új figyelőt

Ebben a lépésben konfiguráljuk a figyelőt a bejövő hálózati forgalmat fogadó nyilvános IP-címhez és porthoz. Az alábbi példában a korábban konfigurált előtér-IP-konfiguráció, előtér-konfiguráció és a protokollt (http vagy HTTPS-t, amely kis-és nagybetűket), és konfigurálja a figyelő. Ebben a példában a figyelő figyeli a HTTP-forgalom a nyilvános IP-cím a 82-es porton ebben a forgatókönyvben a korábbi szakaszokban létrehozott.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Az URL-cím elérési út térkép létrehozásához

Ez a lépés konfigurál az elérési út és a háttér-készlet, a bejövő forgalom kezelésére rendelt közötti leképezést meghatározásához az alkalmazás-átjáró által használt relatív URL-címet.

> [!IMPORTANT]
> Mindegyik elérési út kezdődhet "/", és az egyetlen hely csillag engedélyezett végén. Érvényes többek között az /xyz, /xyz* vagy/xyz / *. Az az elérési út matcher táplált a karakterlánc nem tartalmaz sem szöveges az első után "?" vagy "#", és ezek a karakterek nem engedélyezettek. 

Az alábbi példa létrehoz egy szabály a/képek / * elérési útja, a háttér-a forgalom útválasztási **imagesBackendPool**. Ez a szabály biztosítja, hogy az egyes URL-címeket a háttérben történő továbbítódik. Például http://adatum.com/images/figure1.jpg ugrik **imagesBackendPool**. Ha az elérési út nem felel meg az előre definiált elérésiút-szabály, a szabály térkép konfiguráció is konfigurálja egy háttér címkészletet. Például http://adatum.com/shoppingcart/test.html ugrik **pool1** , mert az alapértelmezett alkalmazáskészlet páratlan forgalom nevezünk.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információt a Secure Sockets Layer (SSL) kiszervezési, lásd: [konfigurálása az SSL-kiszervezés Alkalmazásátjáró](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
