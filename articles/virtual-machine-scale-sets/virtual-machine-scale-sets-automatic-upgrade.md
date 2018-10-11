---
title: Operációs rendszer lemezkép automatikus verziófrissítését, az Azure-beli virtuálisgép-méretezési csoportokban |} A Microsoft Docs
description: Ismerje meg az operációsrendszer-képet egy méretezési csoportban lévő Virtuálisgép-példányokon automatikus frissítése
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069454"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Az Azure virtuálisgép-méretezési csoport automatikus operációs rendszer lemezkép frissítéseinek beállítása

Automatikus operációsrendszer-lemezkép frissítés az Azure-beli virtuálisgép-méretezési csoportok egy szolgáltatása, amely automatikusan frissíti az összes virtuális gépek a legújabb operációsrendszer-lemezkép.

Operációs rendszer automatikus verziófrissítése a következő jellemzőkkel rendelkezik:

- A beállítása után rendszerképet kiadók által közzétett legújabb operációsrendszer-lemezkép a rendszer automatikusan alkalmazza a méretezési csoport felhasználói beavatkozás nélkül.
- Frissíti a példányok kötegek működés közbeni módon minden alkalommal, amikor a közzétevő által közzétett új platform-lemezképet.
- Integrálható az alkalmazás állapotadat-mintavétel.
- A Virtuálisgép-méretek és a Windows- és Linux platform rendszerképeit működik.
- (Operációs rendszer Verziófrissítései is kezdeményezhető, manuálisan is) bármikor is kikapcsolja az automatikus frissítéseket.
- Virtuális gép operációsrendszer-lemezének helyére az új operációsrendszer-lemez legfrissebb rendszerképverzió hoztak létre. Konfigurált bővítmények és adatok egyéni parancsfájlok futnak, közben megőrződnek lemezen tárolt adatokat.
- Az Azure disk encryption (az előzetes verzió) jelenleg nem támogatott.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hogyan történik az operációs rendszer frissítési munka kép?

Frissítés működik, és cserélje le a virtuális gépek az operációsrendszer-lemez egy új létrehozott rendszerkép legújabb verzióját használja. Bármely bővítmények konfigurált, és egyéni parancsfájlok, közben megőrződnek lemezen tárolt adatokat. Az alkalmazás állásidő minimalizálása érdekében a verziófrissítések kerül sor kötegek gépek, a méretezési csoport frissítése a tetszőleges időpontban nem több mint 20 %-át. Lehetősége is van egy Azure Load Balancer állapotadat-mintavétel integrálásához. Erősen ajánlott egy alkalmazás szívverés építhet be, és a frissítési folyamat során az egyes kötegek frissítés sikerült érvényesíteni. A végrehajtási lépések a következők: 

1. A verziófrissítés megkezdése előtt az orchestrator biztosítja, hogy a példányok nem több mint 20 %-át sérült állapotban. 
2. A kötegelt frissítés egy kötegben legfeljebb 20 %-a teljes példányszám kellene Virtuálisgép-példányok azonosítása.
3. A Virtuálisgép-példányok a Batch operációsrendszer-lemezkép frissítése.
4. Ha az ügyfél állapotadat-mintavételek alkalmazás konfigurálva van, a frissítés vár, legfeljebb 5 percig mintavételek lesz kifogástalan, mielőtt a következő köteg frissítése. 
5. Ha vannak hátralévő példányok frissítése, goto 1. lépés) a következő köteg; Ellenkező esetben a frissítés akkor fejeződött be.

A méretezési csoport minden egyes köteg a frissítés előtt Virtuálisgép-példány általános állapotát az operációs rendszer frissítési orchestrator ellenőrzése. A batch a frissítés során is előfordulhatnak más egyidejű tervezett vagy nem tervezett karbantartás történik, amely hatással lehet a virtuális gépek rendelkezésre állásának az Azure-adatközpontokban. Ezért lehetőség, hogy ideiglenesen több mint 20 %-példányok le lehet-e. Ezekben az esetekben az aktuális köteg végén a méretezési csoport frissítési leáll.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációs rendszer platform lemezképek támogatottak. Jelenleg nem használható, hogy rendelkezik létrehozott saját egyéni rendszerképeit. 

