---
title: Azure Traffic Manager alhálózat felülbírálása az Azure CLI használatával | Microsoft Docs
description: Ez a cikk segít megérteni, hogy Traffic Manager alhálózat felülbírálásával hogyan bírálhatja felül a Traffic Manager-profilok útválasztási módszerét, hogy a végfelhasználói IP-cím alapján, előre meghatározott IP-címeken keresztül irányítsa át a forgalmat a végpont-hozzárendelések számára.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351177"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Alhálózat Traffic Manager felülbírálása az Azure CLI használatával

Traffic Manager alhálózat felülbírálása lehetővé teszi egy profil útválasztási módszerének módosítását.  A felülbírálás hozzáadásával a rendszer a végfelhasználó IP-címe alapján, előre meghatározott IP-tartománnyal továbbítja a forgalmat a végpont-hozzárendeléshez. 

## <a name="how-subnet-override-works"></a>Az alhálózat felülbírálásának működése

Ha az alhálózat felülbírálásait egy Traffic Manager-profilhoz adja, Traffic Manager először ellenőrizze, hogy van-e alhálózat felülbírálva a végfelhasználó IP-címéhez. Ha az egyik található, a rendszer a felhasználó DNS-lekérdezését a megfelelő végpontra irányítja.  Ha nem talál leképezést, Traffic Manager a profil eredeti útválasztási metódusára kerül vissza. 

Az IP-címtartományok megadhatók CIDR-tartományként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8). Az egyes végpontokhoz társított IP-tartományoknak egyedinek kell lenniük a végpont számára. Az IP-címtartományok különböző végpontok közötti átfedése miatt a Traffic Manager elutasítja a profilt.

Az alhálózatok felülbírálásait két típusú útválasztási profil támogatja:

* **Földrajzi** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, a lekérdezés a végpont állapotának megfelelő állapotba irányítja a lekérdezést.
* **Teljesítmény** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, akkor a rendszer csak akkor irányítja át a forgalmat a végponthoz, ha az kifogástalan állapotú.  Ha az alhálózat felülbírálási végpontja nem kifogástalan állapotú, akkor a Traffic Manager vissza fog térni a teljesítmény-útválasztási heurisztikus művelethez.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager alhálózat felülbírálásának létrehozása

Traffic Manager alhálózat felülbírálásának létrehozásához az Azure CLI használatával adhatja hozzá a felülbírálás alhálózatait a Traffic Manager végponthoz.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Frissítse a Traffic Manager végpontot az alhálózat felülbírálásával.
Az Azure CLI használatával frissítse a végpontot az [az Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)paranccsal.

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Az IP-címtartományok eltávolításához futtassa az az [Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) parancsot a **--Remove** kapcsolóval.

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>További lépések
További információ a Traffic Manager [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).

Tudnivalók az [alhálózat forgalmáról – útválasztási módszer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)