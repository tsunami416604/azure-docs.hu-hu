---
title: "Az Azure virtuális gép automatikus az operációs rendszer frissítései méretezési készletek |} Microsoft Docs"
description: "Ismerje meg, az operációs rendszerben található egy méretezési csoportban lévő Virtuálisgép-példányok automatikus frissítése"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 145f4ec92b142a1585ba17bf6e49c7824cc32529
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure virtuális gépek méretezési automatikus az operációs rendszer frissítései

Operációs rendszer lemezképének automatikus frissítése az előzetes verziójú funkciók a Azure virtuálisgép-méretezési készlet, amely automatikusan frissíti a legújabb operációsrendszer-lemezképek összes virtuális gép.

Operációs rendszer automatikus frissítése a következő jellemzőkkel rendelkezik:

- Beállítása után a legújabb operációsrendszer-lemezképek kép kiadók által automatikusan alkalmazza a méretezési készletben, felhasználói beavatkozás nélkül.
- Frissíti a példányok kötegek működés közbeni módon minden alkalommal, amikor egy új platformlemezképet közzé van téve, amelyet a közzétevő.
- Alkalmazás állapotmintáihoz integrálható (nem kötelező, de erősen ajánlott biztonsági).
- Az összes Virtuálisgép-méretek működik.
- Működik a Windows és Linux platformon képek.
- (Az operációs rendszer frissítései kezdeményezhetők, manuálisan is) bármikor is kikapcsolja az automatikus frissítések.
- Az operációsrendszer-lemezképet a virtuális gépek helyére az új operációsrendszer-lemezképet lemezkép legújabb verziójával. Konfigurált bővítmények és egyéni parancsfájlok futnak, megőrzött adatok megmaradnak a lemezek közben.


## <a name="preview-notes"></a>Megjegyzések megtekintése 
A kép, ha a következő korlátozások és korlátozások vonatkoznak:

- Az operációs rendszer automatikus frissítése csak támogatási [négy operációs rendszer termékváltozatok](#supported-os-images). Nincs SLA-t vagy garanciát. Azt javasoljuk, hogy nem használja az automatikus frissítések a termelési kritikus fontosságú munkaterhelésekhez előzetes.
- A Service Fabric-fürtök méretezési csoportok támogatása hamarosan elérhető.
- (Jelenleg az előzetes verzió) Azure lemez titkosítása **nem** virtuális gép méretezési készlet automatikus operációsrendszer-verziófrissítő támogatja.
- A portál felhasználói élményt nyújtja. hamarosan már az.


## <a name="register-to-use-automatic-os-upgrade"></a>Regisztrálás az automatikus frissítése az operációs rendszer használata
Az operációs rendszer automatikus frissítési funkció használatához a minta-szolgáltató regisztrálása [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) az alábbiak szerint:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Regisztrációs állapotát körülbelül 10 percet vesz igénybe a jelentés *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapotát az [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Regisztrálás után győződjön meg arról, hogy a *Microsoft.Compute* szolgáltató regisztrálva van [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) az alábbiak szerint:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

Azt javasoljuk, hogy az alkalmazások állapotteljesítmény használja-e. A szolgáltató szolgáltatás állapotteljesítmény regisztrálásához használhatja [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) az alábbiak szerint:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

Ebben az esetben szükséges körülbelül 10 percig regisztrációs állapotát jelentés *regisztrált*. Ellenőrizheti, hogy az aktuális regisztrációs állapotát az [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Egyszer regisztrálva. Győződjön meg arról, hogy a *Microsoft.Network* szolgáltató regisztrálva van [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) az alábbiak szerint:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációs rendszer platform lemezképek támogatottak. Jelenleg nem használható, hogy rendelkezik létrehozott saját egyéni lemezképek. A *verzió* értékre kell állítani a platformlemezképet tulajdonságának *legújabb*.

A következő termékváltozatok jelenleg támogatott (több megkapja):
    
| Közzétevő               | Ajánlat         |  SKU               | Verzió  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-ES LTS VERZIÓ          | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | legújabb   |
| MicrosoftWindowsServer  | WindowsServer | 2016-adatközpont-Smalldisk | legújabb   |



## <a name="application-health"></a>Alkalmazás állapotának
Az operációs rendszer a frissítés során megtörténik a Virtuálisgép-méretezési csoportban lévő példányok egyszerre csak egy köteg. A frissítés továbbra is csak ha az ügyfél alkalmazás állapota kifogástalan, a frissített Virtuálisgép-példányokon. Azt javasoljuk, hogy az alkalmazás maga biztosítja a méretezési készlet operációs rendszer frissítési motor állapotfigyelő jelek. Alapértelmezés szerint az operációs rendszer frissítéskor a platform úgy ítéli meg VM energiaállapot és üzembe helyezési állapota annak megállapításához, hogy a Virtuálisgép-példány megfelelő frissítés után bővítmény. Az operációs rendszer a frissítés során egy Virtuálisgép-példány az operációsrendszer-lemezképet, a Virtuálisgép-példány helyére egy új lemezt, a lemezkép legújabb verziója alapján. Miután az operációs rendszer frissítése befejeződött, a konfigurált extensions virtuális gépeken futnak. Csak akkor, ha a virtuális gép az összes bővítmény sikeresen telepített, akkor az alkalmazás tekinthető kifogástalan. 

A méretezési igény szerint konfigurálható alkalmazás állapot-mintavételi csomagjai arra, hogy ellássa a platform pontos információkat az alkalmazás folyamatban lévő állapota. Egyéni betöltési terheléselosztó-vizsgálat állapotát jel használt alkalmazás állapot-mintavételi csomagjai. Méretezési készlet Virtuálisgép-példány fut az alkalmazás jelzi, hogy állapota kifogástalan külső HTTP vagy TCP-kérésekre is válaszol. Az egyéni betöltése Terheléselosztói mintavétel működéséről további információkért lásd: [megértése load balancer mintavételt](../load-balancer/load-balancer-custom-probe-overview.md). Az alkalmazás állapotának mintavételi esetében nincs szükség az operációs rendszer automatikus frissítéseket, de ajánlott.

A méretezési több elhelyezési csoportok használatára van beállítva, ha vizsgálat használatával egy [szabványos terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) kell használnia.

### <a name="important-keep-credentials-up-to-date"></a>Fontos: Hitelesítő adatok naprakészen tartása
Ha a méretezési bármely hitelesítő adatokat használja a külső erőforrások eléréséhez, például ha egy Virtuálisgép-bővítmény van konfigurálva egy SAS-jogkivonatot használja a tárfiók, akkor győződjön meg arról, hogy a hitelesítő adatok vannak mindig naprakészek legyenek. Ha a lejárt hitelesítő adatok, beleértve a tanúsítványok és a tokeneket a frissítés sikertelen lesz, és hibás állapotban marad a virtuális gépek első kötegben.

A virtuális gépek helyreállítása, és engedélyezze újra automatikus operációsrendszer-verziófrissítő erőforrás hitelesítési hiba esetén javasolt lépéseket a következők:

* A token (vagy bármely más hitelesítő adatok) lett átadva a fájlformátum(ok) kiterjesztését vagy kiterjesztéseit újragenerálása.
* Győződjön meg arról, hogy felvegye a külső entitás a virtuális Gépen belül használt hitelesítő adat naprakészek legyenek.
* A méretezési készlet modell fájlformátum(ok) kiterjesztését vagy kiterjesztéseit frissítése szükséges új jogkivonatokhoz.
* Telepítse a frissített méretezési készlet, amely frissíti az összes Virtuálisgép-példányok, többek között a sikertelen néhányat a meglévők közül. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurálása egy egyéni Load Balancer mintavételi alkalmazás állapotának mintavételi méretű beállítása
Ajánlott eljárásként létrehozni a terheléselosztói mintavétel explicit módon méretezési állapotát. Egy meglévő HTTP-vizsgálatot, vagy a TCP-Hálózatfigyelővel azonos végpontja is használható, de előfordulhat, hogy egy állapotmintáihoz hagyományos terheléselosztó-vizsgálatok különböző viselkedés. Például egy hagyományos terheléselosztói mintavétel térhetnek vissza nem kifogástalan, ha a példány terhelése túl nagy, mivel, amely nem lehet megfelelő, a példány állapotának meghatározása az operációs rendszer automatikus frissítés során. Konfigurálja a mintavétel a vizsgálathoz használt magas sebessége kisebb, mint két perc.

A terheléselosztó mintavételi lehet hivatkozni a *networkProfile* a skála beállítása és társítható vagy egy belső vagy nyilvános-terheléselosztót az alábbiak szerint:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Az operációs rendszer lemezképének házirend kényszerítése az előfizetésből
A biztonságos frissítésekre lehetőleg egy frissítési házirendek kikényszerítéséhez. Ez a házirend megkövetelheti az alkalmazás állapot-mintavételi csomagjai az előfizetésből. A következő Azure Resource Manager-házirend elutasítja automatikus operációsrendszer-lemezképek frissítéséhez a konfigurált beállítások nem rendelkező központi telepítések:

1. A beépített Azure Resource Manager házirend-definíció az beszerzése [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) az alábbiak szerint:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Házirend hozzárendelése egy előfizetés [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) az alábbiak szerint:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Automatikus frissítések konfigurálása
Automatikus frissítések konfigurálása, győződjön meg arról, hogy a *automaticOSUpgrade* tulajdonsága *igaz* a méretezési készletben model definition. Ez a tulajdonság az Azure PowerShell vagy az Azure CLI 2.0 konfigurálható.

Az alábbi példában szereplő Azure PowerShell (4.4.1 vagy újabb) a méretezési készletben elnevezett az automatikus frissítések konfigurálása *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


Az alábbi példában az Azure parancssori felület (2.0.20 vagy újabb) a méretezési készletben elnevezett az automatikus frissítések konfigurálása *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Az automatikus operációsrendszer-verziófrissítő állapotának ellenőrzése
A skála beállítása az Azure PowerShell, Azure CLI 2.0 vagy a REST API-k végzett legutóbbi operációsrendszer-verziófrissítő állapotának ellenőrzése

### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példában szereplő Azure PowerShell (4.4.1 vagy újabb) a állapotának beállítása a nevesített skála *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az alábbi példában az Azure parancssori felület (2.0.20 vagy újabb) a állapotának beállítása a nevesített skála *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Az alábbi példában a REST API-t a állapotának beállítása a nevesített skála *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

A GET hívást hasonló ad vissza tulajdonságait a következő egy példa a kimenetre:

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


## <a name="automatic-os-upgrade-execution"></a>Automatikus operációsrendszer-frissítés végrehajtása
Bontsa ki az alkalmazás állapotának Segéd-vizsgálatok használatát, méretezési készlet operációs rendszer frissítései hajtsa végre a következő lépéseket:

1. Ha több mint 20 %-példányok nem kifogástalan állapotú, állítsa le a frissítést; Ellenkező esetben folytassa a műveletet.
2. Virtuálisgép-példányok frissítéséhez egy kötegben, amelyek legfeljebb 20 %-a teljes példányok száma a következő csoportjának azonosítása.
3. Frissítse az operációs rendszer a következő köteg Virtuálisgép-példánya.
4. Ha több mint 20 %-a frissített példányok nem kifogástalan állapotú, állítsa le a frissítést; Ellenkező esetben folytassa a műveletet.
5. Ha az ügyfél állapot-mintavételi csomagjai alkalmazást konfigurált, a frissítés vár, legfeljebb 5 percenként mintavételt lesz kifogástalan, majd azonnal továbbra is fennáll, a következő kötegelt; Ellenkező esetben azt a következő mérési adatköteget lépés előtt 30 perc elteltével.
6. Ha vannak, hogy a frissítés hátralévő, goto 1. lépés) a következő köteg; Ellenkező esetben a frissítés befejeződött.

A méretezési minden kötegelt frissítése előtt az operációs rendszer frissítése motor Virtuálisgép-példány általános állapotát ellenőrzi. A kötegelt frissítés során lehet más egyidejű tervezett vagy nem tervezett karbantartás történik az Azure Adatközpontjaiban, amely hatással lehet a virtuális gépek rendelkezésre állását. Ezért akkor lehetséges, hogy átmenetileg több mint 20 %-példányok le lehet-e. Ilyen esetben az aktuális köteg végén a méretezési frissítési leáll.


## <a name="deploy-with-a-template"></a>A sablon telepítése

Az alábbi sablon használatával egy méretezési csoport, amely használja az automatikus frissítések telepítése <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatikus működés közbeni frissítése – Ubuntu 16.04-es lts verzió</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>További lépések
Az operációs rendszer automatikus frissítések használata a méretezési készlet további példákért lásd a [előzetes verziójú funkciók a GitHub-tárház](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