Jelenleg a következő termékváltozatok támogatottak (több hozzáadódik a jövőben):
    
| Közzétevő               | Operációs ajánlat      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-az-tárolók |

* Ezek a lemezképek támogatása jelenleg bevezetéséről, és hamarosan elérhető összes Azure-régióban. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Automatikus operációsrendszer-lemezkép frissítés követelmények

- A *verzió* értékre kell állítani a platformlemezkép tulajdonsága *legújabb*.
- Alkalmazás állapotadat-mintavételek használata nem Service Fabric méretezési csoportokhoz.
- Ellenőrizze, hogy az erőforrások a méretezési csoport modelljéből hivatkozó érhető el, és mindig naprakész marad. 
  A VM-bővítmény tulajdonságai, storage-fiókban lévő hasznos adattartalom rendszerindítása Exa.SAS URI-t a modellben található titkos kódokhoz való hivatkozhat. 

## <a name="configure-automatic-os-image-upgrade"></a>Automatikus operációsrendszer-lemezkép frissítés konfigurálása
Automatikus operációsrendszer-lemezkép frissítés konfigurálásához ellenőrizze, hogy a *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* tulajdonsága *igaz* a méretezésicsoport-modell definícióját. 

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

Az alábbi példában az Azure parancssori felület (2.0.47 vagy újabb) nevű méretezési az automatikus frissítések konfigurálása *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Ez a tulajdonság az Azure Powershellen keresztül konfigurálásának a támogatását a hamarosan fog történni.

## <a name="using-application-health-probes"></a>Az alkalmazás Health mintavételek 

Az operációs rendszer a frissítés során megtörténik a méretezési csoportban lévő Virtuálisgép-példányok egyszerre csak egy köteg. A frissítés továbbra is csak ha az ügyfél-alkalmazás állapota kifogástalan, a frissített Virtuálisgép-példányokon. Azt javasoljuk, hogy az alkalmazás maga biztosítja a méretezési csoport operációs rendszer frissítési motor egészségügyi jelek. Alapértelmezés szerint az operációs rendszer frissítése során a platform figyelembe veszi virtuális gép energiaállapotát és bővítmény kiépítési állapota annak megállapításához, hogy egy Virtuálisgép-példány kifogástalan állapotú a frissítés után. Egy Virtuálisgép-példánnyal az operációs rendszer frissítése közben az operációsrendszer-lemez egy Virtuálisgép-példány helyére egy új lemezt, legfrissebb rendszerképverzió alapján. Miután az operációs rendszer frissítése befejeződött, a konfigurált bővítmények ezek a virtuális gépek futnak. Csak akkor, ha a virtuális gép a bővítmények vannak kiépítve, az alkalmazás számít kifogástalan állapotú. 

Egy méretezési csoportban igény szerint konfigurálható úgy, hogy az alkalmazás állapotadat-mintavételek alkotják azt a platformot a folyamatban lévő, az alkalmazás állapota a pontos információkkal. Alkalmazás állapotadat-mintavételek olyan egyéni Load Balancer-mintavételek állapotáról legutoljára jelzés használt. Az alkalmazás egy méretezési csoport Virtuálisgép-példány fut jelzi-e megfelelő külső HTTP vagy TCP-kérésekre is válaszol. A Custom betölteni a Terheléselosztói Mintavételezők működéséről további információkért lásd: [megismerése a load balancer vizsgálatok](../load-balancer/load-balancer-custom-probe-overview.md). Egy alkalmazás-Állapotminta nem szükséges operációs rendszer automatikus verziófrissítése, de erősen ajánlott.

