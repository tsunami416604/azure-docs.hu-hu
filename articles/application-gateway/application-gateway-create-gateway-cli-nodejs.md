---
title: Azure-alkalmazásátjáró létrehozása - Klasszikus Azure CLI
description: Megtudhatja, hogyan hozhat létre alkalmazásátjárót az Azure klasszikus CLI használatával az Erőforrás-kezelőben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312632"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Alkalmazásátjáró létrehozása az Azure CLI használatával

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az alkalmazásátjáró a következő alkalmazáskézbesítési funkciókkal rendelkezik: HTTP-terheléselosztás, cookie-alapú munkamenet-affinitás és Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL), kiszervezés, egyéni állapotminta-mintavételek és a többtelephelytámogatása.

## <a name="prerequisite-install-the-azure-cli"></a>Előfeltétel: Telepítse az Azure CLI-t

A cikkben ismertetett lépések végrehajtásához telepítenie kell [az Azure CLI-t,](../xplat-cli-install.md) és be kell [jelentkeznie az Azure-ban.](/cli/azure/authenticate-azure-cli) 

> [!NOTE]
> Ha nem rendelkezik Azure-fiókkal, szüksége van rá. Itt regisztrálhat az [ingyenes próbaverzióra](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megtudhatja, hogyan hozhat létre egy alkalmazásátjárót az Azure Portal használatával.

Ez a forgatókönyv:

* Hozzon létre egy közepes alkalmazásátjáró két példányban.
* Hozzon létre egy ContosoVNET nevű virtuális hálózatot egy 10.0.0.0/16-os fenntartott CIDR-blokkdal.
* Hozzon létre egy alhálózat nevű alhálózat01 használó 10.0.0.0/28 cidr blokk.

> [!NOTE]
> Az alkalmazásátjáró további konfigurálása, beleértve az egyéni állapotpróbákat, a háttérkészlet-címeket és a további szabályokat az alkalmazásátjáró konfigurálása után konfigurálják, és nem a kezdeti telepítés során.

## <a name="before-you-begin"></a>Előkészületek

Az Azure Application Gateway saját alhálózatot igényel. Virtuális hálózat létrehozásakor győződjön meg arról, hogy elegendő címterületet hagy több alhálózat hoz létre. Miután telepített egy alkalmazásátjárót egy alhálózatra, csak további alkalmazásátjárók adhatók hozzá az alhálózathoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a **Microsoft Azure parancssorát**, és jelentkezzen be.

```azurecli-interactive
az login
```

Az előző példa beírása után egy kód kerül megadva. Keresse https://aka.ms/devicelogin meg a böngészőben a bejelentkezési folyamat folytatásához.

![cmd a készülék bejelentkezését mutatja][1]

A böngészőben adja meg a kapott kódot. A visszavezetés egy bejelentkezési oldalra lesz átirányítva.

![böngésző beírni kódot][2]

Miután beírta a kódot, be van jelentkezve, zárja be a böngészőt, hogy folytassa a forgatókönyvvel.

![sikeresen bejelentkezett][3]

## <a name="switch-to-resource-manager-mode"></a>Váltás erőforrás-kezelő módra

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Az alkalmazásátjáró létrehozása előtt egy erőforráscsoport jön létre az alkalmazásátjáró tárolására. Az alábbiakban a parancs látható.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az erőforráscsoport létrehozása után létrejön egy virtuális hálózat az alkalmazásátjáróhoz.  A következő példában a címtér az előző forgatókönyv-megjegyzésekben meghatározott a 10.0.0.0/16 volt.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Alhálózat létrehozása

A virtuális hálózat létrehozása után egy alhálózat kerül hozzáadásra az alkalmazásátjáróhoz.  Ha azt tervezi, hogy az alkalmazásátjárót az alkalmazásátjáróval azonos virtuális hálózatban üzemeltetett webalkalmazással szeretné használni, hagyjon elegendő helyet egy másik alhálózat számára.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

A virtuális hálózat és az alhálózat létrehozása után az alkalmazásátjáró előfeltételei befejeződnek. Ezenkívül egy korábban exportált .pfx tanúsítvány és a tanúsítvány jelszava is szükséges a következő lépéshez: A háttérrendszerhez használt IP-címek a háttérkiszolgáló IP-címei. Ezek az értékek lehetnek privát IP-k a virtuális hálózatban, nyilvános ips, vagy teljesen minősített tartománynevek a háttérkiszolgálók.

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
> A létrehozás során megadható paraméterek listájához futtassa a következő parancsot: **azure network application-gateway create --help**.

Ebben a példában létrehoz egy alapszintű alkalmazásátjáróalapértelmezett beállításokat a figyelő, háttérkészlet, háttérrendszer http-beállítások és szabályok. Ezeket a beállításokat módosíthatja, hogy megfeleljen a központi telepítés, ha a kiépítés sikeres.
Ha már rendelkezik a webalkalmazás definiálva a háttérkészlet az előző lépésekben, miután létrehozta, terheléselosztás kezdődik.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre egyéni állapotmintaképeket [az Egyéni állapotminta létrehozása című ellátogat](application-gateway-create-probe-portal.md)

Ismerje meg, hogyan konfigurálhatja a TLS kiszervezését, és hogyan veheti le a költséges TLS-visszafejtést a webkiszolgálókról a [TLS kiszervezés konfigurálása](application-gateway-ssl-arm.md) webhelyen.

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
