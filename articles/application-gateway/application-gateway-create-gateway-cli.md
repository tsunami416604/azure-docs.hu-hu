---
title: "Hozzon létre egy alkalmazás - Azure CLI 2.0 |} Microsoft Docs"
description: "Ismerje meg az Alkalmazásátjáró létrehozása az Azure CLI 2.0 erőforrás-kezelő használatával."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 9d3732d538f3ed9ecb87247f378a3736692025ca
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Alkalmazásátjáró létrehozása az Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Az Azure klasszikus PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Az Azure Application Gateway egy dedikált virtuális készülék alkalmazás kézbesítési vezérlő (LÉPETT) biztosító szolgáltatásként, az alkalmazás különböző réteg 7 terheléselosztás képességeket kínál.

## <a name="cli-versions"></a>CLI-verziók

Alkalmazásátjáró hozhat létre a következő parancssori felület (CLI) verziók egyikével:

* [Az Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md): az Azure parancssori felület a klasszikus és az Azure Resource Manager üzembe helyezési modellek
* [Az Azure CLI 2.0](application-gateway-create-gateway-cli.md): a Resource Manager üzembe helyezési modellben a következő generációs CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>Előfeltétel: Az Azure parancssori felület 2.0 telepítése

Ebben a cikkben szereplő lépések végrehajtásához kell [macOS, a Linux és a Windows Azure CLI telepítése](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Alkalmazásátjáró létrehozása Azure-fiókra van szüksége. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzió](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megismerheti, hogyan hozhat létre olyan átjárót az Azure-portál használatával.

Ez a forgatókönyv tartalma:

* Hozzon létre egy közepes méretű Alkalmazásátjáró két példányt.
* Nevű AdatumAppGatewayVNET egy fenntartott CIDR-blokkja 10.0.0.0/16, a virtuális hálózat létrehozása.
* Létrehoz egy Appgatewaysubnet nevű alhálózatot, amelynek a CIDR-blokkja 10.0.0.0/28 lesz.

> [!NOTE]
> Az Alkalmazásátjáró, beleértve az egyéni állapotteljesítmény, a háttér-készlet címeket és a további szabályok további konfigurálása történik, az Alkalmazásátjáró létrehozása után, és nem a kezdeti üzembe helyezése során.

## <a name="before-you-begin"></a>Előkészületek

Alkalmazásátjáró saját alhálózatba van szükség. Virtuális hálózat létrehozásakor győződjön meg arról, hogy hagyja-e elegendő címtartomány több alhálózatra. Alkalmazásátjáró alhálózathoz telepítése után csak további alkalmazás átjárók alhálózaton is hozzáadhat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a **Microsoft Azure parancssori** , jelentkezzen be:

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Is `az login` az eszköz bejelentkezéshez írja be a kódot, aka.ms/devicelogin igénylő kapcsoló nélkül.

Miután megadta az előző parancs, kapni fog egy kódot. Navigáljon a böngészőben a bejelentkezési folyamat folytatásához https://aka.ms/devicelogin.

![cmd megjelenítő eszköz-bejelentkezés][1]

A böngészőben írja be a kapott kódot. Ez átirányítja Önt egy bejelentkezési oldalára.

![Írja be a kódját a böngésző][2]

Írja be a kódot a bejelentkezéshez, és zárja be a böngészőt, a folytatáshoz.

![sikeres volt][3]

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Az Alkalmazásátjáró létrehozása előtt hozzon létre egy erőforráscsoportot, azt tartalmazza. Használja az alábbi parancsot:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

A háttér IP-címek használatára a háttér-kiszolgáló IP-címekhez. Ezek az értékek privát IP-címek a virtuális hálózat, a nyilvános IP-címek vagy a háttér-kiszolgálók teljesen minősített tartománynév lehet. A következő példa olyan átjárót HTTP beállításait, a portok és a szabályok további konfigurációkat hoz létre:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

Az előző példában látható több tulajdonságok esetében nem szükséges Alkalmazásátjáró létrehozása közben. A következő kódrészlet példa olyan átjárót hoz létre a szükséges adatokat:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> A létrehozás során használt paraméterek listáját a következő parancsot: `az network application-gateway create --help`.

Ez a példa egy alapszintű application gateway alapértelmezett beállításait a figyelő, a háttér-készlet, a háttér-HTTP-beállítások és a szabályok hoz létre. Ezeket a beállításokat a központi telepítés megfelelően a kiépítés befejezését követően módosíthatja.

Ha a webes alkalmazás definiálva lett az előző lépésben a háttér-készlet, most terheléselosztás kezdődik.

## <a name="get-the-application-gateway-dns-name"></a>Az alkalmazás átjáró DNS-név beolvasása
Az átjáró létrehozása után konfigurálhat az előtér-kommunikációhoz. Egy nyilvános IP-cím használata esetén az Alkalmazásátjáró megköveteli egy dinamikusan hozzárendelt DNS-nevet, amely nincs rövid. Felhasználók is elérte az Alkalmazásátjáró érdekében használja egy olyan CNAME rekordot a nyilvános végpontot az Alkalmazásátjáró mutassanak. További információkért lásd: [használata Azure DNS számára adja meg az egyéni tartomány beállításait az Azure-szolgáltatások](../dns/dns-custom-domain.md).

Alias konfigurálásához beolvasása az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet az Alkalmazásátjáró csatolva PublicIPAddress elem használatával. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, amely mutat, a két webes alkalmazásokhoz, hogy a DNS-név. Ne használja A rekordok mert előfordulhat, hogy módosítsa a VIP indítsa újra az Alkalmazásátjáró.


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

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ez a cikk létrehozott összes erőforrást törli, a következő parancsot:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozzon létre egyéni állapotteljesítmény, keresse fel [a portál használatával hozzon létre egy egyéni mintavétel az Alkalmazásátjáró](application-gateway-create-probe-portal.md).

Konfigurálja az SSL-feladatkiszervezést, és tegye meg a költséges SSL visszafejtési ki a webkiszolgálók, lásd: [SSL kiszervezési Alkalmazásátjáró konfigurálása Azure Resource Manager használatával](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
