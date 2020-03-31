---
title: Tűzfal létrehozása a FreeBSD csomagszűrőjének használatával az Azure-ban
description: További információ aRról, hogyan telepíthet NAT tűzfalat a FreeBSD PF használatával az Azure-ban.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945110"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>A FreeBSD csomagszűrőjének használata biztonságos tűzfal létrehozásához az Azure-ban
Ez a cikk bemutatja, hogyan telepíthet NAT tűzfalat a FreeBSD Packer Filter segítségével az Azure Resource Manager sablonon keresztül a gyakori webkiszolgálói forgatókönyvhöz.

## <a name="what-is-pf"></a>Mi az a PF?
Pf (Packet Filter, szintén írt pf) egy BSD licencelt állapotalapú csomagszűrő, egy központi szoftver tűzfal. Pf azóta gyorsan fejlődött, és most már számos előnye van a többi rendelkezésre álló tűzfalak. A hálózati címfordítás (NAT) az első naptól kezdve PF-ben van, majd a csomagütemező és az aktív várólista-kezelés integrálva lett a PF-be az ALTQ integrálásával és a PF konfigurációján keresztül konfigurálhatóvá tételével. Az olyan funkciók, mint a pfsync és carp a feladatátvételhez és a redundanciához, az authpf a munkamenet-hitelesítéshez és az ftp-proxy a nehéz FTP protokoll tűzfalazásának megkönnyítése érdekében szintén kiterjesztették a PF-et. Röviden, PF egy erős és funkciókban gazdag tűzfal. 

## <a name="get-started"></a>Bevezetés
Ha biztonságos tűzfalat szeretne létrehozni a felhőben a webkiszolgálói számára, akkor kezdjük el. Az Azure Resource Manager-sablonban használt parancsfájlokat is alkalmazhatja a hálózati topológia beállításához.
Az Azure Resource Manager sablon olyan FreeBSD virtuális gépet állított be, amely a Hálózati címlet /átirányítás tanusítást és két FreeBSD virtuális gépet használ, a Nginx webkiszolgáló telepítésével és konfigurálásával. A hálózati fordítás két webkiszolgáló kimenő forgalma mellett a NAT/átirányítás virtuális gép elfogja a HTTP-kérelmeket, és ciklikus multiplexelési módon átirányítja azokat a két webkiszolgálóra. A virtuális hálózat a 10.0.0.2/24 privát, nem irányítható IP-címteret használja, és módosíthatja a sablon paramétereit. Az Azure Resource Manager-sablon egy útvonaltáblát is definiál a teljes virtuális hálózathoz, amely az Azure alapértelmezett útvonalainak a cél IP-címe alapján történő felülbírálására használt egyes útvonalak gyűjteménye. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül
A legújabb [Azure CLI-t](/cli/azure/install-az-cli2) telepítenie kell, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index) Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz `myResourceGroup` egy `West US` erőforráscsoport nevét a helyen.

```azurecli
az group create --name myResourceGroup --location westus
```

Ezután telepítse a [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) sablont az [az csoport központi telepítésével](/cli/azure/group/deployment). Töltse le [az azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) fájlt ugyanazon elérési `adminPassword`út `networkPrefix`alatt, és adja meg a saját erőforrásértékeit, például a , és `domainNamePrefix`a. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Körülbelül öt perc múlva, akkor `"provisioningState": "Succeeded"`kap az információkat . Ezután ssh az előtér virtuális gép (NAT) vagy a Nginx webkiszolgáló egy böngészőben a nyilvános IP-cím vagy fqdn az előtér virtuális gép (NAT). A következő példa az erőforráscsoportban az előtér-virtuális géphez (NAT) `myResourceGroup` rendelt teljes tartománynév- és nyilvános IP-címet sorolja fel. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>További lépések
Szeretné beállítani saját NAT-ját az Azure-ban? Nyílt forráskódú, ingyenes, de erős? Akkor pf egy jó választás. A [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)sablon használatával mindössze öt percre van szükségünk ahhoz, hogy a FreeBSD PF használatával ciklikus multiplexeléses terheléselosztással rendelkező NAT tűzfalat állítsunk be az Azure-ban a közös webkiszolgáló-forgatókönyvekhez. 

Ha meg szeretnénk ismerni a FreeBSD ajánlatát az Azure-ban, [olvassa el az Azure-ban található FreeBSD bevezetést.](freebsd-intro-on-azure.md)

Ha többet szeretnénk tudni a PF-ről, látod a [FreeBSD kézikönyvét](https://www.freebsd.org/doc/handbook/firewalls-pf.html) vagy [a PF-User's Guide-ot.](https://www.freebsd.org/doc/handbook/firewalls-pf.html)
