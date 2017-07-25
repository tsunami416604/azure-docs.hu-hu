---
title: "Több hely üzemeltetése az Azure Application Gateway-en | Microsoft Docs"
description: "Ez az oldal áttekintést nyújt az Application Gateway többhelyes támogatásáról."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: 
ms.assetid: 49993fd2-87e5-4a66-b386-8d22056a616d
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 645f68d836babf11f32fc391e6dacc9430f0070c
ms.contentlocale: hu-hu
ms.lasthandoff: 07/14/2017

---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway – több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webalkalmazást konfigurálhat ugyanazon az alkalmazásátjáró-példányon. Ezzel a funkcióval hatékonyabb topológiát konfigurálhat telepítéseihez, mivel akár 20 webhelyet is hozzáadhat egyetlen alkalmazásátjáróhoz. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Az alábbi példában az alkalmazásátjáró a contoso.com és a fabrikam.com webhelyet szolgálja ki a ContosoServerPool és a FabrikamServerPool nevű háttér-kiszolgálókészlettel.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> A szabályok abban a sorrendben vannak feldolgozva, amelyben a portálon szerepelnek. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

A rendszer a http://contoso.com felé irányuló kérelmeket a ContosoServerPoolhoz, a http://fabrikam.com felé irányulókat pedig a FabrikamServerPoolhoz irányítja.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon alkalmazásátjáró-telepítésről lehet üzemeltetni. Az altartományok használatának példái között lehet az egyetlen alkalmazásátjáró-telepítésen üzemeltetett http://blog.contoso.com és http://app.contoso.com.

## <a name="host-headers-and-server-name-indication-sni"></a>Állomásfejléc és kiszolgálónév jelzése (SNI)

Három elterjedt mechanizmus létezik az ugyanazon az infrastruktúrán történő többhelyes üzemeltetéshez.

1. Több webalkalmazás üzemeltetése mindegyikhez rendelt egyedi IP-címmel.
2. Állomásnév használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.
3. Különböző portok használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.

Jelenleg az alkalmazásátjáró egyetlen nyilvános IP-címet kap, amelyen figyeli a forgalmat. Éppen ezért több, saját IP-címmel rendelkező alkalmazás üzemeltetése jelenleg nem támogatott. Az Application Gateway támogatja a különböző portokat figyelő több alkalmazás üzemeltetését, ám ilyen esetekben az alkalmazásoknak nem szabványos portokon kellene fogadniuk forgalmat, ami rendszerint nem kívánatos konfiguráció. Az Application Gateway a HTTP 1.1-állomásfejlécek segítségével üzemeltet egynél több webhelyet ugyanarról a nyilvános IP-címről és portról. Az alkalmazásátjárón üzemeltetett webhelyek ezenkívül támogathatják az SSL-alapú kiszervezést SNI (Kiszolgálónév jelzése) TLS-bővítménnyel. Ebben az esetben az RFC 6066 szabványban meghatározottak szerint az ügyfélböngészőnek és a háttérwebfarmnak támogatnia kell a HTTP/1.1-et és a TLS-bővítményt.

## <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

A már létező HTTPListener konfigurációs elem támogatja az állomásnév- és kiszolgálónév-jelzési elemeket, amelyekkel az alkalmazásátjáró a megfelelő háttérkészlet felé irányítja a forgalmat. Az alábbi kódpélda a sablonfájlból származó HttpListeners-elem kódrészlete.

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

Nincs szükség változtatásra az útválasztási szabályban. Az alapszintű útválasztási szabályt kell továbbra is választani, hogy a megfelelő webhelyfigyelőt kapcsolja a megfelelő háttércímkészlethez.

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

## <a name="next-steps"></a>Következő lépések

Miután megismerte a többhelyes üzemeltetést, látogasson el a [Create an application gateway using multiple site hosting](application-gateway-create-multisite-azureresourcemanager-powershell.md) (Alkalmazásátjáró létrehozása többhelyes üzemeltetéssel) weboldalra, ahonnan megtudhatja, hogyan hozhat létre egynél több webalkalmazást támogató alkalmazásátjárót.


