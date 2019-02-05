---
title: Linux Azure VM-rendszerképek létrehozása a Packer |} A Microsoft Docs
description: Ismerje meg, hogyan Packer használatával Linux virtuálisgép-rendszerképek létrehozása az Azure-ban
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3a7ac2e7a86a135f20f46b03be2c38af330a5367
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730339"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Linux virtuálisgép-rendszerképek létrehozása az Azure-ban a Packer használatával
Az Azure-ban minden virtuális gép (VM) jön létre egy rendszerképből, amely meghatározza a Linux-disztribúció és operációsrendszer-verzió. Rendszerképek előre telepített alkalmazások és konfigurációk tartalmazhatnak. Az Azure piactér sok első és a külső képek biztosít rendszerek leggyakoribb disztribúciói és alkalmazás-környezetekhez, vagy létrehozhat saját igényeire szabott lemezképek. Ez a cikk részletesen bemutatja a nyílt forráskódú eszköz [Packer](https://www.packer.io/) definiálására és egyéni lemezképeket az Azure-ban.


## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
Az összeállítási folyamat során Packer hozza létre, ideiglenes Azure-erőforrások, a forrás virtuális Gépet épít. Rögzíti a forrásoldali virtuális gép lemezképként való használatra, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport a Packer buildelési folyamat kimenete tárolja.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
Csomagolói hitelesíti az Azure-ral egyszerű szolgáltatás használatával. Azure-beli szolgáltatásnév egy biztonsági identitás, az alkalmazások, szolgáltatások és automatizálási eszközökkel, mint például a Packer használható. Szabályozhatja és az egyszerű szolgáltatás az Azure-ban hajthat végre műveleteket helyrendszerszerepkörökre engedélyeinek megadásához.

Az egyszerű szolgáltatás létrehozása [az ad sp create-for-rbac](/cli/azure/ad/sp) és csomagolói szükséges a hitelesítő adatokat:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Egy példa a kimenetre az az előző parancsokban a következőképpen történik:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure-beli hitelesítésre, is kell szerezze be az Azure-előfizetési Azonosítóját a [az fiók show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Ez a két parancs kimenete a következő lépésben fogja használni.


## <a name="define-packer-template"></a>Csomagoló sablon meghatározása
A lemezképeket, hozzon létre egy sablon JSON-fájlként. A sablonban és adjon meg sikerei provisioners, amely a tényleges buildelési folyamat végrehajtására. Csomagoló rendelkezik egy [Azure webhelykiépítőt](https://www.packer.io/docs/builders/azure.html) , amely lehetővé teszi, megadhatja az Azure-erőforrások, például az egyszerű szolgáltatás hitelesítő adatai az előző létrehozott lépést.

Hozzon létre egy fájlt *ubuntu.json* , és illessze be az alábbi tartalommal. Adja meg a saját értékeit a következőkhöz:

| Paraméter                           | Beszerzési helyét |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Első sorában kimenete `az ad sp` create parancs - *appId* |
| *client_secret*                     | A második sor kimenetét `az ad sp` create parancs - *jelszó* |
| *tenant_id*                         | A kimenet harmadik sora `az ad sp` create parancs - *bérlő* |
| *subscription_id*                   | A kimeneti `az account show` parancs |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport nevét |
| *managed_image_name*                | A létrehozott felügyelt lemez lemezkép neve |


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

Ez a sablon összeállít egy Ubuntu 16.04 LTS rendszerképet, telepíti az nginx-et, és a virtuális gép deprovisions.

> [!NOTE]
> Ha kibontja a sablon üzembe helyezése felhasználói hitelesítő adatokat, állítsa be, olvassa el az Azure-ügynök deprovisions webhelykiépítőt parancsot `-deprovision` helyett `deprovision+user`.
> A `+user` jelző összes felhasználói fiók távolít el a forrás virtuális Gépen.


## <a name="build-packer-image"></a>Csomagolói rendszerkép összeállítása
Ha még nincs telepítve a helyi gépen Packer [csomagolói telepítési utasításokat](https://www.packer.io/docs/install/index.html).

A rendszerkép létrehozásához adja meg a Packer sablonfájl módon:

```bash
./packer build ubuntu.json
```

Egy példa a kimenetre az az előző parancsokban a következőképpen történik:

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

A virtuális gép létrehozása, futtassa a provisioners és a központi telepítés tisztítása Packer néhány percig tart.


## <a name="create-vm-from-azure-image"></a>Virtuális gép létrehozása az Azure-rendszerképből
Most már létrehozhat egy virtuális Gépet a rendszerképből [az virtuális gép létrehozása](/cli/azure/vm). Adja meg a létrehozott lemezképet a `--image` paraméter. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* a *myPackerImage* és SSH-kulcsokat hoz létre, ha azok még nem léteznének:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ha szeretne egy másik erőforráscsoportban vagy régióban, mint a Packer lemezkép hozzon létre virtuális gépet, adja meg a rendszerkép neve helyett a lemezkép-azonosító. Szerezheti be a lemezkép-azonosító [kép megjelenítése az](/cli/azure/image#az-image-show).

A virtuális gép létrehozása néhány percet vesz igénybe. A virtuális gép létrehozása után jegyezze fel a `publicIpAddress` az Azure CLI által megjelenített. Ezzel a címmel a webböngészőn keresztül NGINX-webhely eléréséhez.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm) paranccsal:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Virtuális gép és az NGINX tesztelése
Most nyisson meg egy webböngészőt, és írja be a `http://publicIpAddress` címet a címsorba. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. Az alapértelmezett NGINX-lap a következő példához hasonlóan jelenik meg:

![Alapértelmezett NGINX-webhely](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>További lépések
Ebben a példában használt Packer a Virtuálisgép-rendszerkép létrehozása az NGINX már telepítve van. A Virtuálisgép-lemezkép együtt a meglévő központi telepítési munkafolyamatokba, mint például segítségével telepítse az alkalmazást az Ansible, Chef vagy Puppet a lemezképből létrehozott virtuális gépek.

További Packer példasablonkészlet más Linux-disztribúciók, lásd: [a GitHub-adattár](https://github.com/hashicorp/packer/tree/master/examples/azure).
