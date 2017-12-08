---
title: "Konfigurálja az SSL kiszervezési - Azure Application Gateway - Azure CLI 2.0 |} Microsoft Docs"
description: "Ez a cikk ismerteti az utasításokat követve hozzon létre egy alkalmazás SSL-kiszervezés Azure CLI 2.0 használatával"
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
ms.openlocfilehash: a2c4062db821e39e1af4fa1d54da0121d3993db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Az SSL-kiszervezés Alkalmazásátjáró konfigurálása Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Az Azure klasszikus PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. SSL kiszervezési is egyszerűbbé teszi a tanúsítványok kezelését az előtér-kiszolgálón.

## <a name="prerequisite-install-the-azure-cli-20"></a>Előfeltétel: Az Azure parancssori felület 2.0 telepítése

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="required-components"></a>Szükséges összetevők

* **Háttér-kiszolgálófiók készlet**: a háttér-kiszolgálók IP-címek listáját. A felsorolt IP-címek a virtuális hálózati alhálózat kell tartoznia, vagy egy nyilvános IP-cím vagy a virtuális IP-cím (VIP) kell lennie.
* **Háttér-kiszolgálófiók Készletbeállítások**: minden készlethez beállítások, például a portot, a protokoll és a cookie-alapú kapcsolat van. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port**: Ez a port nem nyilvános port meg van nyitva, az alkalmazás-átjárón. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő**: A figyelő rendelkezik egy előtér-portot, a protokollt (Http vagy Https; ezek a beállítások-és nagybetűk), és az SSL-tanúsítvány neve (ha az SSL beállításának-kiszervezés).
* **A szabály**: A szabály van kötve, a figyelő és a háttér-kiszolgálófiók készletben, és határozza meg, melyik háttér-kiszolgálófiók készlet át tudja irányítani a forgalmat, a találatok, amikor egy adott figyelő. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához *Https*-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCertificate** elem **HttpListener** az SSL-tanúsítvány konfigurálva a változó értékével. Az előtér-port frissíteni kell, hogy **443-as**.

**Ahhoz, hogy a cookie-alapú kapcsolat**: annak érdekében, hogy egy ügyfél kérelmet mindig van irányítva az azonos virtuális gép a webfarm Alkalmazásátjáró konfigurálhatja. Ennek megvalósítása érdekében beszúrása egy munkamenetcookie-t, amely lehetővé teszi, hogy az átjáró megfelelően irányítja a forgalmat. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen *Enabled* a **BackendHttpSettings** elemen belül.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>A meglévő Alkalmazásátjáró SSL kiszervezési konfigurálása

Adjon meg egy meglévő Alkalmazásátjáró SSL kiszervezési konfigurálása a következő parancsokat:

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Hozzon létre egy alkalmazás SSL kiürítése

Az alábbi minta Alkalmazásátjáró SSL kiszervezési hoz létre. A tanúsítvány és a tanúsítvány jelszava frissíteni kell, hogy érvényes titkos kulccsal.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Egy alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után a következő lépésre konfigurálhatja az előtér-kommunikációra.  Alkalmazásátjáró szükséges egy dinamikusan hozzárendelt DNS-név, ha egy nyilvános IP-cím használatával, amely nincs rövid. Annak érdekében, hogy a végfelhasználók is elérte az Alkalmazásátjáró, egy CNAME rekordot a segítségével a nyilvános végpontot az Alkalmazásátjáró mutasson. További információkért lásd: [az egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Alias konfigurálásához beolvasni az Alkalmazásátjáró és a hozzá társított IP-/ DNS-név segítségével részleteit a **PublicIPAddress** elem csatolva az Alkalmazásátjáró. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, amely mutat, a két webes alkalmazásokhoz, hogy a DNS-név. A-rekordok használatát, mert a VIP módosíthatja a indítsa újra az alkalmazás-átjáró nem ajánlott.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
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

## <a name="next-steps"></a>Következő lépések

Ha szeretne konfigurálni a belső terheléselosztót használ, tekintse meg az Alkalmazásátjáró [hozzon létre egy alkalmazást a belső terheléselosztók](application-gateway-ilb.md).

Terheléselosztási beállítások általában kapcsolatos további információkért lásd:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
