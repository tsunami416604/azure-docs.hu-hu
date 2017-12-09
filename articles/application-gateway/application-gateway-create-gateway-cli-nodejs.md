---
title: "Hozzon létre egy Azure Application Gateway - Azure CLI 1.0 |} Microsoft Docs"
description: "Útmutató Alkalmazásátjáró létrehozása az Azure CLI 1.0 erőforrás-kezelő használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: fe50fb3a7434702101dc5ae7a9dd176a33423119
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Alkalmazásátjáró létrehozása az Azure parancssori felület használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Alkalmazásátjáró a következő alkalmazás kézbesítési funkciókkal rendelkezik: HTTP terheléselosztás, a munkamenet cookie-alapú kapcsolat és a Secure Sockets Layer (SSL) kiszervezési egyéni állapotfigyelő mintavételt betölteni, és többhelyes támogatás.

## <a name="prerequisite-install-the-azure-cli"></a>Előfeltétel: Az Azure parancssori felület telepítése

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (Azure CLI)](../xplat-cli-install.md) és kell [jelentkezzen be Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Ha az Azure-fiók nem rendelkezik, meg kell egyet. Itt regisztrálhat az [ingyenes próbaverzióra](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben ismerje meg az Azure portál használatával Alkalmazásátjáró létrehozása.

Ez a forgatókönyv tartalma:

* Hozzon létre egy közepes méretű Alkalmazásátjáró két példányt.
* Nevű ContosoVNET egy fenntartott CIDR-blokkja 10.0.0.0/16, a virtuális hálózat létrehozása.
* A CIDR-blokkja 10.0.0.0/28 használó subnet01 nevű alhálózat létrehozása.

> [!NOTE]
> Az Alkalmazásátjáró, beleértve az egyéni állapotfigyelő további konfigurációs vizsgálat, háttér címkészletet címeket, és további szabályok is konfigurálva van az Alkalmazásátjáró konfigurálása után, és nem a kezdeti üzembe helyezése során.

## <a name="before-you-begin"></a>Előkészületek

Az Azure Application Gateway saját alhálózatba van szükség. Amikor egy virtuális hálózat létrehozásával, győződjön meg arról, hogy hagyja-e elég hely a cím több alhálózattal rendelkezik. Miután telepít egy alhálózatra Alkalmazásátjáró, csak további alkalmazásátjárót képesek fel kell venni az alhálózat.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Nyissa meg a **Microsoft Azure parancssori**, és jelentkezzen be. 

```azurecli-interactive
azure login
```

Miután beírta a fenti példában, a kód valósul meg. Nyissa meg a böngészőben a bejelentkezési folyamat folytatásához https://aka.ms/devicelogin.

![cmd megjelenítő eszköz-bejelentkezés][1]

A böngészőben írja be a kapott kódot. Ekkor megnyílik egy bejelentkezési oldalára.

![Írja be a kódját a böngésző][2]

Ha nincs megadva a kód be van jelentkezve, zárja be a böngészőt, és a forgatókönyv a folytatáshoz.

![sikeres volt][3]

## <a name="switch-to-resource-manager-mode"></a>Váltson Resource Manager módra

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Az Alkalmazásátjáró létrehozása, mielőtt egy erőforráscsoportot hoz létre az Alkalmazásátjáró. Az alábbiakban a parancs látható.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az erőforráscsoport létrehozása után az Alkalmazásátjáró létrejön egy virtuális hálózatot.  A következő példában a címterület volt 10.0.0.0/16, a fenti forgatókönyv kiegészítő meghatározottak szerint.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Alhálózat létrehozása

A virtuális hálózat létrejötte után, az alkalmazás átjáró alhálózat jelenik meg.  Ha egy webalkalmazást, mint az Alkalmazásátjáró azonos virtuális hálózatban lévő üzemeltetett Alkalmazásátjáró használni, ügyeljen arra, hogy hagyjon elegendő helyet egy másik alhálózat.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

A virtuális hálózati és alhálózati létrehozása után az Alkalmazásátjáró szükséges előfeltételek teljesülnek. Emellett a korábban exportált .pfx tanúsítvány és a jelszót a tanúsítványhoz szükség, a következő lépés: a háttéralkalmazás használja az IP-címek a háttérkiszolgáló az IP-címet. Ezek az értékek lehetnek privát IP-címek a virtuális hálózat, nyilvános IP-cím vagy teljes tartománynevét a háttérkiszolgálókhoz.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> A következő parancs létrehozása során megadható paramétereket listáját: **azure hálózati Alkalmazásátjáró létrehozása – súgó**.

Ez a példa egy alapszintű application gateway alapértelmezett beállításait a figyelő, a háttérkészlet, a backendhttpsettings osztályhoz és a szabályok hoz létre. Ezeket a beállításokat a központi telepítés megfelelően, ha a kiépítés sikeres módosíthatja.
Ha már van definiálva az előző lépésekben, a létrehozását követően, a háttérkészletben webalkalmazásokat terheléselosztás kezdődik.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozzon létre egyéni állapotteljesítmény ellátogatva [hozzon létre egy egyéni állapotmintáihoz](application-gateway-create-probe-portal.md)

Megtudhatja, hogyan konfigurálja az SSL-Feladatkiszervezést, és tegye meg a költséges SSL visszafejtési ki a webkiszolgálók ellátogatva [SSL-kiszervezés konfigurálása](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
