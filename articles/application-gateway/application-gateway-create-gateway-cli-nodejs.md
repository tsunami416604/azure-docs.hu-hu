---
title: Az Azure Application Gateway - létrehozása az Azure klasszikus parancssori felület
description: Ismerje meg, hogyan hozhat létre egy Application Gateway az Azure klasszikus parancssori felület használatával a Resource Managerben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608461"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Application gateway létrehozása az Azure CLI-vel

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az Application gateway az alábbi alkalmazáskézbesítési funkciókkal rendelkezik: HTTP-terheléselosztást, cookie-alapú munkamenet-affinitás és Secure Sockets Layer (SSL) alapú kiszervezés, egyéni állapotmintákat, és a többhelyes támogatást.

## <a name="prerequisite-install-the-azure-cli"></a>Előfeltétel: Telepítse az Azure CLI-t

Ebben a cikkben szereplő lépések végrehajtásához kell [az Azure CLI telepítése](../xplat-cli-install.md) és kell [jelentkezzen be Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Ha nem rendelkezik Azure-fiókra, kell egyet. Itt regisztrálhat az [ingyenes próbaverzióra](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megtudhatja hogyan hozhat létre egy application gateway az Azure portal használatával.

Ebben a forgatókönyvben lesz:

* Egy közepes méretű Alkalmazásátjáró létrehozása két példánnyal.
* Hozzon létre egy virtuális hálózatot a 10.0.0.0/16 egy fenntartott CIDR-blokk ContosoVNET nevű.
* Hozzon létre egy, a CIDR-blokkja 10.0.0.0/28 használó subnet01 nevű alhálózatot.

> [!NOTE]
> További konfigurációs az application Gateway, beleértve az egyéni állapot-mintavételei, háttérbeli címkészlet-címeit, és a további szabályok vannak konfigurálva, az application gateway konfigurálása után, és nem a kezdeti üzembe helyezése során.

## <a name="before-you-begin"></a>Előkészületek

Az Azure Application Gateway a saját alhálózatában van szükség. Ha egy virtuális hálózatot hoz létre, győződjön meg arról, hogy hagyja-e elég hely a cím több alhálózattal rendelkezik. Miután telepít egy alhálózatot az application gateway, csak további az application Gateway átjárók tudnak lesz hozzáadva az alhálózathoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a **a Microsoft Azure-parancssort**, és jelentkezzen be.

```azurecli-interactive
az login
```

Miután beírta a fenti példa, egy kódot biztosítunk. Navigáljon a https://aka.ms/devicelogin egy böngészőben a bejelentkezési folyamat folytatása.

![bejelentkezés az cmd megjelenítő eszközön][1]

A böngészőben adja meg a kapott kódot. Ekkor megnyílik egy bejelentkezési oldalra.

![Adja meg a kódot a böngészőben][2]

Ha nincs megadva a kód van bejelentkezve, zárja be a böngészőt, és a forgatókönyv a folytatáshoz.

![sikeresen jelentkezett be][3]

## <a name="switch-to-resource-manager-mode"></a>Váltson Resource Manager módra

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Az application gateway létrehozása előtt egy erőforráscsoportot az application gateway tartalmazó jön létre. Az alábbiakban a parancs látható.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Miután létrejött az erőforráscsoport, egy virtuális hálózatot az application gateway létrejön.  A következő példában a címtér lett 10.0.0.0/16, ahogyan az a fenti forgatókönyv megjegyzéseket.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Alhálózat létrehozása

A virtuális hálózat létrehozása után egy alhálózatot az application gateway számára egészül ki.  Ha azt tervezi, az application gateway ugyanazon a virtuális hálózaton lévő üzemeltetett webalkalmazások az application gateway használatára, mindenképpen hagyja bejelölve elég hellyel rendelkezzen a egy másik alhálózatot.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

A virtuális hálózat és alhálózat létrehozása után az application gateway számára az előfeltételek teljesülnek. Emellett a korábban exportált .pfx tanúsítvány és a jelszót a tanúsítványhoz szükség, a következő lépés: A használt biztosítani a háttérbeli IP-címek a háttérkiszolgáló az IP-címek. Ezek az értékek lehetnek magánhálózati IP-címek a virtuális hálózatban, nyilvános IP-címek vagy teljesen minősített tartománynevet a háttérkiszolgálókhoz.

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
> Paraméterek, amelyeket a következő parancs futtatásával a létrehozás során adható meg listáját: **az azure network application-gateway create--help**.

Ez a példa létrehoz egy alapszintű application gateway a figyelő, a háttérkészlet, a háttérbeli http-beállítások és a szabályok az alapértelmezett beállításokkal. Ezek a beállítások igény szerint a telepítés után a kiépítés sikeres módosíthatja.
Ha már van definiálva az előző lépésekben, ezután a háttérkészlet webalkalmazása terheléselosztás kezdődik.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre egyéni állapotmintákat funkcionáló [hozzon létre egy egyéni](application-gateway-create-probe-portal.md)

Ismerje meg, hogyan konfigurálja az SSL-kiürítés, és a költséges SSL visszafejtési Ön webkiszolgálóiról funkcionáló [SSL kiürítési konfigurálása](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
