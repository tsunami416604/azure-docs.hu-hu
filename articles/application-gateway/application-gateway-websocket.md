---
title: WebSocket-támogatás az Azure Application Gateway-ben
description: Az Application Gateway natív támogatást nyújt a WebSockethez minden átjáróméret esetében. Nincsenek felhasználó által konfigurálható beállítások.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130331"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>A WebSocket támogatás áttekintése az Application Gateway alkalmazásban

Az Application Gateway natív támogatást nyújt a WebSockethez minden átjáróméret esetében. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. 

Az [RFC6455](https://tools.ietf.org/html/rfc6455) szabványban szabványosított WebSocket protokoll lehetővé teszi a teljes kétirányú kommunikációt a kiszolgáló és az ügyfél között hosszú ideig futó TCP-kapcsolaton keresztül. Ez a szolgáltatás lehetővé teszi a webkiszolgáló és az ügyfél közötti interaktívabb kommunikációt, amely kétirányú lehet anélkül, hogy a HTTP-alapú implementációkban szükséges lekérdezésre lenne szükség. A WebSocket a HTTP-vel ellentétben alacsony terheléssel rendelkezik, és ugyanazt a TCP-kapcsolatot több kérelemhez/válaszhoz is felhasználhatja, ami az erőforrások hatékonyabb kihasználását eredményezi. A WebSocket protokollok a 80-as és 443-as hagyományos HTTP-portokon keresztül működnek.

Továbbra is használhatja a szabványos HTTP-figyelő a 80-as vagy 443-as porton a WebSocket-forgalom fogadásához. A WebSocket-forgalmat ezután a WebSocket-kompatibilis háttérkiszolgálóra irányítja az alkalmazásátjáró-szabályokban megadott megfelelő háttérkészlet használatával. A háttérkiszolgálónak válaszolnia kell az alkalmazásátjáró-mintavételekre, amelyeket az [állapotminta áttekintése](application-gateway-probe-overview.md) szakasz ban ismertetett. Az alkalmazásátjáró állapotmintás a HTTP/HTTPS csak. Minden háttérkiszolgálónak válaszolnia kell a HTTP-mintavételekre az alkalmazásátjáróhoz a WebSocket-forgalom kiszolgálóra történő irányításához.

Olyan alkalmazásokban használják, amelyek a gyors, valós idejű kommunikáció előnyeit élvezik, például a csevegés, az irányítópult és a játékalkalmazások.

## <a name="how-does-websocket-work"></a>Hogyan működik a WebSocket?

WebSocket-kapcsolat létrehozásához egy adott HTTP-alapú kézfogás kerül kiváltásra az ügyfél és a kiszolgáló között. Ha sikeres, az alkalmazásréteg-protokoll http-ről WebSockets-re "frissül" a korábban létrehozott TCP-kapcsolat használatával. Ha ez megtörténik, a HTTP teljesen kikerül a képből; az adatok a WebSocket protokoll használatával mindkét végponton elküldhetők vagy fogadhatók, amíg a WebSocket-kapcsolat be nem zárul. 

![Websocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

Egy meglévő HTTP-figyelő websocket-forgalom támogatására használható. Az alábbi listaegy httpListeners-elem egy mintasablonfájlból való részletét tartalmaz. Http- és HTTPS-figyelőkre is szükség van a WebSocket és a WebSocket-forgalom biztonságossá tétele érdekében. Hasonlóképpen használhatja a portálon vagy az Azure PowerShell-ben hozzon létre egy alkalmazásátjárót a 80/443-as porton lévő figyelőkkel a WebSocket-forgalom támogatásához.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>HáttérendAddressPool, HáttérrendszerHttpSetting és Útválasztási szabály konfigurációja

A BackendAddressPool segítségével websocket-kompatibilis kiszolgálókkal rendelkező háttérkészletet definiálhat. A háttérrendszerHttpSetting van definiálva egy backend port 80 és 443. A kérelem időtúlára érték http-beállítások is vonatkozik a WebSocket-munkamenet. Nincs szükség módosításra az útválasztási szabályban, amely a megfelelő figyelőt a megfelelő háttércímkészlethez köti. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket engedélyezett háttérkiszolgáló

A WebSocket működéséhez a háttérrendszernek rendelkeznie kell a konfigurált porton (általában 80/443) futó HTTP/HTTPS webkiszolgálóval. Ez azért van, mert a WebSocket protokoll megköveteli, hogy a kezdeti kézfogás HTTP-nek legyen, és fejlécmezőként frissítsen a WebSocket protokollra. Az alábbi példa egy fejlécet mutat be:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Ennek másik oka az, hogy az alkalmazásátjáró-háttér-állapotminta csak a HTTP- és HTTPS-protokollokat támogatja. Ha a háttérkiszolgáló nem válaszol a HTTP- vagy HTTPS-mintavételekre, akkor a háttérkészletből kiveszi a rendszer.

## <a name="next-steps"></a>További lépések

Miután tudomást szerzett a WebSocket-támogatásról, [hozzon létre egy alkalmazásátjárót](quick-create-powershell.md) a WebSocket-kompatibilis webalkalmazás okkal való ismerkedéshez.