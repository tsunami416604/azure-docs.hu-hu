---
title: Az Azure Traffic Manager alhálózati felülbírálása az Azure CLI használatával | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy a Traffic Manager alhálózati felülbíráláshogyan felülbírálható a Traffic Manager-profil útválasztási metódusának felülbírálása a végfelhasználói IP-cím alapján a végfelhasználói IP-cím alapján, előre definiált IP-tartomány-végpontleképezéseken keresztül.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938467"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager alhálózati felülbírálás az Azure CLI használatával

A Traffic Manager alhálózati felülbírálás lehetővé teszi a profil útválasztási módjának módosítását.  A felülbírálás hozzáadása a végfelhasználó IP-címe alapján irányítja a forgalmat egy előre meghatározott IP-tartomány-végpont leképezéssel. 

## <a name="how-subnet-override-works"></a>Az alhálózat-felülbírálás működése

Amikor alhálózati felülbírálásokat ad hozzá egy forgalomkezelő profilhoz, a Traffic Manager először ellenőrzi, hogy van-e alhálózati felülbírálás a végfelhasználó IP-címéhez. Ha talál egyet, a felhasználó DNS-lekérdezése a megfelelő végpontra lesz irányítva.  Ha nem talál leképezést, a Traffic Manager visszatér a profil eredeti útválasztási metódusához. 

Az IP-címtartományok cidr-tartományokként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8) adhatók meg. Az egyes végpontokhoz társított IP-tartományoknak egyedinek kell lenniük az adott végponthoz képest. A különböző végpontok közötti IP-tartományok átfedése a Traffic Manager által elutasított profilt eredményezi.

Az útválasztási profiloknak két típusa van, amelyek támogatják az alhálózati felülbírálásokat:

* **Földrajzi** – Ha a Traffic Manager alhálózati felülbírálást talál a DNS-lekérdezés IP-címéhez, a lekérdezést a végpont állapotátől függetlenül a végponthoz irányítja.
* **Teljesítmény** – Ha a Traffic Manager alhálózati felülbírálása a DNS-lekérdezés IP-címét, akkor csak a forgalmat a végpontra, ha kifogástalan.  Traffic Manager visszaesik a teljesítmény-útválasztási heurisztikus, ha az alhálózati felülbírálási végpont nem kifogástalan.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager-alhálózat-felülbírálás létrehozása

Traffic Manager-alhálózat-felülbírálás létrehozásához az Azure CLI segítségével hozzáadhatja a felülbírálás alhálózatait a Traffic Manager-végponthoz.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Frissítse a Traffic Manager végpontot az alhálózati felülbírálással.
Az Azure CLI segítségével frissítheti a végpontot az [az hálózati forgalomkezelő végpontfrissítésével.](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)

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

Az IP-címtartományokat az [az hálózati forgalomkezelő végpontfrissítésének](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) futtatásával távolíthatja el a **--remove** kapcsolóval.

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Következő lépések
További információ a Traffic Manager [forgalomirányítási módszereiről.](traffic-manager-routing-methods.md)

További információ az [Alhálózati forgalom-útválasztási módszerről](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)