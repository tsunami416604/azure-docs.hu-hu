---
title: Azure dedikált gazdagépek üzembe helyezése a parancssori felület használatával
description: Virtuális gépek üzembe helyezése dedikált gazdagépekre az Azure CLI használatával.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ab9d7128748e99b75b7e1a7187a7958e18300759
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483482"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Virtuális gépek üzembe helyezése dedikált gazdagépeken az Azure CLI használatával
 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 

Győződjön meg arról, hogy telepítette az Azure CLI 2.0.70 vagy újabb verzióját, majd jelentkezzen be egy Azure-fiókba `az login`használatával. 


## <a name="limitations"></a>Korlátozások

- A virtuális gépek méretezési csoportjai jelenleg nem támogatottak a dedikált gazdagépeken.
- A kezdeti kiadás a következő virtuálisgép-sorozatot támogatja: DSv3, ESv3, FSv2, LSv2 és MSv2. 
 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása 
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozza létre az erőforráscsoportot az az Group Create paranccsal. A következő példában létrehozunk egy *myDHResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása 

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában az [az VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) paranccsal hozzunk létre egy, a rendelkezésre állási zónákat és a tartalék tartományokat használó gazda-csoportot. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>További példák

Azt is megteheti, hogy az [az VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) paranccsal létrehoz egy gazdagép csoportot az 1. rendelkezésre állási zónában (és nincs tartalék tartomány).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
A következő az [az VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) paranccsal hoz létre egy gazdagépet, amely csak a tartalék tartományokat használja (azokat a régiókat kell használni, ahol a rendelkezésre állási zónák nem támogatottak). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Gazdagép létrehozása 

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.  A következő SKU-értékek támogatottak: DSv3_Type1 és ESv3_Type1.

A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

A gazdagép létrehozásához használja [az az VM Host Create](/cli/azure/vm/host#az-vm-host-create) paranccsal. Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása 
Hozzon létre egy dedikált gazdagépen belüli virtuális gépet [az az VM Create](/cli/azure/vm#az-vm-create)paranccsal. Ha a rendelkezésre állási zónát a gazda csoport létrehozásakor adta meg, akkor ugyanazt a zónát kell használnia a virtuális gép létrehozásakor.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép hibás állapotban lesz létrehozva. 


## <a name="check-the-status-of-the-host"></a>A gazdagép állapotának keresése

Megtekintheti a gazdagép állapotának állapotát, valamint azt, hogy hány virtuális gépet telepíthet tovább a gazdagépre az [az VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view)paranccsal.

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A kimenet a következőhöz hasonlóan fog kinézni:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportálás sablonként 
Ha most szeretne létrehozni egy további fejlesztési környezetet ugyanazzal a paraméterekkel, vagy egy olyan éles környezettel, amely megfelel a sablonnak, exportálhat egy sablont is. A Resource Manager JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozó teljes környezeteket hozhat létre. A JSON-sablonokat manuálisan is létrehozhatja, vagy exportálhat egy meglévő környezetet a JSON-sablon létrehozásához. Az az [Group export](/cli/azure/group#az-group-export) paranccsal exportálhatja az erőforráscsoportot.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Ez a parancs létrehozza a `myDHResourceGroup.json` fájlt az aktuális munkakönyvtárban. Amikor létrehoz egy környezetet a sablonból, a rendszer az összes erőforrás nevét kéri. Ezeket a neveket feltöltheti a sablon fájljába úgy, hogy hozzáadja a `--include-parameter-default-value` paramétert a `az group export` parancshoz. Szerkessze a JSON-sablont az erőforrásnevek megadásához, vagy hozzon létre egy Parameters. JSON fájlt, amely megadja az erőforrások nevét.
 
Ha létre szeretne hozni egy környezetet a sablonból, használja az [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create)elemet.

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

A dedikált gazdagépekre akkor is díjat számítunk fel, ha nincsenek virtuális gépek üzembe helyezése. Minden olyan gazdagépet törölni kell, amelyet jelenleg nem használ a költségek megtakarítására.  

Csak akkor törölhet egy gazdagépet, ha már nem használja a virtuális gépeket. Törölje a virtuális gépeket az [az VM delete](/cli/azure/vm#az-vm-delete)paranccsal.

```bash
az vm delete -n myVM -g myDHResourceGroup
```

A virtuális gépek törlése után törölheti a gazdagépet az [az VM Host delete](/cli/azure/vm/host#az-vm-host-delete)paranccsal.

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Miután törölte az összes gazdagépet, törölheti a gazdagép csoportot az [az VM Host Group delete](/cli/azure/vm/host/group#az-vm-host-group-delete)paranccsal.  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
A teljes erőforráscsoportot egyetlen parancsban is törölheti. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gépet, gazdagépet és gazdagépet is.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Következő lépések

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál.

- A [Azure Portal](dedicated-hosts-portal.md)használatával dedikált gazdagépeket is létrehozhat.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.