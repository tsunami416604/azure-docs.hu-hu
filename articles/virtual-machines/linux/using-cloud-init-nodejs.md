---
title: "Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása az Azure-ban |} Microsoft Docs"
description: "Felhő inicializálás használata során az Azure CLI 1.0 létrehozása Linux virtuális gép testreszabása"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 0b6150bca333188666935b3c9aa02c4b33690db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása az Azure CLI 1.0 során
Ez a cikk bemutatja, hogyan végezheti el a felhő inicializálás parancsfájl az állomásnév, a telepített csomagokat, és felhasználói fiókok kezelése.  A felhő inicializálás parancsfájlok során a virtuális gép létrehozása az Azure CLI nevezzük.  A cikkben foglaltak végrehajtásához szükség van:

* egy Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)),
* és be kell jelentkeznie az [Azure parancssori felületre](../../cli-install-nodejs.md) a következővel: `azure login`.
* Az Azure parancssori felületnek `azure config mode arm` Azure Resource Manager módban *kell lennie*.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="quick-commands"></a>Gyors parancsok
Hozzon létre egy felhő-init.txt parancsfájlt, amely beállítja az állomásnevet, frissíti az összes csomagot és sudo felhasználót ad hozzá Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Hozzon létre egy erőforráscsoportot, a virtuális gépek elindítása.

```azurecli
azure group create myResourceGroup westus
```

Linux virtuális gép létrehozása felhőben inicializálás konfigurálásának rendszerindítás során.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Részletes bemutató
### <a name="introduction"></a>Bevezetés
Amikor új Linux virtuális gép elindításához testreszabott vagy készen áll az Ön igényeinek kap egy szabványos Linux virtuális gép, és semmi ne legyen. [Felhő inicializálás](https://cloudinit.readthedocs.org) szabványos módja, szúrjon be ezt a Linux virtuális Gépet egy parancsfájl vagy a konfigurációs beállítások, az első alkalommal szolgáltatáshoz indítja.

Azure, a három különböző módja van módosításokat Linux virtuális gép, mert folyamatban van telepítve, vagy a legközelebbi.

* Parancsfájlok felhő inicializálás használatával helyezhet el.
* Az Azure használatával parancsfájlok szúrjon [VMAccess bővítmény](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Azure-sablon alapján felhő inicializálás használatával.
* Egy Azure-sablon használatával [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A beillesztendő parancsfájlok indítás után bármikor:

* SSH közvetlenül a parancsok futtatásához
* Az Azure használatával parancsfájlok szúrjon [VMAccess bővítmény](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), imperatively vagy az Azure-sablon alapján
* Konfigurációs felügyeleti eszközök, például Ansible, védőérték, Chef vagy Puppet.

> [!NOTE]
> : A VMAccess bővítmény legfelső szintű SSH használatával ugyanúgy lehet egy parancsfájl végrehajtása.  Azonban a Virtuálisgép-bővítmény használatával lehetővé teszi, hogy számos hasznos a forgatókönyvtől függően az Azure által kínált.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Felhő inicializálás elérhetőségét a Azure virtuális gép gyors-lemezkép aliasok létrehozására:
| Alias | Közzétevő | Ajánlat | SKU | Verzió | felhő inicializálás |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |legújabb |nem |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |igen |
| Debian |credativ |Debian |8 |legújabb |nem |
| openSUSE |SUSE |openSUSE |13.2 |legújabb |nem |
| RHEL |Redhat |RHEL |7.2 |legújabb |nem |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |legújabb |igen |

A Microsoft a partnerekkel együttműködve az beszerzése felhő inicializálás tartalmazza, és működik-e az Azure biztosít lemezképeket a dolgozik.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>A felhő inicializálás parancsfájl hozzáadása a virtuális gép létrehozása az Azure parancssori felülettel
Indítsa el a felhő inicializálás parancsfájl, amikor egy virtuális gép létrehozása az Azure-ban, adja meg a felhő inicializálás fájlt az Azure parancssori felület használatával `--custom-data` váltani.

Hozzon létre egy erőforráscsoportot, a virtuális gépek elindítása.

```azurecli
azure group create myResourceGroup westus
```

Linux virtuális gép létrehozása felhőben inicializálás konfigurálásának rendszerindítás során.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Állítsa be a Linux virtuális gép állomásnevét felhő inicializálás parancsfájl létrehozása
A legegyszerűbb és legfontosabb beállításait minden Linux virtuális gép egyik lenne az állomásnevet. A Microsoft könnyedén állíthat be a felhő inicializálás használja ezt a parancsfájlt.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Példa felhő inicializálás parancsfájl nevű `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

A virtuális gép kezdeti indításkor, a felhő inicializálás parancsfájl értékűre állítja be az állomásnév `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Bejelentkezés, és ellenőrizze az új virtuális gép állomásnevét.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Linux frissítése felhő inicializálás parancsfájl létrehozása
A biztonság érdekében célszerű az Ubuntu virtuális gép frissítése a első indításakor.  Felhő inicializálás is nézzük meg, attól függően, hogy a Linux-disztribúció használ, a kövesse parancsfájl használatával.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Példa felhő inicializálás parancsfájl `cloud_config_apt_upgrade.txt` a Debian termékcsalád
```sh
#cloud-config
apt_upgrade: true
```

Miután Linux betöltődött, a telepített csomagok segítségével frissített `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Bejelentkezés, és ellenőrizze, hogy az összes csomag frissítése.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Felhasználó hozzáadása Linux felhő inicializálás parancsfájl létrehozása
Az első feladatok számára bármely új Linux virtuális gép egyik felhasználó hozzáadása a szolgáltatást, vagy kerülje a `root`. SSH kulcs-e a biztonság és a használhatóság ajánlott eljárás, és hozzáadja őket a `~/.ssh/authorized_keys` a felhő inicializálás parancsfájl-fájlt.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Példa felhő inicializálás parancsfájl `cloud_config_add_users.txt` Debian termékcsalád
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Miután Linux betöltődött, a felsorolt felhasználók létrehozása és a sudo csoportba felvett.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Bejelentkezés, és ellenőrizze az újonnan létrehozott felhasználó.

```bash
ssh myVM
cat /etc/group
```

Kimenet

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Következő lépések
Felhő inicializálás szabványos úgy lehet módosítani a Linux virtuális gép rendszerindító válik. Azure is rendelkezik a Virtuálisgép-bővítmények, amelyek lehetővé teszik a LinuxVM rendszerindító, vagy futás közben módosítani. Például használhatja az Azure VMAccessExtension SSH vagy felhasználói adatok visszaállítására, a virtuális gép futása közben. A felhő inicializálás a jelszó alaphelyzetbe állítása újraindítás kellene.

[Virtuálisgép-bővítmények és szolgáltatásokkal kapcsolatban](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Kezelheti a felhasználókat, az SSH és az ellenőrzése vagy javítása Azure virtuális gépeken Linux a VMAccess bővítmény használatával lemezek](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

