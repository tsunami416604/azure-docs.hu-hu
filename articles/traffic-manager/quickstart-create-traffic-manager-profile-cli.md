---
title: 'Gyors útmutató: profil létrehozása az alkalmazások számára – Azure CLI – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magasan elérhető webalkalmazás létrehozásához.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: allensu
ms.openlocfilehash: 8b8880e10f9b920a2ec077d4cc4c2239e6ea7438
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034219"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Gyors útmutató: Traffic Manager profil létrehozása egy magasan elérhető webalkalmazáshoz az Azure CLI használatával

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre olyan Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát fogja létrehozni. Mindegyik egy másik Azure-régióban fut. A Traffic Manager-profilt a [végpont prioritása](traffic-manager-routing-methods.md#priority)alapján hozza létre. A profil a webes alkalmazást futtató elsődleges helyre irányítja a felhasználói forgalmat. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helyhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt [az az Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) paranccsal, amely a felhasználói forgalmat a végponti prioritás alapján irányítja.

A következő példában cserélje le a **< profile_name >** egy egyedi Traffic Manager-profil nevére.

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

Ebben a rövid útmutatóban két különböző Azure-régióban (az*USA keleti* régiójában és *Nyugat-Európában*) üzembe helyezett webalkalmazás két példánya szükséges. Mindegyik a Traffic Manager elsődleges és feladatátvételi végpontjának fogja szolgálni.

### <a name="create-web-app-service-plans"></a>Web App Service-csomagok létrehozása
Hozzon létre Web App Service-csomagokat az az [appservice Plan Create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) paranccsal a webalkalmazás két különböző Azure-régióban üzembe helyezett példányaihoz.

A következő példában cserélje le a **< appspname_eastus >** és **< appspname_westeurope >** egyedi app Service csomagjának nevére

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása az App Service-csomagban
Hozzon létre két példányt a webalkalmazás az [az WebApp Create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) PARANCCSAL az *USA keleti* régiójában és a *Nyugat-európai* Azure-régióban található app Service-csomagokban.

Az alábbi példában cserélje le a **< app1name_eastus >** és **< App2name_westeurope** > egyedi névvel, és cserélje le **< appspname_eastus** > és **<** appspname_westeurope > az előző szakaszban app Service csomagok létrehozásához használt névvel.

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
Adja hozzá a két Web Apps Traffic Manager végpontként az [az Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) paranccsal a Traffic Manager profilhoz az alábbiak szerint:

- Határozza meg a webalkalmazás azonosítóját, és adja hozzá az *USA keleti* régiója Azure-régióban található webalkalmazást elsődleges végpontként az összes felhasználói forgalom átirányításához. 
- Determinet a webalkalmazás azonosítóját, és adja hozzá a Nyugat- *Európai* Azure-régióban található webalkalmazást feladatátvételi végpontként. 

Ha az elsődleges végpont nem érhető el, a forgalom automatikusan átirányítja a feladatátvételi végpontot.

Az alábbi példában cserélje le a **< app1name_eastus >** és **< app2name_westeurope >** az előző szakaszban szereplő egyes régiókhoz létrehozott alkalmazásokra, cserélje le **< appspname_eastus** > és **<** appspname_westeurope > az előző szakaszban az App Service-csomagok létrehozásához használt névvel, és cserélje le az **< profile_name >** az előző szakaszban használt profil nevére. 

**USA keleti végpontja**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Jegyezze fel a kimenetben megjelenített azonosítót, és használja a következő parancsot a végpont hozzáadásához:

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
Jegyezze fel a kimenetben megjelenített azonosítót, és használja a következő parancsot a végpont hozzáadásához:

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

## <a name="test-your-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a Traffic Manager profil tartománynevét fogja megtekinteni. Azt is beállíthatja, hogy az elsődleges végpont ne legyen elérhető. Végezetül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy Traffic Manager továbbítja a forgalmat a feladatátvételi végpontnak.

Az alábbi példában cserélje le a **< app1name_eastus >** és **< app2name_westeurope >** az előző szakaszban szereplő egyes régiókhoz létrehozott alkalmazásokra, cserélje le **< appspname_eastus** > és **<** appspname_westeurope > az előző szakaszban az App Service-csomagok létrehozásához használt névvel, és cserélje le az **< profile_name >** az előző szakaszban használt profil nevére.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg az Traffic Manager profil DNS-nevét az [az Network Traffic-Manager Profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)paranccsal.

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Másolja a **RelativeDnsName** értéket. A Traffic Manager profil DNS-neve a *http://<* relativednsname *>. trafficmanager támogatása. net*. 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. A webböngészőben adja meg a Traffic Manager profiljának DNS-nevét (*http://<* relativednsname *>. trafficmanager támogatása. net*) a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányítja át. Az **1-es prioritásra**van beállítva.
2. Ha Traffic Manager feladatátvételt szeretné megtekinteni a működés közben, tiltsa le az elsődleges helyet az [az Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)paranccsal.

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Másolja a Traffic Manager profiljának DNS-nevét (*http://<* relativednsname *>. trafficmanager támogatása. net*), hogy megtekintse a webhelyet egy új böngésző-munkamenetben.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportot, a webalkalmazásokat és az összes kapcsolódó erőforrást az [az Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete)paranccsal.

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni az útválasztási forgalomról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
