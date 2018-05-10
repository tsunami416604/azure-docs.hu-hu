---
title: A csomagoló Linux Azure Virtuálisgép-lemezképek létrehozása |} Microsoft Docs
description: Csomagoló használata a Linux virtuális gépek létrehozását az Azure-ban
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/03/2018
ms.author: iainfou
ms.openlocfilehash: 7d7ba6a493cca3dd14829e6527136af6df424c05
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Linux virtuális gép képek létrehozása az Azure-ban a csomagoló segítségével
Minden virtuális gép (VM) az Azure-ban, amely meghatározza a Linux-disztribúció és az operációs rendszer verziója lemezkép jön létre. Lemezképek előre telepített alkalmazások és konfigurációk tartalmazhatnak. Az Azure piactéren lehetővé teszi számos első és harmadik fél rendszerképet leggyakoribb disztribúcióiról, valamint az alkalmazás környezetekben, vagy létrehozhat az igényeinek megfelelően igazított a saját egyéni lemezképek. Ez a cikk részletesen a nyílt forráskódú eszköz [csomagoló](https://www.packer.io/) definiálására és egyéni lemezképeket az Azure-ban.


## <a name="create-azure-resource-group"></a>Azure erőforráscsoport létrehozása
A létrehozási folyamat során a csomagoló ideiglenes Azure-erőforrások létrehozza a, a forrás Virtuálisgép-buildekről nyújtanak. A forrás virtuális gép képként rögzíti, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport tárolja a csomagoló felépítési folyamat kimenetét.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Az Azure hitelesítő adatok létrehozása
Csomagoló egyszerű szolgáltatás használatával végzi a hitelesítést. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a csomagoló használható. Szabályozza, és adja meg az engedélyeket, hogy milyen műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban.

Az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) és a hitelesítő adatait, amelyet a csomagoló kimeneti:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

A kimenet a fenti parancsok például a következőképpen történik:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

A hitelesítéshez az Azure-ba is be kell szereznie az Azure-előfizetése Azonosítóját [az fiók megjelenítése](/cli/azure/account#az_account_show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Ez a két parancs kimenete a következő lépésben használhatja.


## <a name="define-packer-template"></a>Adja meg a csomagoló sablon
Lemezképeket, létrehozhat egy sablon JSON-fájlként. A sablon megadása a létrehozói és a tényleges felépítési folyamat végrehajtott provisioners. Csomagoló rendelkezik egy [Azure webhelykiépítőt](https://www.packer.io/docs/builders/azure.html) , amely lehetővé teszi, hogy adható meg az Azure-erőforrások, például a szolgáltatás egyszerű létrehozott hitelesítő adatok az előző lépésben.

Hozzon létre egy fájlt *ubuntu.json* , majd illessze be a következő tartalmat. Adja meg a saját értékeket a következő:

| Paraméter                           | Beszerzési helyét |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Első sor kimenetét `az ad sp` parancs - *appId* |
| *client_secret*                     | A második sor kimenetét `az ad sp` parancs - *jelszó* |
| *tenant_id*                         | A kimenet a harmadik sorban `az ad sp` parancs - *bérlői* |
| *subscription_id*                   | A kimeneti `az account show` parancs |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport nevét |
| *managed_image_name*                | A felügyelt lemezképe létrehozott nevét |


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

Ez a sablon Ubuntu 16.04 LTS képet alkot, NGINX telepíti, majd a virtuális gép deprovisions.

> [!NOTE]
> Bontsa ki a sablon az rendelkezés felhasználói hitelesítő adatokat, ha úgy dönt, a webhelykiépítőt parancsot, amely az Azure agent olvasni deprovisions `-deprovision` helyett `deprovision+user`.
> A `+user` jelző összes felhasználói fiók eltávolítja a virtuális gép – forrásként.


## <a name="build-packer-image"></a>Csomagoló lemezkép
Ha még nincs telepítve a helyi számítógépre csomagoló [csomagoló telepítési utasításokat](https://www.packer.io/docs/install/index.html).

Megadásával hozhat létre a lemezképet a csomagoló sablonfájl az alábbiak szerint:

```bash
./packer build ubuntu.json
```

A kimenet a fenti parancsok például a következőképpen történik:

```bash
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

A virtuális gép létrehozása, a provisioners futtatására, és a központi telepítés tisztítása csomagoló néhány percet vesz igénybe.


## <a name="create-vm-from-azure-image"></a>Kép: Azure virtuális gép létrehozása
Mostantól létrehozhat egy virtuális Gépet a lemezkép [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Adja meg a létrehozott lemezképet a `--image` paraméter. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* a *myPackerImage* és az SSH-kulcsokat generál, ha még nem léteznek:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ha létrehozott egy virtuális gépet egy másik erőforráscsoportban található vagy a régió, mint a csomagoló lemezkép, adja meg a lemezkép neve helyett a lemezkép-Azonosítót. Ezt úgy szerezheti be a lemezkép-Azonosítót a [az kép megjelenítése](/cli/azure/image#az-image-show).

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után vegye figyelembe a `publicIpAddress` megjeleníti azokat az Azure parancssori felület. Ezt a címet a webböngésző segítségével NGINX webhely eléréséhez használt.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm#open-port) paranccsal:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Virtuális gép és NGINX tesztelése
Most nyisson meg egy webböngészőt, és írja be a `http://publicIpAddress` címet a címsorba. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. Az alapértelmezett NGINX lap jelenik meg az alábbi példában látható módon:

![Alapértelmezett NGINX-webhely](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>További lépések
Ebben a példában a csomagoló NGINX már telepítve van a Virtuálisgép-lemezkép létrehozásához használt. A Virtuálisgép-lemezkép mellett a meglévő központi telepítési munkafolyamatai, például segítségével telepítse az alkalmazást az Ansible, Chef vagy Puppet lemezkép alapján létrehozott virtuális gépek.

További példa csomagoló sablonokat más Linux disztribúciókkal, lásd: [a GitHub-tárház](https://github.com/hashicorp/packer/tree/master/examples/azure).