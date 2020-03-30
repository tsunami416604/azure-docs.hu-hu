---
title: Karbantartásszabályozás
description: Megtudhatja, hogyan szabályozhatja, hogy mikor alkalmazza a karbantartást az Azure-beli virtuális gépeken a Maintenance Control használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250178"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Előzetes verzió: Frissítések vezérlése a Karbantartás-vezérléssel és az Azure CLI-vel

A karbantartási vezérlés használatával nem igényel újraindítást igénylő platformfrissítések kezelése. Az Azure gyakran frissíti infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindítása érdekében. A legtöbb frissítés a felhasználók számára átlátható. Egyes bizalmas számítási feladatok, például a játék, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók, még néhány másodpercig sem tolerálhatják a virtuális gépek karbantartás vagy leválasztását. A karbantartás-vezérlés lehetővé teszi, hogy megvárja a platformfrissítéseit, és alkalmazza azokat egy 35 napos gördülő ablakon belül. 

A karbantartás-vezérlés lehetővé teszi annak eldöntését, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre.

A karbantartás vezérlésével a következőkre van lehetőség:
- A kötegfrissítések egyetlen frissítési csomagba kerülnek.
- Várjon akár 35 napot a frissítések alkalmazásával. 
- Az Azure Functions használatával automatizálhatja a karbantartási időszak platformfrissítéseit.
- A karbantartási konfigurációk előfizetések és erőforráscsoportok között működnek. 

> [!IMPORTANT]
> A Karbantartás vezérlés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./linux/dedicated-hosts.md)kell lenniük, vagy [elkülönített virtuálisgép-mérethasználatával](./linux/isolation.md)kell létrehozniuk.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak rendelkeznie kell **erőforrás-közreműködői** hozzáféréssel.


## <a name="install-the-maintenance-extension"></a>A karbantartási bővítmény telepítése

Ha úgy dönt, hogy helyileg telepíti az [Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.76-os vagy újabb verzióra van szüksége.

Telepítse `maintenance` az előzetes CLI-bővítményt helyileg vagy a Cloud Shellben. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Karbantartási `az maintenance configuration create` konfiguráció létrehozásához használható. Ebben a példában létrehoz egy karbantartási konfiguráció nevű *myConfig* hatókörrel a gazdagép. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Másolja a konfigurációs azonosítót a kimenetből, amelyet később használni szeretne.

A `--maintenanceScope host` használata biztosítja, hogy a karbantartási konfiguráció t használja a gazdagép frissítéseinek vezérléséhez.

Ha azonos nevű, de más helyen lévő konfigurációt próbál létrehozni, hibaüzenetet kap. A konfigurációneveknek egyedinek kell lenniük az előfizetésben.

Az elérhető karbantartási konfigurációkat `az maintenance configuration list`a használatával kérdezheti le.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>A konfiguráció hozzárendelése

A `az maintenance assignment create` konfiguráció hozzárendelése az elkülönített virtuális gép hez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció azonosítójának használatával. Adja `--resource-type virtualMachines` meg és adja meg `--resource-name`a virtuális gép nevét és a `--resource-group`virtuális gép erőforráscsoportját `--location`a alkalmazásban, valamint a virtuális gép helyét. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedikált gazdagép

Ha egy dedikált állomásra szeretne konfigurációt `--resource-type hosts` `--resource-parent-name` alkalmazni, a nevét a `--resource-parent-type hostGroups`állomáscsoportnevével és a. 

A `--resource-id` paraméter az állomás azonosítója. Használhatja [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) a dedikált gazdagép azonosítójának leigazolásához.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Konfiguráció ellenőrzése

Ellenőrizheti, hogy a konfiguráció megfelelően lett-e alkalmazva, vagy `az maintenance assignment list`ellenőrizheti, hogy jelenleg milyen konfiguráció van alkalmazva a használatával.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedikált gazdagép 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Függőben lévő frissítések keresése

Ezzel `az maintenance update list` ellenőrizve, hogy vannak-e függőben lévő frissítések. Frissítés --előfizetés a virtuális gép-előfizetés azonosítója.

Ha nincsenek frissítések, a parancs hibaüzenetet ad vissza, `Resource not found...StatusCode: 404`amely a következő szöveget tartalmazza: .

Ha vannak frissítések, csak egy lesz vissza, akkor is, ha több frissítés van függőben. A frissítés adatai egy objektumban lesznek visszaadva:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Ellenőrizze a függőben lévő frissítések egy elkülönített virtuális gép. Ebben a példában a kimenet az olvashatóság érdekében táblázatként van formázva.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedikált gazdagép

Egy dedikált állomás függőben lévő frissítéseinek keresése. Ebben a példában a kimenet az olvashatóság érdekében táblázatként van formázva. Cserélje le az erőforrások értékeit a sajátjára.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Frissítések alkalmazása

A `az maintenance apply update` függőben lévő frissítések alkalmazásához használható. A sikeres, ez a parancs a dlon vissza a frissítés részleteit tartalmazó JSON.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy kérelmet egy elkülönített virtuális gép frissítések alkalmazására.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikált gazdagép

Frissítések alkalmazása dedikált állomásra.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>A frissítések alkalmazásának ellenőrzése 

A frissítések előrehaladását a segítségével `az maintenance applyupdate get`ellenőrizheti. 

A frissítés `default` neveként használhatja az utolsó frissítés eredményeit, vagy lecserélheti `myUpdateName` a rendszera `az maintenance applyupdate create`rendszerre a rendszer a rendszer futtatásakor visszaadott frissítés nevét.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime lesz az az idő, amikor a frissítés befejeződött, akár Ön által kezdeményezett, vagy a platform, ha önfenntartó ablak nem használták. Ha a karbantartás-ellenőrzésen keresztül még soha nem alkalmazták a frissítést, akkor az alapértelmezett értéket jeleníti meg.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedikált gazdagép

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Karbantartási konfiguráció törlése

Karbantartási `az maintenance configuration delete` konfiguráció törlésére használható. A konfiguráció törlésével eltávolítja a karbantartási vezérlőt a társított erőforrásokból.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>További lépések
További információ: [Karbantartás és frissítések](maintenance-and-updates.md).
