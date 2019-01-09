---
title: Azure Application Gateway URL-alapú tartalom-útválasztás áttekintése
description: Ez az oldal az Azure Application Gateway URL-alapú tartalom-útválasztás, UrlPathMap-konfigurációról és a PathBasedRouting szabály áttekintést nyújt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.openlocfilehash: d5d8ed09da2b05de079bc1b62066bb4008a659d8
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118367"
---
# <a name="url-path-based-routing-overview"></a>Az URL-alapú útválasztás áttekintése

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. 

Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket a megfelelő háttér-kiszolgálókészlethez irányítja.

A következő példában az Application Gateway szolgálja ki a három háttér kiszolgálókészlettel contoso.com forgalom például: Kérések a VideoServerPool, az ImageServerPool és a DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

A http://contoso.com/video/* URL-hez kapcsolódó kérések a VideoServerPool, a http://contoso.com/images/* URL-hez kapcsolódóak pedig az ImageServerPool készlethez lesznek átirányítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

> [!IMPORTANT]
> A szabályok abban a sorrendben vannak feldolgozva, amelyben a portálon szerepelnek. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

## <a name="urlpathmap-configuration-element"></a>Az UrlPathMap konfigurációs elem

Az UrlPathMap elem elérésiút-minták meghatározására szolgál a háttér-kiszolgálókészletek leképezésében. Az alábbi kódpélda a sablonfájlból származó urlPathMap elem kódrészlete.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

PathPattern az elérésiút-minták listája. Minden mintának a / jellel kell kezdődnie, a „*” jel pedig kizárólag a mintavégi „/” jel után állhat. Az elérésiút-megfeleltetőben megadott sztring nem tartalmaz szöveget az első után? vagy #, és ezek a karakterek itt nem megengedettek. Ellenkező esetben egy URL-CÍMBEN szereplő bármely karakter PathPattern engedélyezettek.

A támogatott minták attól függenek, amelyre e telepít az Application Gateway v1 vagy v2-es:

#### <a name="v1"></a>V1

Elérésiút-szabályok nem különbözteti meg.

|V1 elérésiút-minta  |Támogatott?  |
|---------|---------|
|`/images/*`     |igen|
|`/images*`     |nem|
|`/images/*.jpg`     |nem|
|`/*.jpg`     |nem|
|`/Repos/*/Comments/*`     |nem|
|`/CurrentUser/Comments/*`     |igen|

#### <a name="v2"></a>v2

Elérésiút-szabályok a kis-és nagybetűket.

|v2 elérésiút-minta  |Támogatott?  |
|---------|---------|
|`/images/*`     |igen|
|`/images*`     |igen|
|`/images/*.jpg`     |nem|
|`/*.jpg`     |nem|
|`/Repos/*/Comments/*`     |nem|
|`/CurrentUser/Comments/*`     |igen|

További információért tekintse át az [URL-alapú átirányításhoz készült Resource Manager-sablonokat](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

## <a name="pathbasedrouting-rule"></a>PathBasedRouting szabály

A PathBasedRouting típusú RequestRoutingRule szabály arra szolgál, hogy egy figyelőt az adott urlPathMap elemhez kössön. Minden kérés, amely ehhez a figyelőhöz kapcsolódik, az urlPathMap elemben meghatározott irányelvek alapján lesz átirányítva.
A PathBasedRouting szabály kódrészlete:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>További lépések

Miután elsajátította az URL-alapú tartalom-átirányításról szóló ismereteket, látogasson el [az URL-alapú átirányítást használó alkalmazásátjáró létrehozását bemutató témakörhöz](application-gateway-create-url-route-portal.md).
