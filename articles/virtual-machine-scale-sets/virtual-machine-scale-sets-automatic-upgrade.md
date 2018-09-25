---
title: Operációs rendszer automatikus verziófrissítése, az Azure-beli virtuálisgép-méretezési csoportokban |} A Microsoft Docs
description: Ismerje meg, az operációs rendszer egy méretezési csoportban lévő Virtuálisgép-példányok automatikus frissítése
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 935b3ff0fe03984b02dc2e1137f48e53b06ce0c2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995108"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure-beli virtuálisgép-méretezési csoport operációs rendszer automatikus verziófrissítése

Automatikus operációsrendszer-lemezkép frissítés előzetes verziójú funkció Azure-beli virtuálisgép-méretezési csoportokhoz, amely automatikusan frissíti a legújabb operációsrendszer-lemezkép összes virtuális gép.

Operációs rendszer automatikus verziófrissítése a következő jellemzőkkel rendelkezik:

- A beállítása után rendszerképet kiadók által közzétett legújabb operációsrendszer-lemezkép a rendszer automatikusan alkalmazza a méretezési csoport felhasználói beavatkozás nélkül.
- Frissíti a példányok kötegek működés közbeni módon minden alkalommal, amikor a közzétevő által közzétett új platform-lemezképet.
- Integrálható az alkalmazás állapotadat-mintavétel (nem kötelező, de erősen ajánlott a biztonság).
- Virtuálisgép-méretek esetében működik.
- Platformlemezképek működik a Windows és Linux rendszereken.
- (Operációs rendszer Verziófrissítései indíthatók el manuálisan a jól) bármikor is kikapcsolja az automatikus frissítéseket.
- Virtuális gép operációsrendszer-lemezének helyére az új operációsrendszer-lemez legfrissebb rendszerképverzió hoztak létre. Konfigurált bővítmények és adatok egyéni parancsfájlok futnak, közben megőrződnek lemezen tárolt adatokat.


## <a name="preview-notes"></a>Megjegyzések megtekintése 
Az előzetes verzió, ha a következő korlátozások és korlátozások vonatkoznak:

