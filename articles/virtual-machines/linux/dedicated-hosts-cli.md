---
title: Linuxos virtuális gépek telepítése dedikált állomásokra a CLI használatával
description: Telepítse a virtuális gépeket dedikált állomásokra az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127694"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Virtuális gépek telepítése dedikált állomásokra az Azure CLI használatával
 

Ez a cikk végigvezeti, hogyan hozhat létre egy Azure [dedikált gazdagépet](dedicated-hosts.md) a virtuális gépek (Virtuális gépek) üzemeltetéséhez. 

Győződjön meg arról, hogy telepítette az Azure CLI 2.0.70-es vagy újabb verzióját, és bejelentkezett egy Azure-fiókba a használatával. `az login` 


## <a name="limitations"></a>Korlátozások

- A virtuálisgép-méretezési csoportok jelenleg nem támogatottak dedikált állomásokon.
- A dedikált gazdagépek számára elérhető méretek és hardvertípusok régiónként eltérőek lehetnek. További információ a gazdagép [díjszabási oldalán.](https://aka.ms/ADHPricing)
 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása 
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozza létre az erőforráscsoportot az az csoport létrehozásával. A következő példa létrehoz egy *myDHResourceGroup* nevű erőforráscsoportot az *USA keleti részén.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása 

A **gazdagépcsoport** olyan erőforrás, amely dedikált gazdagépek gyűjteményét képviseli. Hozzon létre egy gazdagépcsoportot egy régióban és egy rendelkezésre állási zónát, és adjon hozzá állomásokat. A magas rendelkezésre állás tervezésekor további lehetőségek is rendelkezésre állnak. Az alábbi lehetőségek egyikét vagy mindkettőt használhatja a dedikált gazdagépekkel: 
- Több rendelkezésre állási zónára is kiterjedjen. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagépcsoporttal.
- Több tartalék tartományban, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagépcsoport tartaléktartomány-számát. Ha nem szeretné span tartalék tartományok a csoportban, használja a tartalék tartomány száma 1. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában az [az vm gazdagépcsoport-létrehozást](/cli/azure/vm/host/group#az-vm-host-group-create) fogunk használni egy gazdagépcsoport létrehozásához a rendelkezésre állási zónák és a tartalék tartományok használatával. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>További példák

Az [vm gazdagépcsoport létrehozása](/cli/azure/vm/host/group#az-vm-host-group-create) is használható egy gazdagépcsoport létrehozásához az 1.

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
A következő használja [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) egy gazdagépcsoport csak tartalék tartományok használatával (olyan régiókban használható, ahol a rendelkezésre állási zónák nem támogatottak). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Állomás létrehozása 

Most hozzunk létre egy dedikált gazdagép a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a termékváltozatot is az állomásszámára. A gazdatermékváltozat rögzíti a támogatott virtuális gép sorozat, valamint a hardver-generálás a dedikált gazdagép.  

A gazdagép számos szolgáltatásáról és díjszabásáról az [Azure dedikált gazdagépdíj-szabása](https://aka.ms/ADHPricing)című témakörben talál további információt.

Az [vm host create](/cli/azure/vm/host#az-vm-host-create) használatával hozzon létre egy gazdagép. Ha tartalék tartományszámot állít be a gazdagépcsoporthoz, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása 
Hozzon létre egy virtuális gépet egy dedikált gazdagépen belül [az vm create](/cli/azure/vm#az-vm-create)használatával. Ha a gazdagépcsoport létrehozásakor megadott egy rendelkezésre állási zónát, akkor ugyanazt a zónát kell használnia a virtuális gép létrehozásakor.

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
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép sikertelen állapotban jön létre. 


## <a name="check-the-status-of-the-host"></a>Az állomás állapotának ellenőrzése

Ellenőrizheti a gazdagép állapotát, és hogy hány virtuális gépet telepíthet továbbra is a gazdagépre az [vm host get-instance-view használatával.](/cli/azure/vm/host#az-vm-host-get-instance-view)

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A kimenet így fog kinézni:
 
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
Exportálhat egy sablont, ha most létre szeretne hozni egy további fejlesztői környezetet ugyanazzal a paraméterrel, vagy egy olyan éles környezetet, amely megfelel annak. Az Erőforrás-kezelő JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozva teljes környezeteket hozhat létre. A JSON-sablonokat manuálisan is létrehozhatja, vagy exportálhatja a meglévő környezetet a JSON-sablon létrehozásához. Az [csoport exportálása](/cli/azure/group#az-group-export) az az csoport exportálásával exportálhatja az erőforráscsoportot.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Ez a `myDHResourceGroup.json` parancs létrehozza a fájlt az aktuális munkakönyvtárban. Amikor ebből a sablonból hoz létre egy környezetet, a program az összes erőforrásnevet kéri. Ezeket a neveket a sablonfájlban `--include-parameter-default-value` úgy feltöltheti, hogy hozzáadja a paramétert a `az group export` parancshoz. A JSON-sablon szerkesztésével adja meg az erőforrásneveket, vagy hozzon létre egy parameters.json fájlt, amely megadja az erőforrásneveket.
 
Ha környezetet szeretne létrehozni a sablonból, használja [az az csoport központi telepítési létrehozását.](/cli/azure/group/deployment#az-group-deployment-create)

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Akkor is díjat számítunk fel a dedikált gazdagépekért, ha nincsenek üzembe helyezve virtuális gépek. Törölje azokat a gazdagépeket, amelyeket jelenleg nem használ a költségek megtakarítására.  

Csak akkor törölheti az állomást, ha már nincsenek olyan virtuális gépek, amelyek azt használják. Törölje a virtuális gépeket [az az vm delete](/cli/azure/vm#az-vm-delete)használatával.

```bash
az vm delete -n myVM -g myDHResourceGroup
```

A virtuális gépek törlése után törölheti az állomást az [vm host delete](/cli/azure/vm/host#az-vm-host-delete)használatával.

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Miután törölte az összes állomást, törölheti a gazdagépcsoportot az [vm gazdagépcsoport törlésével.](/cli/azure/vm/host/group#az-vm-host-group-delete)  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
A teljes erőforráscsoportot egyetlen parancsban is törölheti. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gépet, állomást és gazdagépcsoportot.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>További lépések

- További információt a [Dedikált állomások](dedicated-hosts.md) áttekintése című témakörben talál.

- Az [Azure Portal](dedicated-hosts-portal.md)használatával dedikált állomásokat is létrehozhat.

- Van egy mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy régióban. [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)