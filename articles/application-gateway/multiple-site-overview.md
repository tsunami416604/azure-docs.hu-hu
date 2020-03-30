---
title: Több webhely üzemeltetése az Azure Application Gateway-en
description: Ez a cikk áttekintést nyújt az Azure Application Gateway többhelyes támogatás.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257410"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway – több hely üzemeltetése

Több helyüzemeltetési lehetővé teszi, hogy több webalkalmazás konfigurálása ugyanazon a porton egy alkalmazás átjáró. Ez a szolgáltatás lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon a központi telepítésekhez, ha legfeljebb 100 webhelyet ad hozzá egy alkalmazásátjáróhoz. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. A következő példában az alkalmazásátjáró két ContosoServerPool `contoso.com` és `fabrikam.com` FabrikamServerPool nevű háttérkiszolgáló-készlet forgalmat szolgál ki.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> A szabályok feldolgozása abban a sorrendben kerül feldolgozásra, ahogy a v1 Termékváltozat portálján vannak felsorolva. A v2 Termékváltozat esetében a pontos egyezések elsőbbsége magasabbak. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen ugyanannak a szülőtartománynak több altartományát is üzemeltetheti ugyanazon az alkalmazásátjáró-telepítésen. Például üzemeltetheti, `http://blog.contoso.com` `http://app.contoso.com` és egyetlen alkalmazásátjáró központi telepítése.

## <a name="host-headers-and-server-name-indication-sni"></a>Állomásfejléc és kiszolgálónév jelzése (SNI)

Három elterjedt mechanizmus létezik az ugyanazon az infrastruktúrán történő többhelyes üzemeltetéshez.

1. Több webalkalmazás üzemeltetése mindegyikhez rendelt egyedi IP-címmel.
2. Állomásnév használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.
3. Különböző portok használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.

Jelenleg az Application Gateway egyetlen nyilvános IP-címet támogat, ahol figyeli a forgalmat. Így több alkalmazás, mindegyik saját IP-címmel jelenleg nem támogatott. 

Az Application Gateway több alkalmazást támogat, amelyek különböző portokon figyelnek, de ebben a forgatókönyvben az alkalmazásoknak nem szabványos portokon kell fogadniuk a forgalmat. Ez gyakran nem a kívánt konfiguráció.

Az Application Gateway a HTTP 1.1-állomásfejlécek segítségével üzemeltet egynél több webhelyet ugyanarról a nyilvános IP-címről és portról. Az alkalmazásátjárón tárolt helyek a TLS-kiszervezést is támogathatják a Kiszolgálónév-jelzési (SNI) TLS-bővítménysel. Ebben az esetben az RFC 6066 szabványban meghatározottak szerint az ügyfélböngészőnek és a háttérwebfarmnak támogatnia kell a HTTP/1.1-et és a TLS-bővítményt.

## <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

A meglévő HTTPListener konfigurációs elemek javítva vannak az állomásnév és a kiszolgáló névjelző elemeinek támogatásához. Az Application Gateway a megfelelő háttérkészlethez irányítja a forgalmat. 

A következő példa egy sablonfájlból származó HttpListeners-elem kódrészlete:

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