- Az operációs rendszer frissítése csak [négy operációs rendszer termékváltozatok](#supported-os-images). Nincs, garanciát vagy SLA-t. Azt javasoljuk, hogy nem használja az automatikus frissítések a kritikus fontosságú éles előzetes verzió ideje alatt.
- Az Azure disk encryption van **nem** jelenleg a virtual machine scale set operációs rendszer automatikus verziófrissítése támogatott.


## <a name="register-to-use-automatic-os-upgrade"></a>Regisztráljon, és használja az operációs rendszer automatikus verziófrissítése
Az operációs rendszer automatikus frissítési funkció használatához az előzetes verzió-szolgáltató regisztrálása az Azure Powershell vagy az Azure CLI 2.0 használatával.

### <a name="powershell"></a>PowerShell

1. Regisztrálás [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. Jelentés regisztrációs állapot körülbelül 10 percet vesz igénybe *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapot- [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). 

3. Miután regisztrált, ellenőrizze, hogy a *Microsoft.Compute* szolgáltató regisztrálva van. Az alábbi példában az Azure PowerShell-lel [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. Regisztrálás [az a funkció regisztrálása](/cli/azure/feature#az-feature-register):

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. Jelentés regisztrációs állapot körülbelül 10 percet vesz igénybe *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapot- [az funkció show](/cli/azure/feature#az-feature-show). 
 
3. Miután regisztrált, ügyeljen arra, hogy a *Microsoft.Compute* szolgáltató regisztrálva van. Az alábbi példában az Azure parancssori felület (2.0.20-as vagy újabb) a [az provider register](/cli/azure/provider#az-provider-register):

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Service Fabric-fürtök rendelkezik saját alkalmazásállapot fogalmát, de a méretezési csoportok a Service Fabric nélkül használja a terheléselosztó állapotmintája alkalmazás állapotának figyeléséhez. 
>
> ### <a name="azure-powershell"></a>Azure PowerShell
>
> 1. A szolgáltató funkció regisztrálható az állapotadat-mintavételek [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. Ismét szükséges körülbelül 10 percet a regisztrációs állapot jelentés *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapot- [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature)
>
> 3. Miután regisztrált ügyeljen arra, hogy a *Microsoft.Network* szolgáltató regisztrálva van a használatával [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. A szolgáltató funkció regisztrálható az állapotadat-mintavételek [az a funkció regisztrálása](/cli/azure/feature#az-feature-register):
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. Ismét szükséges körülbelül 10 percet a regisztrációs állapot jelentés *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapot- [az funkció show](/cli/azure/feature#az-feature-show). 
>
> 3. Miután regisztrált ügyeljen arra, hogy a *Microsoft.Network* szolgáltató regisztrálva van a használatával [az provider register](/cli/azure/provider#az-provider-register) módon:
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>Portal-felület
Miután a fenti regisztrációs lépéseket követi, nyissa meg [az Azure Portalon](https://aka.ms/managed-compute) operációs rendszer automatikus verziófrissítését a méretezésicsoport-engedélyezéséhez és a frissítési folyamat előrehaladását:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációs rendszer platform lemezképek támogatottak. Saját kezűleg létrehozott egyéni lemezképek jelenleg nem használható. A *verzió* értékre kell állítani a platformlemezkép tulajdonsága *legújabb*.

Jelenleg a következő termékváltozatok támogatottak (több kerül):
    
| Közzétevő               | Ajánlat         |  SKU               | Verzió  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-az-tárolók | legújabb   |



## <a name="application-health-without-service-fabric"></a>A Service Fabric nélkül alkalmazásállapot
> [!NOTE]
> Ez a szakasz csak a méretezési csoportok anélkül, hogy a Service Fabric vonatkozik. A Service Fabric rendelkezik a saját alkalmazásállapot fogalma. Operációs rendszer automatikus frissítéseinek használata a Service Fabric, az új operációsrendszer-képet bevezetési frissítési tartományt frissítési tartományonként a Service Fabric-ban futó szolgáltatások magas rendelkezésre állás fenntartása érdekében. A Service Fabric-fürtök tartóssági jellemzői további információkért tekintse meg [ebben a dokumentációban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Az operációs rendszer a frissítés során megtörténik a méretezési csoportban lévő Virtuálisgép-példányok egyszerre csak egy köteg. A frissítés továbbra is csak ha az ügyfél-alkalmazás állapota kifogástalan, a frissített Virtuálisgép-példányokon. Emiatt az alkalmazás állapotának jelek, a méretezési csoport operációs rendszer frissítési motor kell megadnia. Operációs rendszerre a platform figyelembe veszi a virtuális gép energiaállapotát és bővítmény kiépítési állapota annak megállapításához, hogy egy Virtuálisgép-példány kifogástalan állapotú a frissítés után. Egy Virtuálisgép-példánnyal az operációs rendszer frissítése közben az operációsrendszer-lemez egy Virtuálisgép-példány helyére egy új lemezt, legfrissebb rendszerképverzió alapján. Miután az operációs rendszer frissítése befejeződött, a konfigurált bővítmények ezek a virtuális gépek futnak. Csak akkor, ha a virtuális gép a bővítmények vannak kiépítve, az alkalmazás számít kifogástalan állapotú. 

Ezenkívül a méretezési *kell* alkalmazás állapotadat-mintavételek alkotják azt a platformot a folyamatban lévő, az alkalmazás állapota a megfelelő információkkal kell konfigurálni. Alkalmazás állapotadat-mintavételek olyan egyéni Load Balancer-mintavételek állapotáról legutoljára jelzés használt. Az alkalmazás egy méretezési csoport Virtuálisgép-példány fut jelzi-e megfelelő külső HTTP vagy TCP-kérésekre is válaszol. A Custom betölteni a Terheléselosztói Mintavételezők működéséről további információkért lásd: [megismerése a load balancer vizsgálatok](../load-balancer/load-balancer-custom-probe-overview.md).

Ha a méretezési csoportban több elhelyezési csoport használatára van konfigurálva, mintavételezők használatával egy [Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) kell használni.

### <a name="important-keep-credentials-up-to-date"></a>Fontos: Naprakészen tarthatja hitelesítő adatok
Ha a méretezési külső erőforrások hozzáféréséhez a hitelesítő adatokat használ, győződjön meg arról, hogy a hitelesítő adatai mindig naprakészek, mert szüksége. Például a VM-bővítmény tárfiók SAS-token használatára konfigurálható. Ha a hitelesítő adatokat, többek között a tanúsítványok és a jogkivonatok érvényessége lejárt, a frissítés sikertelen lesz, és sikertelen állapotban marad az első kötegbe tartozó virtuális gépek.

Virtuális gépek helyreállítása, majd újra engedélyeznie az operációs rendszer automatikus verziófrissítése, egy erőforrás hitelesítési hiba esetén a javasolt lépések a következők:

* A token (vagy bármely más hitelesítő adatokat) átad a fájlformátum(ok) kiterjesztését vagy kiterjesztéseit újragenerálása.
* Győződjön meg arról, hogy felvegye a külső entitások a virtuális gép a használt hitelesítő adat naprakész.
* Új-tokenekkel frissítse fájlformátum(ok) kiterjesztését vagy kiterjesztéseit, az a méretezési csoport modelljéből.
* Helyezze üzembe a frissített méretezési csoportot, amely frissíti az összes Virtuálisgép-példány sikertelen azokat is beleértve. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurálható egy egyéni Load Balancer-vizsgálatokra, alkalmazás-Állapotminta a méretezési csoport beállítása
Ön *kell* terheléselosztói mintavételezők explicit módon létre méretezési csoport állapotát. Egy meglévő HTTP-mintavétel vagy a TCP-mintavétel egyazon végpont használható, de az állapotfigyelő mintavételező megkövetelheti egy hagyományos load balancer mintavételi eltérő működést. Például egy hagyományos terheléselosztói mintavételező nem kifogástalan, ha a terhelést a példányon túl magas eredményezhetnek. Ezzel szemben, amelyek nem lehet megfelelő, a példány állapotának meghatározása során egy operációs rendszer automatikus verziófrissítése. Szeretné, hogy kevesebb két percnél ellenőrzési gyakorisága a mintavétel konfigurálása.

A load balancer mintavételi lehet hivatkozni a *networkProfile* a méretezési csoport beállítása és társítható vagy egy belső vagy nyilvános internetkapcsolattal rendelkező terheléselosztó a következő:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Az operációs rendszer lemezkép frissítési szabályzat kényszerítése az előfizetésében
Biztonságos frissítéseket mindenképpen ajánlatos egy frissítési szabályzatban. Ez a szabályzat lehet szükség az alkalmazás állapotadat-mintavételek az előfizetésében. Az alábbi Azure Resource Manager-házirend elutasítja üzemelő példánya, amely automatikus operációsrendszer-lemezkép frissítése a konfigurált beállítások nem rendelkezik:

### <a name="powershell"></a>PowerShell
1. Szerezze be az Azure Resource Manager beépített szabályzatdefiníciót, a [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) módon:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Szabályzat hozzárendelése egy előfizetéshez, [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) módon:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>CLI 2.0
Szabályzat hozzárendelése egy előfizetéshez, a beépített Azure Resource Manager-házirend:

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>Automatikus frissítések konfigurálása
Az automatikus frissítések konfigurálása, ellenőrizze, hogy a *verziófrissítését* tulajdonsága *igaz* a méretezésicsoport-modell definícióját. Ez a tulajdonság az Azure PowerShell vagy az Azure CLI konfigurálhatja.

### <a name="powershell"></a>PowerShell
Az alábbi példában az Azure PowerShell-lel (4.4.1 vagy újabb) nevű méretezési az automatikus frissítések konfigurálása *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
Az alábbi példában az Azure parancssori felület (2.0.20-as vagy újabb) nevű méretezési az automatikus frissítések konfigurálása *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Az operációs rendszer automatikus verziófrissítése állapotának ellenőrzése
Ellenőrizheti a legújabb operációs rendszer frissítése a méretezési csoportban Azure PowerShell-lel, az Azure CLI vagy a REST API-k végrehajtott állapotát.

### <a name="powershell"></a>PowerShell
Az alábbi példa az Azure Powershellt (4.4.1 vagy újabb) ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli"></a>Azure CLI

Az alábbi példában az Azure parancssori felület (2.0.20-as vagy újabb) ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Az alábbi példa a REST API-t használ, ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

A GET hívást hasonló ad vissza tulajdonságait az alábbi példa kimenetében:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Operációs rendszer automatikus verziófrissítése végrehajtása
Bontsa ki az állapot-mintavételei alkalmazás használatát, a méretezési csoport operációs rendszer verziófrissítései hajtsa végre a következő lépéseket:

1. Ha több mint 20 %-a példányok nem kifogástalan állapotú, állítsa le a frissítés; Ellenkező esetben továbbra is.
2. A következő köteg maximális 20 %-a teljes példányszám kellene kötegelt frissítés Virtuálisgép-példányok azonosítása.
3. Frissítse az operációs rendszert, a következő köteg Virtuálisgép-példányok.
4. Ha több mint 20 %-a frissített példányok nem kifogástalan állapotú, állítsa le a frissítés; Ellenkező esetben továbbra is.
5. A méretezési csoportok, amelyek a Service Fabric-fürt részét képezik a frissítés vár, akár 5 percig mintavételek állapotúak lesznek, majd azonnal folytatódik a következő köteg be. A Service Fabric-fürt részét képező méretezési csoportokhoz a méretezési csoport vár mielőtt a következő köteg 30 perc.
6. Ha vannak hátralévő példányok frissítése, goto 1. lépés) a következő köteg; Ellenkező esetben a frissítés akkor fejeződött be.

A méretezési csoport minden egyes köteg a frissítés előtt az operációs rendszer frissítése motor Virtuálisgép-példány általános állapotát ellenőrzi. A batch a frissítés során lehetnek más egyidejű tervezett vagy nem tervezett karbantartás történik, amely hatással lehet a virtuális gépek rendelkezésre állásának az Azure-adatközpontokban. Ezért lehetőség, hogy ideiglenesen több mint 20 %-példányok esetleg nem működik. Ezekben az esetekben az aktuális köteg végén a méretezési csoport frissítési leáll.


## <a name="deploy-with-a-template"></a>Üzembe helyezés egy sablon használatával

A következő sablon segítségével telepítheti egy méretezési csoportot, amely automatikus frissítéseket használja <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatikus működés közbeni frissítése – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>További lépések
Operációs rendszer automatikus frissítéseinek használata méretezési csoportokkal kapcsolatos további példákért lásd a [GitHub-adattárat az előzetes verziójú funkciók](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