Ha a méretezési csoportban több elhelyezési csoport használatára van konfigurálva, mintavételezők használatával egy [Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) kell használni.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurálható egy egyéni Load Balancer-vizsgálatokra, alkalmazás-Állapotminta a méretezési csoport beállítása
Ajánlott eljárásként létrehozni a terheléselosztói mintavételezők explicit módon méretezési csoport állapotát. Egy meglévő HTTP-mintavétel vagy a TCP-mintavétel egyazon végpont is használható, de az állapotfigyelő mintavételező szükség lehet a hagyományos terheléselosztó mintavétel eltérő viselkedéssel. Például egy hagyományos terheléselosztói mintavételező adhatnak vissza nem kifogástalan, ha a terhelést a példányon túl magas, mivel nem tűnik megfelelő példány állapotának meghatározása során egy operációs rendszer automatikus verziófrissítése. Szeretné, hogy kevesebb két percnél ellenőrzési gyakorisága a mintavétel konfigurálása.

A load balancer mintavételi lehet hivatkozni a *networkProfile* a méretezési csoport beállítása és társítható vagy egy belső vagy nyilvános internetkapcsolattal rendelkező terheléselosztó a következő:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Operációs rendszer automatikus frissítéseinek használata a Service Fabric, az új operációsrendszer-képet bevezetési frissítési tartományt frissítési tartományonként a Service Fabric-ban futó szolgáltatások magas rendelkezésre állás fenntartása érdekében. A Service Fabric-fürtök tartóssági jellemzői további információkért tekintse meg [ebben a dokumentációban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Tartsa naprakészen a hitelesítő adatok
Ha a méretezési a hitelesítő adatokat használ a külső erőforrások eléréséhez, például ha egy Virtuálisgép-bővítményt van konfigurálva egy SAS-jogkivonatot használó tárfiók, szüksége lesz, hogy a hitelesítő adatok naprakészek maradnak. Ha a hitelesítő adatokat, többek között a tanúsítványok és a jogkivonatok érvényessége lejárt, a frissítés sikertelen lesz, és sikertelen állapotban marad az első kötegbe tartozó virtuális gépek.

Virtuális gépek helyreállítása, majd újra engedélyeznie az operációs rendszer automatikus verziófrissítése, egy erőforrás hitelesítési hiba esetén a javasolt lépések a következők:

* A token (vagy bármely más hitelesítő adatokat) átad a fájlformátum(ok) kiterjesztését vagy kiterjesztéseit újragenerálása.
* Győződjön meg arról, hogy naprakész állapotban-e a virtuális gép külső entitások kommunikáljon a használt hitelesítő adat.
* Új-tokenekkel frissítse fájlformátum(ok) kiterjesztését vagy kiterjesztéseit, az a méretezési csoport modelljéből.
* Helyezze üzembe a frissített méretezési csoportot, amely frissíti az összes Virtuálisgép-példány sikertelen azokat is beleértve. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Az operációs rendszer lemezkép automatikus frissítéseinek előzményeit beolvasása 
Ellenőrizheti a legújabb operációs rendszer frissítése a méretezési csoportban Azure PowerShell-lel, az Azure CLI 2.0-s vagy a REST API-k végrehajtott előzményeit. Az utolsó öt operációs rendszer frissítési kísérletek az elmúlt két hónapon belül előzmények kérheti le.

### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példa az Azure Powershellt használja a állapotának a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az alábbi példában az Azure parancssori felület (2.0.47 vagy újabb) ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Az alábbi példa a REST API-t használ, ellenőrizheti az állapotot, a méretezési csoport nevű *myVMSS* az erőforráscsoport neve *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Tekintse meg az API-t itt dokumentációját: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Szerezze be a legújabb verziót a platform operációsrendszer-lemezkép hogyan? 

A lemezkép-verziók esetében az operációs rendszer frissítése támogatott SKU-k használatával megtekintheti az alábbi példákat: 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Üzembe helyezés egy sablon használatával

A következő sablon segítségével telepítheti egy méretezési csoportot, amely automatikus frissítéseket használja <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatikus működés közbeni frissítése – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>További lépések
Operációs rendszer automatikus frissítéseinek használata méretezési csoportokkal kapcsolatos további példákért lásd a [GitHub-adattárat az előzetes verziójú funkciók](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
