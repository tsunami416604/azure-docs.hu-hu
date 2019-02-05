---
title: A FreeBSD csomagszűrőjének használata tűzfal létrehozása az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe a NAT-tűzfal használatával a FreeBSD PF az Azure-ban.
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
ms.openlocfilehash: 8cfa1696a18925e9e9e8b96299f1255875e85aa8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727263"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>A FreeBSD csomagszűrőjének használata biztonságos tűzfal létrehozása az Azure-ban
Ez a cikk bemutatja, hogyan NAT-tűzfal, a FreeBSD Packer szűrő keresztül az Azure Resource Manager-sablon használatával a webes gyakori forgatókönyv üzembe helyezéséhez.

## <a name="what-is-pf"></a>Mit jelent a PF?
PF (csomagszűrőt, is írt pf) BSD licencelt csomag szűrő, egy központi adott optimalizálóként működik a szoftver. PF azóta alakult gyorsan, és most már rendelkezik más elérhető tűzfalakon keresztül több előnye is van. Hálózati címfordítás (NAT) PF megalakulásától, majd csomagütemező és aktív várólista felügyeleti vannak integrálva, PF, a ALTQ integrálása, és így PF a konfigurálással állítható be. Feladatátvétel és a redundancia, a munkamenet-hitelesítés és az ftp-proxy megkönnyítése érdekében a nehezen FTP protokollt tűzfalas authpf pfsync és a carp-ot funkciókat is ki van bővítve PF. Röviden PF egy hatékony és gazdag tűzfal. 

## <a name="get-started"></a>Bevezetés
Ha érdeklik a biztonságos tűzfal beállítása a webkiszolgálók számára a felhőben, akkor az első lépések. A hálózati topológia beállítása az Azure Resource Manager-sablon kiadásban használt parancsfájlokra is alkalmazhat.
Az Azure Resource Manager-sablon beállítása a NAT /redirection PF és két FreeBSD virtuális gépek használata a Nginx webkiszolgáló telepítését és konfigurálását végző FreeBSD virtuális gépek. Mellett NAT keletkező kimenő forgalomért két webes kiszolgálók, a NAT/átirányítási virtuális gép elfogja a HTTP-kérelmekre, és átirányítja őket a két webkiszolgálók Ciklikus időszeleteléses módon. A virtuális hálózatot használ a privát nem átirányítható IP cím terület 10.0.0.2/24, és a sablon paramétereit. Az Azure Resource Manager-sablon is meghatározza egy útválasztási táblázatot az egész virtuális hálózat, amely a cél IP-cím alapján az Azure alapértelmezett útvonalakat felül külön útvonalak gyűjteménye. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Üzembe helyezése az Azure CLI-n keresztül
A legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index). Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az alábbi példa létrehoz egy erőforráscsoport-nevet `myResourceGroup` a a `West US` helyét.

```azurecli
az group create --name myResourceGroup --location westus
```

Ezután helyezze üzembe a sablont [a telepítő a freebsd pf](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment). Töltse le [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) alatt ugyanazt az útvonalat, és adja meg például a saját erőforráscsoport értékeit `adminPassword`, `networkPrefix`, és `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Öt perc múlva kap információit `"provisioningState": "Succeeded"`. Ezután is ssh az előtérbeli virtuális gép (NAT) vagy a hozzáférés Nginx-webkiszolgálót a nyilvános IP-cím vagy teljes Tartománynevét az előtérbeli virtuális gép (NAT) használatával. Az alábbi példa felsorolja a teljes tartománynév és az előtérbeli virtuális gép (NAT) hozzárendelt nyilvános IP-címet a `myResourceGroup` erőforráscsoportot. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>További lépések
Biztosan állítsa be a saját NAT az Azure-ban? Ingyenes, de hatékony, nyílt forráskód? Akkor PF használata jó választás. A sablon használatával [a telepítő a freebsd pf](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), csak akkor kell öt perc alatt állítsa be a NAT-tűzfal Ciklikus időszeleteléses FreeBSD használó terheléselosztási PF az Azure-ban a közös web server forgatókönyvhöz. 

Ha szeretné megtanulni a FreeBSD Azure-ban az elérhető, [Bevezetés a FreeBSD Azure-on](freebsd-intro-on-azure.md).

Ha szeretne többet megtudni az PF, [FreeBSD kézikönyv](https://www.freebsd.org/doc/handbook/firewalls-pf.html) vagy [PF – felhasználói útmutató](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
