---
title: Az Azure-ban hozzon létre egy tűzfal freebsd rendszerű számítógépén csomag szűrő használatával |} Microsoft Docs
description: A NAT-tűzfalon freebsd rendszerű számítógépén használatával telepítése PF az Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 42312522381529075f544a6c277f66a4ef1982d2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904253"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Freebsd rendszerű számítógépén csomag szűrő használata biztonságos tűzfal létrehozása az Azure-ban
Ez a cikk központi telepítése a NAT-tűzfalon freebsd rendszerű a csomagoló szűrő Azure Resource Manager sablon segítségével használatával általános web server helyzetre be.

## <a name="what-is-pf"></a>Mi az a PF?
PF (csomagszűrők, is írt pf) BSD licencelt csomag szűrő, egy központi szoftvertermék a firewalling. PF azóta kialakulásának gyorsan, és most keresztül elérhető tűzfalak több előnye is van. Hálózati címfordítás (NAT) PF nap egyet, majd csomagütemező óta, és aktív várólista felügyeleti lettek integrálva a PF, a ALTQ integrálása, és így PF tartozó konfigurálással konfigurálható. A feladatátvételi és a redundancia, a munkamenet hitelesítési és ftp-proxy megkönnyítése érdekében a nehéz FTP protokollt firewalling authpf például pfsync és a CARP szolgáltatások is meghosszabbította PF. PF rövid, hatékony és gazdag tűzfal. 

## <a name="get-started"></a>Bevezetés
Ha szeretné a biztonságos tűzfal beállítása a webkiszolgálók a felhőben, majd lássunk neki. A parancsfájlok segítségével az Azure Resource Manager sablon telepítése a hálózati topológia is alkalmazhat.
Az Azure Resource Manager-sablon beállítása, amely végrehajtja a PF és két freebsd rendszerű virtuális gépek használata a webalkalmazás-kiszolgáló telepítése és konfigurálása a Nginx NAT /redirection freebsd rendszerű virtuális gép. Két webes kiszolgálók kimenő forgalom NAT elvégzése mellett a NAT/átirányítás virtuális gép elfogja a HTTP-kérelmekre, és átirányítja őket ciklikus multiplexeléssel két kiszolgálót. A virtuális hálózatot használ a titkos nem átirányítható IP cím terület 10.0.0.2/24, és módosíthatja a sablon paramétereit. Az Azure Resource Manager-sablon is meghatároz egy útválasztási táblázatot a teljes vnet, amely az IP-célcím alapján Azure alapértelmezett útvonalak felülbírálása külön útvonalak gyűjteménye. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Az Azure CLI segítségével végzi a telepítést
A legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoport neve `myResourceGroup` a a `West US` helyét.

```azurecli
az group create --name myResourceGroup --location westus
```

Ezután telepítse a sablon [pf-freebsd rendszerű-telepítés](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) rendelkező [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Töltse le [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) alatt ugyanazt az útvonalat, és határozza meg, mint a saját erőforrás értékek `adminPassword`, `networkPrefix`, és `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Öt perc múlva elérhetővé válik az adatokat `"provisioningState": "Succeeded"`. Ezután is ssh az előtér virtuális gép (NAT) vagy a hozzáférés a böngészőt a nyilvános IP-cím vagy FQDN-jét az előtér virtuális gép (NAT) Nginx-webkiszolgálón. Az alábbi példa felsorolja a teljes tartománynév és a nyilvános IP-címet, amely az előtér virtuális gép (NAT) rendelve a `myResourceGroup` erőforráscsoportot. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>További lépések
Szeretné beállítani az Azure-ban a saját NAT? Nyissa meg a forrást, szabad, de hatékony? PF akkor érdemes választani. A sablon használatával [pf-freebsd rendszerű-telepítés](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), csak akkor kell öt perc állítsa be a NAT-tűzfalon ciklikus multiplexelés freebsd rendszerű használó terheléselosztási PF az Azure-ban tartozó közös web server forgatókönyvhöz. 

Ha szeretne megtudni az Azure-ban freebsd rendszerű elérhető, tekintse meg a [Bevezetés az Azure freebsd rendszerű](freebsd-intro-on-azure.md).

Ha szeretne további információért PF, tekintse meg a [freebsd rendszerű kézikönyv](https://www.freebsd.org/doc/handbook/firewalls-pf.html) vagy [PF-útmutató](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
