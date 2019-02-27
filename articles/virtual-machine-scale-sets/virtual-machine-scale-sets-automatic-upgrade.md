---
title: Operációs rendszer lemezkép automatikus verziófrissítését, az Azure-beli virtuálisgép-méretezési csoportokban |} A Microsoft Docs
description: Ismerje meg az operációsrendszer-képet egy méretezési csoportban lévő Virtuálisgép-példányokon automatikus frissítése
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 55eb81fd969e18ea25dd4194a532747ef5b7d4ca
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871740"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Az Azure virtuálisgép-méretezési csoport automatikus operációs rendszer lemezkép frissítéseinek beállítása

Biztonságos, és automatikusan frissítse az operációsrendszer-lemezt a méretezési csoportban szereplő összes példány automatikus operációsrendszer-lemezkép engedélyezése a méretezési készlet segít a könnyű felügyeleti frissíti.

Operációs rendszer automatikus verziófrissítése a következő jellemzőkkel rendelkezik:

- A beállítása után rendszerképet kiadók által közzétett legújabb operációsrendszer-lemezkép a rendszer automatikusan alkalmazza a méretezési csoport felhasználói beavatkozás nélkül.
- Frissíti a példányok kötegek működés közbeni módon minden alkalommal, amikor a közzétevő által közzétett új platform-lemezképet.
- Alkalmazás állapotadat-mintavételek integrálható és [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md).
- A Virtuálisgép-méretek és a Windows- és Linux platform rendszerképeit működik.
- (Operációs rendszer Verziófrissítései is kezdeményezhető, manuálisan is) bármikor is kikapcsolja az automatikus frissítéseket.
- Virtuális gép operációsrendszer-lemezének helyére az új operációsrendszer-lemez legfrissebb rendszerképverzió hoztak létre. Konfigurált bővítmények és adatok egyéni parancsfájlok futnak, közben megőrződnek lemezen tárolt adatokat.
- [Végrehajtási](virtual-machine-scale-sets-extension-sequencing.md) használata támogatott.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hogyan történik az operációs rendszer frissítési munka kép?

Frissítés működik, és cserélje le a legfrissebb rendszerképverzió használatával létrehozott egy új lemezt a virtuális gépek az operációsrendszer-lemez. Minden konfigurált bővítmények és egyéni parancsfájlok futtathatók az operációsrendszer-lemez közben megőrződnek lemezen tárolt adatokat. Az alkalmazás állásidő minimalizálása érdekében a verziófrissítések kerül sor kötegek, a méretezési csoport frissítése a tetszőleges időpontban nem több mint 20 %-át. Integrálhatja az Azure Load Balancer állapotadat-mintavétel vagy [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md). Egy alkalmazás szívverés beépítése ajánlott azt, és a frissítési folyamat során az egyes kötegek frissítés sikerült érvényesíteni.

A frissítési folyamat a következőképpen történik:
1. A verziófrissítés megkezdése előtt az orchestrator biztosítja, hogy nem több mint 20 %-a teljes méretezési csoportban lévő példányok (bármilyen okból) sérült állapotban.
2. A frissítés orchestrator azonosítja a kötegelt frissítés bármely egy kötegben legfeljebb 20 %-a teljes példányok száma a Virtuálisgép-példányok.
3. Az operációsrendszer-lemez a kijelölt köteg Virtuálisgép-példányok helyére egy új operációsrendszer-lemez, a legújabb lemezképből létrehozott, és az összes megadott bővítmények és a méretezési csoport modelljéből beállításokat alkalmazza a frissített példányhoz.
4. A méretezési csoportok a konfigurált alkalmazáshoz a állapotadat-mintavételek vagy alkalmazásállapot-bővítmény a frissítés vár, a példány lesz kifogástalan, mielőtt a következő köteg frissítése akár 5 percet.
5. A frissítés orchestrator is nyomon követi a százalékos aránya, amelyek nem megfelelő állapotú post válik a frissítés. A frissítés le fog állni, ha több mint 20 %-a frissített példányok nem megfelelő állapotú legyen a frissítési folyamat során.
6. A fenti folyamat továbbra is fennáll, addig, amíg a méretezési csoportban lévő összes példány frissítettek.

A méretezési csoport operációs rendszer frissítési orchestrator keres a méretezési csoport általános állapotát minden batch frissítése előtt. A batch a frissítés során lehetnek más egyidejű tervezett vagy nem tervezett karbantartási tevékenységek, amelyek hatással lehetnek a méretezési csoport példányaihoz állapotát. Ebben az esetben ha több mint 20 %-a méretezési példányok nem megfelelő állapotú, majd a méretezési csoport frissítési leállítja az aktuális köteg végén.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációs rendszer platform lemezképek támogatottak. Egyéni lemezképek jelenleg nem támogatottak.

Jelenleg a következő termékváltozatok támogatottak (és további rendszeres időközönként hozzáadja):

