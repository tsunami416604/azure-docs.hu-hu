---
title: Karbantartásszabályozás
description: Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuális gépek karbantartását hogyan kell alkalmazni a karbantartási vezérlők használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382729"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Előzetes verzió: a frissítések kezelése a karbantartási ellenőrzés és az Azure CLI használatával

A karbantartási vezérlő használatával olyan platform-frissítéseket kezelhet, amelyek nem igényelnek újraindítást. Az Azure gyakran frissíti az infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindításának javítása érdekében. A legtöbb frissítés átlátható a felhasználók számára. Bizonyos érzékeny munkaterhelések, például a játékok, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók nem tolerálják a virtuális gépek lefagyasztásának vagy leválasztásának néhány másodpercét. A karbantartási ellenőrzés lehetőséget biztosít a platform frissítéseinek megvárni, és egy 35 napos időszakon belül alkalmazza őket. 

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált Gazdagépekre.

A karbantartási ellenőrzéssel a következőket teheti:
- A Batch frissítése egyetlen frissítési csomagba.
- Várjon akár 35 napra a frissítések alkalmazásához. 
- A karbantartási időszak platform-frissítéseinek automatizálása Azure Functions használatával.
- A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek. 

> [!IMPORTANT]
> A karbantartási vezérlő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./linux/dedicated-hosts.md)kell lenniük, vagy egy elkülönített virtuálisgép- [mérettel](./linux/isolation.md)kell létrehozni.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak **erőforrás-közreműködői** hozzáféréssel kell rendelkeznie.


## <a name="install-the-maintenance-extension"></a>A karbantartási bővítmény telepítése

Ha úgy dönt, hogy helyileg telepíti az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t, a 2.0.76 vagy újabb verzióra van szükség.

Telepítse a `maintenance` előnézet CLI-bővítményt helyileg vagy Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Karbantartási konfiguráció létrehozásához használja a `az maintenance configuration create`. Ez a példa létrehoz egy *konfig* nevű karbantartási konfigurációt a gazdagépre. 

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

Másolja a konfigurációs azonosítót a kimenetből későbbi használatra.

A `--maintenanceScope host` használata biztosítja, hogy a karbantartási konfiguráció a gazdagép frissítéseinek vezérlésére szolgáljon.

Ha azonos nevű konfigurációt próbál létrehozni, de egy másik helyen, hibaüzenetet fog kapni. A konfiguráció nevének egyedinek kell lennie az előfizetésben.

Az elérhető karbantartási konfigurációk lekérdezését `az maintenance configuration list`használatával végezheti el.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>A konfiguráció kiosztása

Az `az maintenance assignment create` használatával rendelje hozzá a konfigurációt az elkülönített virtuális géphez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció AZONOSÍTÓjának használatával. Adja meg a `--resource-type virtualMachines`t, és adja meg a virtuális gép nevét `--resource-name`hoz, valamint az erőforráscsoportot a virtuális gép számára a `--resource-group`-ben, valamint a virtuális gép helyét `--location`számára. 

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

Ha a konfigurációt dedikált gazdagépre szeretné alkalmazni, meg kell adnia `--resource-type hosts`, `--resource-parent-name` a gazda csoport nevével, és `--resource-parent-type hostGroups`. 

A `--resource-id` paraméter a gazdagép azonosítója. Az az [VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) paranccsal kérheti le a DEDIKÁLT gazdagép azonosítóját.

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

Ellenőrizheti, hogy a konfiguráció helyesen lett-e alkalmazva, vagy ellenőrizze, hogy a konfiguráció jelenleg a `az maintenance assignment list`használatával van-e alkalmazva.

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

A `az maintenance update list` használatával ellenőrizze, hogy vannak-e függőben lévő frissítések. Frissítés – az előfizetés a virtuális gépet tartalmazó előfizetés AZONOSÍTÓjának kell lennie.

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

A függőben lévő frissítések alkalmazásához használja a `az maintenance apply update`. Sikeres művelet esetén a parancs a frissítés részleteit tartalmazó JSON-t ad vissza.

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

A frissítések állapotát a `az maintenance applyupdate get`használatával tekintheti meg. 

A frissítés neveként a `default` használhatja a legutóbbi frissítés eredményeinek megjelenítéséhez, vagy lecserélheti a `myUpdateName`t annak a frissítésnek a nevére, amelyet `az maintenance applyupdate create`futtatásakor adott vissza.

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

Karbantartási konfiguráció törléséhez használja a `az maintenance configuration delete`. A konfiguráció törlése eltávolítja a karbantartási ellenőrzést a kapcsolódó erőforrásokból.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Következő lépések
További információ: [karbantartás és frissítések](maintenance-and-updates.md).
