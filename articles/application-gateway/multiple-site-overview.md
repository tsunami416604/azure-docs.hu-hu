---
title: Több webhely üzemeltetése az Azure Application Gateway
description: Ez a cikk áttekintést nyújt az Azure Application Gateway többhelyes támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80257410"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway – több hely üzemeltetése

Több hely üzemeltetése lehetővé teszi több webalkalmazás konfigurálását az Application Gateway ugyanazon portjára. Ez a funkció lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon az üzemelő példányokhoz, ha akár 100 webhelyet ad hozzá egy Application gatewayhez. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. A következő példában az Application Gateway a `contoso.com` `fabrikam.com` contososerverpoolhoz és a fabrikamserverpoolhoz irányítja nevű, két háttér-kiszolgáló készletből származó forgalmat szolgáltat.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> A szabályok feldolgozása a v1 SKU-ban található portálon megjelenő sorrendben történik. A v2 SKU esetében a pontos egyezések magasabb prioritással rendelkeznek. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen ugyanazon szülőtartomány több altartományát is üzemeltetheti ugyanazon az Application Gateway-példányon. Például `http://blog.contoso.com` `http://app.contoso.com` egyetlen Application Gateway-telepítésben is üzemeltetheti a szolgáltatást.

## <a name="host-headers-and-server-name-indication-sni"></a>Állomásfejléc és kiszolgálónév jelzése (SNI)

Három elterjedt mechanizmus létezik az ugyanazon az infrastruktúrán történő többhelyes üzemeltetéshez.

1. Több webalkalmazás üzemeltetése mindegyikhez rendelt egyedi IP-címmel.
2. Állomásnév használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.
3. Különböző portok használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.

A Application Gateway jelenleg egyetlen nyilvános IP-címet támogat, ahol a forgalmat figyeli. Így a saját IP-címmel rendelkező több alkalmazás használata jelenleg nem támogatott. 

Application Gateway több, különböző portokon figyelt alkalmazást támogat, de ehhez a forgatókönyvhöz az szükséges, hogy az alkalmazások a nem szabványos portokon fogadják a forgalmat. Ez gyakran nem a kívánt konfiguráció.

Az Application Gateway a HTTP 1.1-állomásfejlécek segítségével üzemeltet egynél több webhelyet ugyanarról a nyilvános IP-címről és portról. Az Application Gateway-ben üzemeltetett helyek támogatják a TLS-kiszervezést is Kiszolgálónév jelzése (SNI) TLS-bővítmény használatával. Ebben az esetben az RFC 6066 szabványban meghatározottak szerint az ügyfélböngészőnek és a háttérwebfarmnak támogatnia kell a HTTP/1.1-et és a TLS-bővítményt.

## <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

A meglévő HTTPListener-konfigurációs elemek támogatják az állomásnév és a kiszolgálónév-jelző elemek támogatását. A Application Gateway használja a forgalom a megfelelő háttér-készletbe való irányításához. 

A következő kódrészlet egy sablonfájl HttpListeners elemének kódrészlete:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

A teljes körű sablonalapú telepítés leírását a [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Többhelyes üzemeltetést használó Resource Manager-sablon) weboldalon találja.

## <a name="routing-rule"></a>Útválasztási szabály

Nincs szükség módosításra az útválasztási szabályban. Az alapszintű útválasztási szabályt kell továbbra is választani, hogy a megfelelő webhelyfigyelőt kapcsolja a megfelelő háttércímkészlethez.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>További lépések

Miután megismerte a többhelyes üzemeltetést, látogasson el a [Create an application gateway using multiple site hosting](tutorial-multiple-sites-powershell.md) (Alkalmazásátjáró létrehozása többhelyes üzemeltetéssel) weboldalra, ahonnan megtudhatja, hogyan hozhat létre egynél több webalkalmazást támogató alkalmazásátjárót.

