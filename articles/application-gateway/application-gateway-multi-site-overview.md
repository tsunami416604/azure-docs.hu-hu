---
title: Az Azure Application Gateway több hely üzemeltetése
description: Ez az oldal áttekintést az Azure Application Gateway többhelyes támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/16/2019
ms.author: amsriva
ms.openlocfilehash: 04aca43e7220b0d5f644ca4f03db3a7442972728
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358468"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway – több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webalkalmazást konfigurálhat ugyanazon az alkalmazásátjáró-példányon. Ezzel a funkcióval hatékonyabb topológiát az üzemelő példányok beállításához ad hozzá egy application gateway akár 100 webhelyet. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Az alábbi példában az alkalmazásátjáró a contoso.com és a fabrikam.com webhelyet szolgálja ki a ContosoServerPool és a FabrikamServerPool nevű háttér-kiszolgálókészlettel.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> A szabályok abban a sorrendben vannak feldolgozva, amelyben a portálon szerepelnek. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

A http://contoso.com iránti kérelmek a ContosoServerPoolba, míg a http://fabrikam.com felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon alkalmazásátjáró-telepítésről lehet üzemeltetni. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett http://blog.contoso.com és http://app.contoso.com.

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

## <a name="next-steps"></a>További lépések

Miután megismerte a többhelyes üzemeltetést, látogasson el a [Create an application gateway using multiple site hosting](application-gateway-create-multisite-azureresourcemanager-powershell.md) (Alkalmazásátjáró létrehozása többhelyes üzemeltetéssel) weboldalra, ahonnan megtudhatja, hogyan hozhat létre egynél több webalkalmazást támogató alkalmazásátjárót.