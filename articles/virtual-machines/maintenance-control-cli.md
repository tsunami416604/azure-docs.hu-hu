---
title: Azure-beli virtuális gépek karbantartási vezérlése a parancssori felület használatával
description: Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuális gépek karbantartását hogyan alkalmazza a rendszer a karbantartási és a parancssori felület használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/20/2020
ms.author: cynthn
ms.openlocfilehash: 4843b4769e31748fd5f624005792c604db18f11e
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137501"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Frissítések kezelése a karbantartási ellenőrzés és az Azure CLI használatával

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre. Ez a témakör a karbantartási vezérlők Azure CLI-beállításait ismerteti. További információ a karbantartási ellenőrzés, a hozzá tartozó korlátozások és egyéb felügyeleti lehetőségek használatáról: a [platform frissítéseinek kezelése a karbantartási ellenőrzéssel](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

A `az maintenance configuration create` használatával karbantartási konfigurációt hozhat létre. Ez a példa létrehoz egy *konfig* nevű karbantartási konfigurációt a gazdagépre. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location eastus
```

Másolja a konfigurációs azonosítót a kimenetből későbbi használatra.

A `--maintenanceScope host` használatával biztosíthatja, hogy a rendszer a karbantartási konfigurációt használja a gazdagép frissítéseinek vezérlésére.

Ha azonos nevű konfigurációt próbál létrehozni, de egy másik helyen, hibaüzenetet fog kapni. A konfiguráció nevének egyedinek kell lennie az előfizetésben.

Az elérhető karbantartási konfigurációk lekérdezését a használatával `az maintenance configuration list`végezheti el.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>A konfiguráció kiosztása

A `az maintenance assignment create` (z) használatával rendelje hozzá a konfigurációt az elkülönített virtuális géphez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció AZONOSÍTÓjának használatával. Adja `--resource-type virtualMachines` meg és adja meg a virtuális gép nevét `--resource-name`, valamint a virtuális géphez tartozó ERŐFORRÁSCSOPORTOT `--resource-group`, valamint a virtuális gép helyét. `--location` 

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

Ha egy dedikált gazdagépre kívánja alkalmazni a konfigurációt, a `--resource-type hosts`nevet `--resource-parent-name` is tartalmaznia kell a gazda csoport nevével és `--resource-parent-type hostGroups`. 

A paraméter `--resource-id` a gazdagép azonosítója. Az az [VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) paranccsal kérheti le a DEDIKÁLT gazdagép azonosítóját.

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

## <a name="check-configuration"></a>Konfiguráció keresése

Ellenőrizheti, hogy a konfiguráció helyesen lett-e alkalmazva, vagy ellenőrizze, hogy jelenleg milyen konfigurációt `az maintenance assignment list`alkalmaz a használatával.

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

A `az maintenance update list` használatával megtekintheti, hogy vannak-e függőben lévő frissítések. Frissítés – az előfizetés a virtuális gépet tartalmazó előfizetés AZONOSÍTÓjának kell lennie.

Ha nincsenek frissítések, a parancs egy hibaüzenetet ad vissza, amely a következő szöveget fogja tartalmazni: `Resource not found...StatusCode: 404`.

Ha vannak frissítések, akkor is csak egy lesz visszaadva, még akkor is, ha folyamatban van több frissítés. A frissítéshez tartozó adatértékek egy objektumban lesznek visszaadva:

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

A függőben lévő frissítések keresése egy elkülönített virtuális géphez. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedikált gazdagép

A függőben lévő frissítések keresése egy dedikált gazdagépen. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében. Cserélje le az erőforrások értékeit a saját adataira.

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

A `az maintenance apply update` függőben lévő frissítések alkalmazására használható. Sikeres művelet esetén a parancs a frissítés részleteit tartalmazó JSON-t ad vissza.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy, az elkülönített virtuális gépre vonatkozó frissítések alkalmazására vonatkozó kérelmet.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikált gazdagép

Frissítések alkalmazása dedikált gazdagépre.

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

## <a name="check-the-status-of-applying-updates"></a>Frissítések alkalmazási állapotának keresése 

A frissítések állapotát a használatával `az maintenance applyupdate get`lehet megtekinteni. 

A frissítés neveként `default` használhatja az utolsó frissítés eredményét, vagy lecserélheti `myUpdateName` a értéket a futtatásakor visszaadott frissítés nevére. `az maintenance applyupdate create`

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
A LastUpdateTime az az idő, amikor a frissítés befejeződött, vagy Ön által kezdeményezett, vagy a platformon, ha az önkarbantartási időszakot nem használták. Ha még soha nem történt frissítés a karbantartási ellenőrzésen, akkor az alapértelmezett értéket fogja megjeleníteni.

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

Karbantartási `az maintenance configuration delete` konfiguráció törlésére használható. A konfiguráció törlése eltávolítja a karbantartási ellenőrzést a kapcsolódó erőforrásokból.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>További lépések
További információ: [karbantartás és frissítések](maintenance-and-updates.md).
