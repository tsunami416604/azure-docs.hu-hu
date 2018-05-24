---
title: Oktatóanyag – Virtuálisgép-méretezési csoport létrehozása Linux rendszerhez az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure CLI 2.0-t magas rendelkezésre állású alkalmazások létrehozásához és üzembe helyezéséhez Linux rendszerű virtuális gépeken, virtuálisgép-méretezési csoport használatával
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 741cabd37a5a508257f0307dfec25b5bb2d25153
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli-20"></a>Oktatóanyag: Virtuálisgép-méretezési csoport létrehozása és magas rendelkezésre állású alkalmazás üzembe helyezése Linuxon, az Azure CLI 2.0 használatával

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát skálázhatja manuálisan, vagy megadhat automatikus skálázási szabályokat is az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Ebben az oktatóanyagban egy virtuálisgép-méretezési csoportot fog létrehozni az Azure-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Skálázható alkalmazás létrehozása a cloud-init használatával
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása
> * Méretezésicsoport-példányok kapcsolatinformációinak megtekintése
> * Adatlemezek használata méretezési csoportokban

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Méretezési csoport – áttekintés
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoporton belüli virtuális gépek egy vagy több *elhelyezési csoportban* vannak elosztva a logikai meghibásodási és frissítési tartományok között. Ezek a csoportok a [rendelkezésre állási csoportokhoz](tutorial-availability-sets.md) hasonlóan azonos módon konfigurált virtuális gépekből állnak.

A virtuális gépek a méretezési csoportban igény szerint jönnek létre. Az automatikus skálázási szabályok megadásával beállíthatja, hogyan és mikor szeretne a virtuális gépeket hozzáadni vagy eltávolítani a méretezési csoportban. Ezek a szabályok a processzorterheléshez, a memóriahasználathoz és a hálózati forgalomhoz hasonló mérőszámok alapján aktiválhatók.

A méretezési csoportok legfeljebb 1000 virtuális gépek támogatására képesek, ha Azure platformrendszerképet használnak. A jelentős telepítési követelményekkel rendelkező, illetve a virtuális gépek jelentős testreszabását igénylő számítási feladatok esetén [egyéni rendszerkép létrehozására lehet szükség a virtuális gépekhez](tutorial-custom-images.md). Egyéni rendszerkép használatakor egy méretezési csoportban legfeljebb 300 virtuális gépet hozhat létre.


## <a name="create-an-app-to-scale"></a>Skálázható alkalmazás létrehozása
Éles környezetben szüksége lehet olyan [egyéni rendszerkép létrehozására a virtuális gépekhez](tutorial-custom-images.md), amely tartalmazza az üzembe helyezett és konfigurált alkalmazást. Ebben az oktatóanyagban elvégezzük a virtuális gépek testreszabását az első rendszerindításkor, hogy megfigyelhessük a méretezési csoport működését.

Egy korábbi oktatóanyagban megismerkedett azzal, hogy a [Linux virtuális gépek az első rendszerindításkor hogyan szabhatók testre](tutorial-automate-vm-deployment.md) a cloud-init használatával. Ugyanezzel a cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy egyszerű „Hello World” Node.js-alkalmazást. 

Az aktuális parancshéjban hozzon létre egy *cloud-init.txt* nevű fájlt, és illessze bele a következő konfigurációt. Például hozza létre a fájlt a Cloud Shellben, és ne a helyi gépén. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroupScaleSet* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal. A következő példa létrehoz egy *myScaleSet* nevű méretezési csoportot, a cloud-init fájl használatával elvégzi a virtuális gép testreszabását, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznek:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. Eltarthat még néhány percig, amíg hozzáférhet az alkalmazáshoz.


## <a name="allow-web-traffic"></a>Webforgalom engedélyezése
A terheléselosztó a virtuálisgép-méretezési csoport részeként automatikusan lett létrehozva. A terheléselosztó a terheléselosztó szabályait használó meghatározott virtuális gépek készletében osztja szét a forgalmat. A terheléselosztó alapelveivel és konfigurálásával kapcsolatban bővebben a következő, [Virtuális gépek terheléselosztása az Azure-ban](tutorial-load-balancer.md) című oktatóanyagban ismerkedhet meg.

Annak érdekében, hogy a forgalom elérhesse a webalkalmazást, hozzon létre egy szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Ha működés közben szeretné megtekinteni a Node.js-alkalmazást a weben, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) paranccsal. A következő példa a *myScaleSetLBPublicIP* a méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Írja be a nyilvános IP-címet egy webböngészőbe. Ekkor megjelenik az alkalmazás, és tartalmazza annak a virtuális gépnek a nevét, amelyhez a terheléselosztó forgalmat irányított:

![Node.js-alkalmazás futtatása](./media/tutorial-create-vmss/running-nodejs-app.png)

