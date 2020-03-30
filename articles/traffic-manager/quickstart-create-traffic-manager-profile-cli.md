---
title: Rövid útmutató:Profil létrehozása az alkalmazások HA-jéhez - Azure CLI - Azure Traffic Manager
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magas rendelkezésre állású webalkalmazás létrehozásához.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531515"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Rövid útmutató: Traffic Manager-profil létrehozása magas rendelkezésre állású webalkalmazáshoz az Azure CLI használatával

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát hozza létre. Mindegyik egy másik Azure-régióban fut. A [végpontprioritás](traffic-manager-routing-methods.md#priority-traffic-routing-method)alapján hozzon létre egy Traffic Manager-profilt. A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges helyre irányítja. A Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt [az az hálózati forgalomkezelő profil létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) használatával, amely a felhasználói forgalmat a végpont prioritása alapján irányítja.

A következő példában cserélje le **<profile_name>** egy egyedi Traffic Manager-profilnévre.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Webalkalmazások létrehozása

Ehhez a rövid útmutatóhoz két példányra van szüksége egy webalkalmazáskét különböző Azure-régióban *(USA keleti régióban* és *Nyugat-Európában).* Mindegyik elsődleges és feladatátvételi végpontként fog szolgálni a Traffic Manager számára.

### <a name="create-web-app-service-plans"></a>Webalkalmazás-szolgáltatási csomagok létrehozása
Hozzon létre webalkalmazás-szolgáltatási csomagok at [az appservice terv létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) a két példány a webalkalmazás, amely két különböző Azure-régiókban telepíti.

A következő példában cserélje le **<appspname_eastus>,** és **<appspname_westeurope>** egy egyedi App Service-csomagnévre.

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása az alkalmazásszolgáltatási csomagban
Hozzon létre két példányt a webalkalmazás használatával [az az webapp létrehozása](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) az App Service-csomagok az USA *keleti* régiójában és *Nyugat-Európa* Azure-régiókban.

A következő példában cserélje le **<app1name_eastus>** és<app2name_westeurope **>** egy egyedi alkalmazásnévre, és cserélje le<**appspname_eastus>** és<**appspname_westeurope>** az előző szakaszban az App Service-csomagok létrehozásához használt névvel.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá a két webalkalmazást Traffic [Manager-végpontként az az hálózati forgalomkezelő-végpont létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) használatával a Traffic Manager-profilhoz az alábbiak szerint:

- Határozza meg a Web App-azonosítót, és adja hozzá a Web App található az *USA keleti részén* Azure régióban, mint az elsődleges végpont a felhasználói forgalom irányításához. 
- Határozza meg a Web App-azonosítót, és adja hozzá a *Nyugat-Európa* Azure-régióban található webalkalmazást feladatátvételi végpontként. 

Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra vezet.

A következő példában cserélje le **<app1name_eastus>** és<app2name_westeurope **>** az előző szakaszban az egyes régiókhoz létrehozott alkalmazásnevekre, cserélje le<**appspname_eastus>** és<appspname_westeurope **>** az előző szakaszban az App Service-csomagok létrehozásához használt névre, és cserélje le<**profile_name>** az előző szakaszban használt profilnévre. 

**Usa keleti keleti végpontja**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Jegyezze fel a kimenetben megjelenített azonosítót, és használja a következő parancsban a végpont hozzáadásához:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Nyugat-európai végpont**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Jegyezze fel a kimenetben megjelenített azonosítót, és használja a következő parancsban a végpont hozzáadásához:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>A Traffic Manager-profil tesztelése

Ebben a szakaszban ellenőrizni fogja a Traffic Manager-profil tartománynevét. Azt is konfigurálja, hogy az elsődleges végpont nem érhető el. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy a Traffic Manager elküldi a forgalmat a feladatátvételi végpontra.

A következő példában cserélje le **<app1name_eastus>** és<app2name_westeurope **>** az előző szakaszban az egyes régiókhoz létrehozott alkalmazásnevekre, cserélje le<**appspname_eastus>** és<appspname_westeurope **>** az előző szakaszban az App Service-csomagok létrehozásához használt névre, és cserélje le<**profile_name>** az előző szakaszban használt profilnévre.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a Traffic Manager-profil DNS-nevét az [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)segítségével.

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Másolja a **RelativeDnsName** értéket. A Traffic Manager-profil DNS-neve *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. A webböngészőben adja meg a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>.trafficmanager.net*) a Web App alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányít. **1-es prioritásra**van állítva.
2. A Traffic Manager feladatátvételének működés közbeni megtekintéséhez tiltsa le az elsődleges helyet az [az hálózati forgalomkezelő végpontfrissítésével.](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Másolja a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>.trafficmanager.net*) a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást [az az csoport törlése](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete)használatával.

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni a forgalom útválasztásáról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
