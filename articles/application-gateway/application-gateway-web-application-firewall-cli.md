---
title: "Webalkalmazási tűzfal konfigurálása: Azure Application Gateway |} Microsoft Docs"
description: "Ez a cikk útmutatást webalkalmazási tűzfal egy új vagy meglévő Alkalmazásátjáró használatának módjáról."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: e60bfc89378569b154f4f973d1dceb683fa58482
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Webalkalmazási tűzfal konfigurálása egy új vagy meglévő Alkalmazásátjáró Azure parancssori felülettel

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Megtudhatja, hogyan hozzon létre egy webes alkalmazás tűzfalat (waf-ot)-Alkalmazásátjáró engedélyezve van. Ismerje meg, egy WAF hozzáadása egy meglévő Alkalmazásátjáró is.

Az Azure alkalmazás átjáró WAF webalkalmazások védje a közös web-alapú támadások, például az SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának.

 Alkalmazásátjáró réteg-7 terheléselosztó. Feladatátvétel esetén a teljesítmény-útválasztási HTTP-kérelmek különböző kiszolgálók között, hogy be van kapcsolva a felhőbeli vagy helyszíni biztosít. Alkalmazásátjáró számos alkalmazás kézbesítési vezérlő (LÉPETT) szolgáltatásokat biztosítja:

 * HTTP terheléselosztás 
 * A munkamenet cookie-alapú kapcsolat 
 * Secure Sockets Layer (SSL) kiürítése 
 * Egyéni állapot-mintavételi csomagjai 
 * Többhelyes funkciók támogatása
 
 Támogatott szolgáltatások teljes listáját, lásd: [Alkalmazásátjáró áttekintése](application-gateway-introduction.md).

Ez a cikk bemutatja, hogyan [webalkalmazási tűzfal hozzáadása egy meglévő Alkalmazásátjáró](#add-web-application-firewall-to-an-existing-application-gateway). Azt is bemutatja, hogyan [webalkalmazási tűzfal használó Alkalmazásátjáró létrehozása](#create-an-application-gateway-with-web-application-firewall).

![a forgatókönyv kép][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Előfeltétel: Az Azure parancssori felület 2.0 telepítése

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület (CLI) Mac, Linux és Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>WAF konfigurációs különbségek

Ha, hogy elolvasta a [hozzon létre egy alkalmazást az Azure parancssori felület](application-gateway-create-gateway-cli.md), hogy tudomásul veszi, hogy a Termékváltozat-beállítások konfigurálása az Alkalmazásátjáró létrehozása. A WAF Alkalmazásátjáró a Termékváltozat konfigurálásakor adja meg a további beállításokat tartalmazza. Nincsenek további módosítások, amely akkor adja meg az Alkalmazásátjáró magát a.

| **Beállítás** | **Részletek**
|---|---|
|**Termékváltozat** |Egy normál Alkalmazásátjáró egy WAF nélkül támogatja **szabványos\_kis**, **szabványos\_Közepes**, és **szabványos\_nagy**méretét. A egy WAF bevezetése esetén két további SKU, **WAF\_Közepes** és **WAF\_nagy**. Egy WAF kis alkalmazásátjárót nem támogatott.|
|**Mód** | Ez a beállítás akkor a WAF módját. két érték engedélyezett **észlelési** és **megelőzési**. Ha a WAF be van állítva **észlelési** mód, minden fenyegetések vannak tárolva egy naplófájlt. A **megelőzési** mód, eseményeket naplózza, de a támadó megkapja a 403-as nem engedélyezett az Alkalmazásátjáró válaszát.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Webalkalmazási tűzfal hozzáadása egy meglévő Alkalmazásátjáró

A következő parancsot egy meglévő szabványos Alkalmazásátjáró WAF-kompatibilis Alkalmazásátjáró módosítja:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Ez a parancs egy WAF az Alkalmazásátjáró frissítése. Az alkalmazás átjáró naplók megtekintése ismertetése: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md). Egy WAF biztonsági jellegéből tekintse át a naplókat rendszeresen a webalkalmazások biztonsági állapotát.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Webalkalmazási tűzfal Alkalmazásátjáró létrehozása

A következő parancsot egy WAF Alkalmazásátjáró hoz létre:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Az alapvető beállításokkal WAF létre alkalmazásátjárót védelmet CRS 3.0-val van állítva.

## <a name="get-an-application-gateway-dns-name"></a>Egy alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után a következő lépésre konfigurálhatja az előtér-kommunikációra. Egy nyilvános IP-cím használata esetén az Alkalmazásátjáró egy dinamikusan hozzárendelt DNS-nevet, amely nincs rövid van szükség. Győződjön meg arról, hogy a felhasználók is elérte az Alkalmazásátjáró, hogy használatával egy olyan CNAME rekordot a nyilvános végpontot az Alkalmazásátjáró. További információkért lásd: [egy egyéni tartománynév beállítása az Azure-felhőszolgáltatás](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Egy olyan CNAME rekordot konfigurálásához beolvasása részleteit az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet az Alkalmazásátjáró csatolva PublicIPAddress elem használatával. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, amely mutat, a két webes alkalmazásokhoz, hogy a DNS-név. Nem javasoljuk A rekordok használatával, mert a VIP előfordulhat, hogy módosítsa az Alkalmazásátjáró újraindításakor.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>További lépések

WAF szabályok testreszabásával kapcsolatban [testre szabhatja a webes alkalmazás tűzfalszabályok keresztül az Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
