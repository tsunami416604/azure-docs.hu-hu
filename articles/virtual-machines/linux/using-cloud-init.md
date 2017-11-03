---
title: "Használja a felhő inicializálás Linux virtuális gép testreszabása |} Microsoft Docs"
description: "Felhő inicializálás használata a Linux virtuális gép testreszabása az Azure CLI 2.0 létrehozása során"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Felhő inicializálás használni ahhoz, hogy egy Linux virtuális Gépet az Azure-ban
Ez a cikk bemutatja, hogyan használható [felhő inicializálás](https://cloudinit.readthedocs.io) , hogy az állomásnevet, csomagok, és az Azure virtuális gépen (VM) felhasználói fiókok kezelése. A felhő inicializálás parancsfájlok futtathatók a rendszerindító, ha a virtuális gép létrehozása az Azure CLI 2.0-s verziójával. A részletesebb megtudhatja, hogyan telepíthet alkalmazásokat, konfigurációs fájlok írási és kulcsok a Key Vault szúrjon, [ebben az oktatóanyagban](tutorial-automate-vm-deployment.md). Az [Azure CLI 1.0-s](using-cloud-init-nodejs.md) verziójával is elvégezheti ezeket a lépéseket.


## <a name="cloud-init-overview"></a>Felhő inicializálás áttekintése
[Felhő inicializálás](https://cloudinit.readthedocs.io) Linux virtuális gép testreszabása, először elinduló széles körben használt módszer. Felhő inicializálás használhatja csomagok és a fájlok írási, vagy a felhasználók és biztonsági beállításainak. Felhő inicializálás során a rendszerindítási folyamat fut, mert nincsenek további lépéseket vagy a konfiguráció alkalmazásához szükséges ügynökök.

Felhő inicializálás terjesztéseket is használható. Például, hogy ne használjon **apt-get-telepítés** vagy **yum telepítése** csomag telepítéséhez. Helyette megadhatja a telepítendő csomagok listája. Felhő inicializálás automatikusan használja a natív csomag felügyeleti eszköz a distro választja.

A Microsoft a partnerekkel együttműködve az beszerzése felhő inicializálás tartalmazza, és működik-e az Azure biztosít lemezképeket a dolgozik. Az alábbi táblázat ismerteti a jelenlegi felhő inicializálás elérhetőségét Azure platformon lemezképek:

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-ES LTS VERZIÓ |legújabb |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |


## <a name="set-the-hostname-with-cloud-init"></a>Állítsa be a felhő inicializálás állomásnév
Felhő inicializálás fájlok nyelven íródtak [YAM](http://www.yaml.org). Virtuális gép létrehozása az Azure-felhő inicializálás parancsfájl futtatását [az virtuális gép létrehozása](/cli/azure/vm#create), adja meg a felhő inicializálás fájlt a `--custom-data` váltani. Néhány példa arra, mi is konfigurálhatja a felhő inicializálás fájl vizsgáljuk meg. Egy általános forgatókönyv is, hogy a virtuális gép állomásnevét. Alapértelmezés szerint az állomásneve megegyezik a virtuális gép nevét. 

Először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példakód létrehozza a erőforráscsoportot *myResourceGroup* a a *eastus* helye:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy fájlt az aktuális rendszerhéjban *cloud_init_hostname.txt* , majd illessze be a következő konfigurációt. A felhő rendszerhéj nem a helyi számítógépen hozzon létre például a fájlt. A szerkesztő kívánja használata. Adja meg a felhő rendszerhéj `sensible-editor cloud_init_hostname.txt` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor:

```yaml
#cloud-config
hostname: myhostname
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_hostname.txt` az alábbiak szerint:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Létrehozása után az Azure parancssori felület információval is szolgál a virtuális Gépet. Használja a `publicIpAddress` az SSH-kapcsolatot a virtuális Gépet. Adja meg a saját címet a következőképpen:

```bash
ssh azureuser@publicIpAddress
```

A virtuális gép nevét megjelenítéséhez használja a `hostname` parancsot a következőképpen:

```bash
hostname
```

A virtuális Gépet, hogy állítsa a felhő inicializálás fájlban, ahogy az az alábbi példa kimenetben értékként jelenteniük kell az állomásnév:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Frissítse a virtuális gép felhőalapú inicializálás
Biztonsági okokból érdemes lehet konfigurálni a virtuális gép első indításakor a legújabb frissítések alkalmazásához. Felhő inicializálás különböző Linux disztribúciókkal keresztül működik, mert nincs szükség meg `apt` vagy `yum` a package manager számára. Helyette megadhatja `package_upgrade` és a felhő inicializálás folyamat határozza meg a distro használja a megfelelő mechanizmus segítségével. Ez a munkafolyamat lehetővé teszi, hogy az azonos felhő inicializálás parancsfájlok disztribúciókkal használja.

Tekintse meg a frissítési folyamat működés közben, hozzon létre egy felhő-init fájlt *cloud_init_upgrade.txt* , majd illessze be a következő konfigurációt:

```yaml
#cloud-config
package_upgrade: true
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_upgrade.txt` az alábbiak szerint:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

SSH-kapcsolatot a virtuális Gépet, a fenti parancs kimenetében megjelennek a nyilvános IP-címét. A saját nyilvános IP-címet adja meg a következők szerint:

```bash
ssh azureuser@publicIpAddress
```

Futtassa a csomag felügyeleti eszköz és a frissítések. Az alábbi példában `apt-get` egy Ubuntu virtuális gépen:

```bash
sudo apt-get upgrade
```

Felhő inicializálás ellenőrzi, és a frissítések telepítése a rendszerindító, nincsenek frissítések alkalmazásához, ahogy az a következő egy példa a kimenetre:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Felhasználó hozzáadása a virtuális gép és felhő inicializálás
Az első feladatok számára bármely új Linux virtuális gép egyik hozzáadni egy felhasználót, hogy kerülje el a szolgáltatást *legfelső szintű*. SSH-kulcsok a biztonsági és a használhatóság ajánlott eljárás. Kulcsok bekerülnek a *~/.ssh/authorized_keys* a felhő inicializálás parancsfájl-fájlt.

Felhasználó hozzáadása a Linux virtuális gépek, hozzon létre egy felhő-init fájlt *cloud_init_add_user.txt* , majd illessze be a következő konfigurációt. Adja meg a saját nyilvános kulcsát (például a tartalmát *~/.ssh/id_rsa.pub*) a *ssh-jogosult-kulcsok*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_add_user.txt` az alábbiak szerint:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

SSH-kapcsolatot a virtuális Gépet, a fenti parancs kimenetében megjelennek a nyilvános IP-címét. A saját nyilvános IP-címet adja meg a következők szerint:

```bash
ssh myadminuser@publicIpAddress
```

Ellenőrizze, hogy a felhasználó lett felvéve a virtuális gép és a megadott csoportokra, tekintse meg a tartalmát a */etc/csoport* fájlt az alábbiak szerint:

```bash
cat /etc/group
```

Az alábbi példa kimenetben látható a felhasználó nem a *cloud_init_add_user.txt* fájl hozzá lett adva a virtuális gép és a megfelelő csoporthoz:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Következő lépések
Felhő inicializálás módosítani a Linux-alapú virtuális gépek rendszerindító szabványos módszerek valamelyikével. Az Azure-ban is használhatja Virtuálisgép-bővítmények a Linux virtuális gép rendszerindító, vagy ha fut-e módosítani. Az Azure Virtuálisgép-bővítmény segítségével például parancsprogram végrehajtása a virtuális gép futó, nem csupán a először el kell indítani. További információ a Virtuálisgép-bővítmények: [VM bővítményeket és szolgáltatásokat](extensions-features.md), vagy a bővítmény használatával tekintse meg a [felhasználók kezelése, SSH, és ellenőrizze vagy Azure virtuális gépeken Linux használatával a VMAccess bővítménylemezekjavítása](using-vmaccess-extension.md).