Ha kíváncsi a méretezési csoport működésére, kényszerítse a webböngésző frissítését, és tekintse meg, miként osztja el a terheléselosztó a forgalmat az alkalmazást futtató összes virtuális gép között.


## <a name="management-tasks"></a>Felügyeleti feladatok
A méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Az Azure CLI 2.0 gyors módszert kínál e feladatok elvégzéséhez. Lássunk néhány gyakori feladatot.

### <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportban futó virtuális gépek listájának megjelenítéséhez használja az [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) parancsot az alábbi módon:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

A kimenet a következő példához hasonló:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Virtuális gépek példányszámának növelése vagy csökkentése
A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss#az_vmss_show) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ezt követően az [az vmss scale](/cli/azure/vmss#az_vmss_scale) parancs használatával manuálisan növelheti vagy csökkentheti a méretezési csoportban futó virtuális gépek számát. Az alábbi példában a méretezési csoport virtuális gépeinek számát *3*-ra állítjuk:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Automatikus skálázási szabályok konfigurálása
A méretezési csoportban futó példányok számának manuális beállítása helyett automatikus skálázási szabályokat adhat meg. Ezek a szabályok figyelik a méretezési csoportban futó példányokat, és az Ön által megadott mérőszámok, illetve küszöbértékek alapján lépnek működésbe. Az alábbi példában a szabály eggyel növeli a példányok számát, amikor az átlagos processzorhasználat 5 percnél hosszabb ideig meghaladja a 60%-ot. Ha az átlagos processzorhasználat 5 percnél hosszabb ideig 30% alá csökken, a szabály eggyel csökkenti a példányok számát. Az előfizetés azonosítója segítségével hozhatók létre a méretezési csoport különböző összetevőihez tartozó erőforrás URI-k. A szabályok létrehozásához használja az [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) parancsot, és másolja, majd illessze be a következő automatikus skálázási parancsprofilt:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Az automatikus skálázási profil ismételt használatához hozzon létre egy (JavaScript Object Notation) JSON-fájlt és adja az `az monitor autoscale-settings create` parancsnak a `--parameters @autoscale.json` paraméterrel. Az automatikus skálázás tervezésével kapcsolatban bővebben az [automatikus skálázás ajánlott eljárásait](/azure/architecture/best-practices/auto-scaling) ismertető cikkben tájékozódhat.


### <a name="get-connection-info"></a>Kapcsolati adatok lekérése
A méretezési csoportokban futó virtuális gépek kapcsolati adatainak beszerzéséhez használja az [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) parancsot. Ez a parancs az SSH-val történő kapcsolódást engedélyező összes virtuális gép nyilvános IP-címét és portját adja vissza:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Adatlemezek használata méretezési csoportokkal
A méretezési csoportokkal adatlemezeket is létrehozhat és használhat. Egy korábbi oktatóanyagban megismerkedett az [Azure-lemezek kezelésével](tutorial-manage-disks.md). Az oktatóanyag az operációs rendszert tároló lemeztől eltérő adatlemezeken található alkalmazások létrehozására vonatkozó ajánlott eljárások és teljesítménnyel kapcsolatos fejlesztések bemutatását tartalmazta.

### <a name="create-scale-set-with-data-disks"></a>Adatlemezekkel rendelkező méretezési csoport létrehozása
Méretezési csoport létrehozásához és adatlemezek csatlakoztatásához adja hozzá a `--data-disk-sizes-gb` paramétert az [az vmss create](/cli/azure/vmss#az_vmss_create) parancshoz. Az alábbi példában egy *50* GB-os adatlemezekkel rendelkező méretezési csoportot hozunk létre:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Ha a rendszer eltávolít egy példányt a méretezési csoportból, akkor az ahhoz csatlakoztatott adatlemezek is el lesznek távolítva.

### <a name="add-data-disks"></a>Adatlemezek hozzáadása
Ha szeretne hozzáadni egy adatlemezt a méretezési csoport példányaihoz, használja az [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach) parancsot. Az alábbi példában minden egyes példányhoz hozzáadunk egy *50* GB-os lemezt:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Adatlemezek leválasztása
Ha szeretne eltávolítani egy adatlemezt a méretezési csoport példányairól, használja az [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach) parancsot. A következő példában eltávolítjuk a *2* LUN-számú adatlemezt:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy virtuálisgép-méretezési csoportot. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Skálázható alkalmazás létrehozása a cloud-init használatával
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása
> * Méretezésicsoport-példányok kapcsolatinformációinak megtekintése
> * Adatlemezek használata méretezési csoportokban

Folytassa a következő oktatóanyaggal, amely a virtuális gépekre vonatkozó terheléselosztási alapelveket ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása](tutorial-load-balancer.md)
