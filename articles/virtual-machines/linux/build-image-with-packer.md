---
title: Linux Azure virtuálisgép-lemezképek létrehozása a Packerrel
description: Ismerje meg, hogyan hozhat létre linuxos virtuális gépeket az Azure-ban a Packer használatával
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 3aec50b8c8f2033b7340bde15ea7670c1a0b6bb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534219"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Hogyan lehet a Packer használatával linuxos virtuálisgép-lemezképeket létrehozni az Azure-ban?
Az Azure-ban minden virtuális gép (VM) egy olyan lemezképből jön létre, amely meghatározza a Linux-disztribúciót és az operációs rendszer verzióját. A képek előre telepített alkalmazásokat és konfigurációkat tartalmazhatnak. Az Azure Marketplace számos első és harmadik féltől származó lemezképet biztosít a leggyakoribb disztribúciókhoz és alkalmazáskörnyezetekhez, vagy létrehozhat jattot az igényeinek megfelelő egyéni lemezképekhez. Ez a cikk bemutatja, hogyan használhatja a nyílt forráskódú eszközt [Packer](https://www.packer.io/) egyéni lemezképek definiálására és létrehozására az Azure-ban.

> [!NOTE]
> Az Azure most már rendelkezik egy szolgáltatás, az Azure Image Builder (előzetes verzió), a saját egyéni lemezképek meghatározásához és létrehozásához. Az Azure Image Builder a Packerre épül, így akár a meglévő Packer-rendszerhéj-kiépítési parancsfájlokat is használhatja vele. Az Azure Image Builder használatával ismerkedjen meg, [olvassa el a Linuxos virtuális gép létrehozása az Azure Image Builder szolgáltatással című témakört.](image-builder.md)


## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
A létrehozási folyamat során packer ideiglenes Azure-erőforrásokat hoz létre a forrás virtuális gép létrehozása során. A forrás virtuális gép lemezképként való rögzítéséhez meg kell határoznia egy erőforráscsoportot. A Packer-létrehozási folyamat kimenete ebben az erőforráscsoportban tárolódik.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
A Packer egy egyszerű szolgáltatás használatával hitelesíti magát az Azure-ral. Az Azure egyszerű szolgáltatásegy biztonsági identitás, amely az alkalmazások, szolgáltatások és automatizálási eszközök, például a Packer használható. Szabályozhatja és meghatározhatja az engedélyeket, hogy milyen műveleteket hajthat végre az egyszerű szolgáltatás az Azure-ban.

Hozzon létre egy egyszerű szolgáltatás az [ad sp create-for-rbac](/cli/azure/ad/sp) és a kimeneti hitelesítő adatokat, hogy Packer szüksége packer:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Az előző parancsok kimenetének egyik példája a következő:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Az Azure-ban való hitelesítéshez be kell szereznie az Azure-előfizetés-azonosítóját [is az-fiókshow-val:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

A következő lépésben a két parancs kimenetét használhatja.


## <a name="define-packer-template"></a>Packer-sablon definiálása
Képek létrehozásához hozzon létre egy sablont JSON-fájlként. A sablonban megadhatja a tényleges buildelő eljárást megvalósító építők és kiépítők. A Packer rendelkezik egy [azure-beli kirendeltel,](https://www.packer.io/docs/builders/azure.html) amely lehetővé teszi az Azure-erőforrások, például az előző lépésben létrehozott egyszerű szolgáltatáshitelesítő adatok meghatározását.

Hozzon létre egy *ubuntu.json* nevű fájlt, és illessze be a következő tartalmat. Adja meg saját értékeit a következőkre:

| Paraméter                           | Hol lehet beszerezni |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | A create parancs `az ad sp` kimenetének első sora - *appId* |
| *client_secret*                     | A create parancs `az ad sp` kimenetének második sora - *jelszó* |
| *tenant_id*                         | A create parancs `az ad sp` kimenetének harmadik sora - *bérlő* |
| *subscription_id*                   | Kimenet `az account show` parancsból |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport neve |
| *managed_image_name*                | A létrehozott felügyelt lemezkép neve |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Ez a sablon egy Ubuntu 16.04 LTS lemezképet hoz létre, telepíti az NGINX-et, majd dekálja a virtuális gép.

> [!NOTE]
> Ha ezt a sablont a felhasználói hitelesítő adatok kiépítési, módosítsa a `-deprovision` kiépítési `deprovision+user`parancsot, amely deprovision az Azure-ügynök olvasni, nem pedig.
> A `+user` jelző eltávolítja az összes felhasználói fiókot a forrás virtuális gépről.


## <a name="build-packer-image"></a>Packer-kép létrehozása
Ha még nem telepítette a Csomagolót a helyi számítógépre, [kövesse a Csomagoló telepítési utasításait.](https://www.packer.io/docs/install/index.html)

A packer-sablonfájl megadásával a következőként hozhatja létre a képet:

```bash
./packer build ubuntu.json
```

Az előző parancsok kimenetének egyik példája a következő:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Néhány percet vesz igénybe, amíg a Packer létrehozza a virtuális gép, a kiépítőket, és a központi telepítés karbantartása.


## <a name="create-vm-from-azure-image"></a>Virtuális gép létrehozása az Azure-lemezképből
Most már létrehozhat egy virtuális gép a rendszerképaz [vm létrehozása](/cli/azure/vm). Adja meg a `--image` paraméterrel létrehozott képet. A következő példa létrehoz egy *myVM* nevű virtuális gép a *myPackerImage-ből,* és sSH-kulcsokat hoz létre, ha azok még nem léteznek:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ha a Packer-lemezképnél eltérő erőforráscsoportban vagy régióban szeretne virtuális gépeket létrehozni, adja meg a lemezkép neve helyett a lemezképazonosítót. A képazonosítót az [az image show](/cli/azure/image#az-image-show)segítségével szerezheti be.

A virtuális gép létrehozása néhány percet vesz igénybe. A virtuális gép létrehozása után vegye `publicIpAddress` figyelembe az Azure CLI által megjelenített. Ez a cím az NGINX webhely webböngészőn keresztüli elérésére szolgál.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm) paranccsal:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Teszt virtuális gép és NGINX
Most nyisson meg egy webböngészőt, és írja be a `http://publicIpAddress` címet a címsorba. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. Az alapértelmezett NGINX-lap a következő példában látható:

![Alapértelmezett NGINX-webhely](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>További lépések
Az [Azure Image Builder](image-builder.md)meglévő Packer-kiépítési parancsfájlokat is használhat.