| Közzétevő               | Operációs ajánlat      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | A 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Automatikus operációsrendszer-lemezkép frissítés követelmények

- A *verzió* értékre kell állítani a platformlemezkép tulajdonsága *legújabb*.
- Használja az alkalmazás állapotadat-mintavételek vagy [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md) nem Service Fabric-méretezési csoportok.
- Győződjön meg arról, hogy a méretezési csoport modelljéből megadott külső erőforrások rendelkezésre álló és frissített. Példák közé tartozik a SAS URI-t a virtuális gép bővítmény tulajdonságai, a storage-fiók hasznos adattartalom rendszerindításra, a modell és egyéb titkok referenciáját.

## <a name="configure-automatic-os-image-upgrade"></a>Automatikus operációsrendszer-lemezkép frissítés konfigurálása
Automatikus operációsrendszer-lemezkép frissítés konfigurálásához ellenőrizze, hogy a *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* tulajdonsága *igaz* a méretezésicsoport-modell definícióját.

### <a name="rest-api"></a>REST API
A következő példa ismerteti, hogyan lehet beállítani egy méretezési csoport modelljéből operációs rendszer automatikus frissítései:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmaggal ellenőrizheti az operációs rendszer frissítési előzmények a méretezési csoporthoz. Az alábbi példa konfigurálja a elnevezésű méretezési csoportot az automatikus frissítéseket *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Használat [az vmss update](/cli/azure/vmss#az-vmss-update) ellenőrizze az operációs rendszer frissítési előzmények a méretezési csoporthoz. Az Azure CLI használatával 2.0.47 vagy újabb. Az alábbi példa konfigurálja a elnevezésű méretezési csoportot az automatikus frissítéseket *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Az alkalmazás Health mintavételek

Az operációs rendszer a frissítés során megtörténik a méretezési csoportban lévő Virtuálisgép-példányok egyszerre csak egy köteg. A frissítés továbbra is csak ha az ügyfél-alkalmazás állapota kifogástalan, a frissített Virtuálisgép-példányokon. Azt javasoljuk, hogy az alkalmazás maga biztosítja a méretezési csoport operációs rendszer frissítési motor egészségügyi jelek. Alapértelmezés szerint az operációs rendszer frissítése során a platform figyelembe veszi virtuális gép energiaállapotát és bővítmény kiépítési állapota annak megállapításához, hogy egy Virtuálisgép-példány kifogástalan állapotú a frissítés után. Egy Virtuálisgép-példánnyal az operációs rendszer frissítése közben az operációsrendszer-lemez egy Virtuálisgép-példány helyére egy új lemezt, legfrissebb rendszerképverzió alapján. Miután az operációs rendszer frissítése befejeződött, a konfigurált bővítmények ezek a virtuális gépek futnak. Az alkalmazás megfelelő számít, csak akkor, ha a példányon a bővítmények vannak kiépítve.

Egy méretezési csoportban igény szerint konfigurálható úgy, hogy az alkalmazás állapotadat-mintavételek alkotják azt a platformot a folyamatban lévő, az alkalmazás állapota a pontos információkkal. Alkalmazás állapotadat-mintavételek olyan egyéni Load Balancer-mintavételek állapotáról legutoljára jelzés használt. Az alkalmazás egy méretezési csoport Virtuálisgép-példány fut jelzi-e megfelelő külső HTTP vagy TCP-kérésekre is válaszol. A Custom betölteni a Terheléselosztói Mintavételezők működéséről további információkért lásd: [megismerése a load balancer vizsgálatok](../load-balancer/load-balancer-custom-probe-overview.md). Egy alkalmazás-Állapotminta nem szükséges a Service Fabric méretezési csoportokhoz, de ajánlott. Nem – a Service Fabric-méretezési csoportok megkövetelése vagy terheléselosztó alkalmazás állapotadat-mintavételek vagy [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md).

Ha a méretezési csoportban több elhelyezési csoport használatára van konfigurálva, mintavételezők használatával egy [Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) kell használni.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurálható egy egyéni Load Balancer-vizsgálatokra, alkalmazás-Állapotminta a méretezési csoport beállítása
Ajánlott eljárásként létrehozni a terheléselosztói mintavételezők explicit módon méretezési csoport állapotát. Egy meglévő HTTP-mintavétel vagy a TCP-mintavétel egyazon végpont használható, de az állapotfigyelő mintavételező szükségük a hagyományos terheléselosztó mintavétel eltérő viselkedést. Hagyományos terheléselosztói mintavételezők például sikerült vissza nem megfelelő állapotú. Ha a terhelés a példányon túl nagy, de, amely nem lenne megfelelő a példány állapotának meghatározása során egy operációs rendszer automatikus verziófrissítése. Szeretné, hogy kevesebb két percnél ellenőrzési gyakorisága a mintavétel konfigurálása.

A load balancer mintavételi lehet hivatkozni a *networkProfile* a méretezési csoport beállítása és társítható vagy egy belső vagy nyilvános internetkapcsolattal rendelkező terheléselosztó a következő:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Operációs rendszer automatikus frissítéseinek használata a Service Fabric, az új operációsrendszer-képet bevezetési frissítési tartományt frissítési tartományonként a Service Fabric-ban futó szolgáltatások magas rendelkezésre állás fenntartása érdekében. Hogy az operációs rendszer automatikus verziófrissítése, a Service Fabric a fürthöz konfigurálva a Silver szintű tartóssági szint vagy újabb kell lennie. A Service Fabric-fürtök tartóssági jellemzői további információkért tekintse meg [ebben a dokumentációban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Tartsa naprakészen a hitelesítő adatok
Ha a méretezési a hitelesítő adatokat használ a külső erőforrások eléréséhez, például ha egy Virtuálisgép-bővítményt van konfigurálva egy SAS-jogkivonatot használó tárfiók, győződjön meg arról, hogy a hitelesítő adatai frissültek. A lejárt a hitelesítő adatokat, többek között a tanúsítványok és a jogkivonatok, ha a frissítés sikertelen lesz, és az első kötegbe tartozó virtuális gépeket a hibás állapotban marad.

Virtuális gépek helyreállítása, majd újra engedélyeznie az operációs rendszer automatikus verziófrissítése, egy erőforrás hitelesítési hiba esetén a javasolt lépések a következők:

* A token (vagy bármely más hitelesítő adatokat) átad a fájlformátum(ok) kiterjesztését vagy kiterjesztéseit újragenerálása.
* Győződjön meg arról, hogy naprakész állapotban-e a virtuális gép külső entitások kommunikáljon a használt hitelesítő adat.
* Új-tokenekkel frissítse fájlformátum(ok) kiterjesztését vagy kiterjesztéseit, az a méretezési csoport modelljéből.
* Helyezze üzembe a frissített méretezési csoportot, amely frissíti az összes Virtuálisgép-példány sikertelen azokat is beleértve.

## <a name="using-application-health-extension"></a>Alkalmazásállapot-bővítmény használata
Az alkalmazás állapotának bővítmény virtuálisgép-méretezési készlet példány és a méretezési csoport példány belül a virtuális gép állapota a jelentések belül van telepítve. Beállíthatja, hogy a bővítmény alkalmazása a végpont mintavételi és azon a példányon az alkalmazás állapotának frissítése. A példány állapota be van jelölve, az Azure-példány jogosult a frissítési műveletek meghatározásához.

A bővítmény jelentések összesíti a virtuális Gépen belül, mint a bővítmény használható helyzetekben, ahol a külső alkalmazás állapotadat-mintavételek például mintavételek (, amelyek használják az egyéni Azure Load Balancer [mintavételek](../load-balancer/load-balancer-custom-probe-overview.md)) nem használható.

Többféleképpen is üzembe helyezése a bővítményt a méretezési csoportban szereplő példák leírt módon beállítja az Alkalmazásállapot [Ez a cikk](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Az operációs rendszer lemezkép automatikus frissítéseinek előzményeit beolvasása
Ellenőrizheti a legújabb operációs rendszer frissítése a méretezési csoportban Azure PowerShell-lel, az Azure CLI 2.0-s vagy a REST API-k végrehajtott előzményeit. Az utolsó öt operációs rendszer frissítési kísérletek az elmúlt két hónapon belül előzmények kérheti le.

### <a name="rest-api"></a>REST API
Az alábbi példában [REST API-val](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

A GET hívást hasonló ad vissza tulajdonságait az alábbi példa kimenetében:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmaggal ellenőrizheti az operációs rendszer frissítési előzmények a méretezési csoporthoz. Az alábbi példa részletezi, hogyan tekintse át az operációs rendszer frissítésének állapotában találhat egy méretezési csoportot elnevezett *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Használat [az vmss get-operációsrendszer-frissítés-előzmények](/cli/azure/vmss#az-vmss-get-os-upgrade-history) ellenőrizze az operációs rendszer frissítési előzmények a méretezési csoporthoz. Az Azure CLI használatával 2.0.47 vagy újabb. Az alábbi példa részletezi, hogyan tekintse át az operációs rendszer frissítésének állapotában találhat egy méretezési csoportot elnevezett *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Szerezze be a legújabb verziót a platform operációsrendszer-lemezkép hogyan?

A lemezkép-verziók esetében az operációs rendszer frissítése támogatott SKU-k használatával megtekintheti az alábbi példákat:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Üzembe helyezés egy sablon használatával

Sablonok használatával is üzembe helyez egy méretezési csoportot az operációs rendszer automatikus verziófrissítése támogatott rendszerképek például [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>További lépések
Operációs rendszer automatikus frissítéseinek használata méretezési csoportokkal kapcsolatos további példákért tekintse át a [GitHub-adattárat](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
