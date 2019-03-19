---
title: WebSocket-támogatás az Azure Application Gatewayjel |} A Microsoft Docs
description: Ez az oldal az Application Gateway WebSocket-támogatás áttekintése.
author: amsriva
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: bae4b3d955076679a5640717ac6f5446e3951fb9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168119"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>WebSocket-támogatás az Application Gateway áttekintése

Az Application Gateway összes átjáró-méretek websocket natív támogatást biztosít. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. 

WebSocket protokoll a szabványosított [RFC6455](https://tools.ietf.org/html/rfc6455) lehetővé teszi, hogy egy kiszolgáló és a egy ügyfél egy teljes kétirányú kommunikációt egy hosszú ideig futó TCP-kapcsolaton keresztül. Ez a funkció lehetővé teszi a webkiszolgáló és az ügyfél, amely lehet a lekérdezés szükséges a HTTP-alapú megvalósításokhoz, feleslegessé téve a kétirányú interaktívabb kommunikációját. WebSocket rendelkezik alacsony többletterhelést HTTP eltérően, és felhasználhatja a azonos TCP-kapcsolat a több kérés/válasz erőforrások hatékonyabb kihasználását eredményezi. WebSocket protokoll célja a hagyományos HTTP-port a 80-as és 443-as porton keresztül működnek.

Továbbra is egy normál HTTP-figyelő használata a 80-as vagy 443-as porton WebSocket forgalom fogadására. WebSocket-forgalmat a rendszer ekkor átirányítja a WebSocket engedélyezett háttérkiszolgálóra a megfelelő háttérkészlet használata az application gateway szabályok megadott. A háttérkiszolgáló ismertetett application gateway mintavételek válaszolnia kell az [állapot-mintavételi áttekintése](application-gateway-probe-overview.md) szakaszban. Application gateway állapotadat-mintavételek csak olyan HTTP/HTTPS. Minden egyes háttérkiszolgálójához válaszolnia kell az application gateway irányíthatja a WebSocket-forgalmat. a kiszolgáló HTTP-mintavételt.

Gyors, valós idejű kommunikációt, például csevegés, az irányítópult és a játék alkalmazások előnyeit kihasználó alkalmazások használatban van.

## <a name="how-does-websocket-work"></a>Hogyan működik a WebSocket

WebSocket kapcsolatot létesíteni, egy adott HTTP-alapú kézfogás cseréje az ügyfél és a kiszolgáló között. Ha ez sikeres, az alkalmazásréteg protokoll van "frissítve" http a websockets protokoll, a korábban létrehozott TCP-kapcsolat használatával. Ez akkor fordul elő, ha HTTP kívül esik a teljes mértékben a képen látható; adatok elküldött vagy fogadott a WebSocket protokoll használatával mindkét végpont, amíg a WebSocket-kapcsolat le van zárva. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

Egy meglévő HTTP-figyelő használható támogatják a WebSocket-forgalmat. Az alábbiakban látható egy egy sablon mintafájl származó httpListeners-elem kódrészlete. A HTTP és HTTPS figyelői WebSocket támogatja, és tegye biztonságossá a WebSocket-forgalmat kell lennie. Hasonló módon használhatja a [portál](application-gateway-create-gateway-portal.md) vagy [PowerShell](application-gateway-create-gateway-arm.md) egy application gateway létrehozása a figyelők a port a 80-as/443-as támogatják a WebSocket-forgalmat.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Értékre, BackendHttpSetting és útválasztási szabály konfigurálása

Egy értékre egy háttérkészlet engedélyezve WebSocket-kiszolgálókkal meghatározására szolgál. A backendHttpSetting egy háttérport 80-as és 443-as porton van definiálva. A cookie-alapú affinitás és requestTimeouts tulajdonságait, amelyek nem kapcsolódnak a WebSocket-forgalmat. Nem szükséges az útválasztási szabályban változik, "Alapszintű" szolgál elősegítsék a megfelelő figyelőt, hogy a megfelelő háttércímkészlethez. 

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

## <a name="websocket-enabled-backend"></a>Engedélyezett WebSocket-háttérrendszer

A háttérbeli kell rendelkeznie a konfigurált futó HTTP/HTTPS webkiszolgáló (általában 80-as/443-as port) websocket működjön. Ez a követelmény azért, mert a WebSocket protokoll van szükség a kezdeti kézfogás HTTP mindig a WebSocket protokoll fejléc-mezőként történő frissítés. Az alábbiakban látható egy példa egy fejléc:

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

Egy másik ennek oka, hogy application gateway háttérrendszer állapotadat-mintavétel csak HTTP és HTTPS protokollok támogatja. Ha a háttérkiszolgáló nem válaszol a HTTP vagy HTTPS mintavételek, kívül háttérkészlet csökken.

## <a name="next-steps"></a>További lépések

Miután megismerkedett a WebSocket-támogatás, látogasson el [hozzon létre egy application gateway](application-gateway-create-gateway.md) a WebSocket – első lépések engedélyezve a webalkalmazáshoz.

