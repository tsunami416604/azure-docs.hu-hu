---
title: "Azure Application Gateway WebSocket támogatást |} Microsoft Docs"
description: "Ezen a lapon a kérelem átjáró WebSocket támogatási áttekintést nyújt."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Alkalmazásátjáró WebSocket támogatást áttekintése

Alkalmazásátjáró közötti átjáró különböző méretű WebSocket natív támogatást biztosít. Nem található felhasználó által konfigurálható szelektív letiltása és engedélyezése WebSocket támogatása. 

A szabványos WebSocket protokoll [RFC6455](https://tools.ietf.org/html/rfc6455) lehetővé teszi, hogy a kiszolgáló és az ügyfél között a teljes kétirányú kommunikációs egy hosszú ideig tartó TCP-kapcsolaton keresztül. Ez a funkció lehetővé teszi, hogy a webkiszolgáló és az ügyfél, amely kétirányú, mint a HTTP-alapú megvalósításokhoz lekérdezési szükségessége nélkül lehet több interaktív kommunikációját. WebSocket alacsony terhelés eltérően a HTTP és a több kérelem/válasz az erőforrások hatékonyabb kihasználását eredményezi azonos TCP-kapcsolatot is felhasználhatja. WebSocket protokoll tervezték hagyományos HTTP-port a 80-as és 443-as porton keresztül.

Tovább a szabványos HTTP-figyelő használ a 80-as vagy 443-as porton WebSocket forgalom fogadására. WebSocket forgalmat a rendszer ekkor átirányítja a WebSocket engedélyezett háttérkiszolgáló megfelelő háttérkészlet megadott alkalmazás átjáró szabályok használatával. A háttérkiszolgáló az alkalmazás átjáró mintavételek menüpontban, amely ismerteti a válaszolnia kell a [állapot-mintavételi áttekintése](application-gateway-probe-overview.md) szakasz. Alkalmazás átjáró állapotfigyelő mintavételt csak olyan HTTP/HTTPS. Minden egyes háttérkiszolgáló válaszolnia kell a HTTP-mintavételt az Alkalmazásátjáró WebSocket forgalom irányítására a kiszolgálóra.

## <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

Egy meglévő HTTP-figyelő segítségével WebSocket forgalom támogatásához. A következő egy minta sablon fájlból httpListeners elem egy részlet. Támogatja a WebSocket és WebSocket forgalmának biztonságossá tétele a HTTP és HTTPS figyelői kellene. Hasonló módon használhatja a [portal](application-gateway-create-gateway-portal.md) vagy [PowerShell](application-gateway-create-gateway-arm.md) Alkalmazásátjáró létrehozása a figyelők a porton keresztül 80/443-as WebSocket forgalom támogatásához.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool BackendHttpSetting és útválasztás szabály konfigurálása

Egy BackendAddressPool engedélyezett WebSocket-kiszolgálókkal háttérkészlet azonosítására szolgál. A backendHttpSetting a 80-as és 443-as, a háttérportot van meghatározva. Az affinitási cookie-alapú és requestTimeouts tulajdonságait, amelyek nem kapcsolódnak a WebSocket-forgalmat. Nincs változás a útválasztási szabály szükséges, a "Basic" szolgál a megfelelő figyelőt, hogy a megfelelő háttér címkészletet kötése. 

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

## <a name="websocket-enabled-backend"></a>A WebSocket engedélyezett háttér

A háttér kell rendelkeznie a konfigurált futó HTTP/HTTPS webkiszolgálón (általában 80/443-as port) WebSocket működéséhez a. Ez a követelmény azért van, mert WebSocket protokoll megköveteli a kezdeti kézfogás HTTP kell egy fejlécmező WebSocket protokoll a frissítést. A következő egy példa a fejléc:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Egy másik ennek oka, hogy alkalmazás átjáró háttér állapotmintáihoz csak a HTTP és HTTPS protokollok támogatja. Ha a háttérkiszolgáló nem válaszol a HTTP vagy HTTPS mintavételek menüpontban, az háttérkészlet kívüli lesz végrehajtva.

## <a name="next-steps"></a>Következő lépések

Támogatás a WebSocket megismerését követően navigáljon [Alkalmazásátjáró létrehozása](application-gateway-create-gateway.md) WebSocket használatába engedélyezve van a webes alkalmazás.

