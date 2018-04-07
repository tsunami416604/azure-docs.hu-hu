---
title: Alaphelyzetbe állítja a hozzáférés az Azure Linux virtuális gépeken futó a VMAccess bővítmény használatával |} Microsoft Docs
description: Alaphelyzetbe állítja a hozzáférés az Azure Linux virtuális gépeken futó a VMAccess bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Kezelheti a felhasználókat, az SSH és az ellenőrzése vagy javítása Azure virtuális gépeken Linux a VMAccess bővítmény használata az Azure CLI 1.0 lemezeket
Ez a cikk bemutatja, hogyan használható az Azure-VMAcesss bővítmény ellenőrizze vagy javítsa ki a lemezt, alaphelyzetbe állítja a felhasználói hozzáférés, a felhasználói fiókok kezelése vagy a Linux SSHD konfigurációjának visszaállítása. A cikkben foglaltak végrehajtásához szükség van:

* egy Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)),
* és be kell jelentkeznie az [Azure parancssori felületre](../../cli-install-nodejs.md) a következővel: `azure login`.
* Az Azure parancssori felületnek `azure config mode arm` Azure Resource Manager módban *kell lennie*.


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands)– a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="quick-commands"></a>Gyors parancsok
A Linux virtuális gépeken vmaccess bővítmény használatához két módja van:

* Az Azure CLI 1.0 és a szükséges paraméterek használatával.
* Nyers VMAccess dolgozza fel, és a műveletek JSON-fájlokat használja.

A gyors parancs szakasz fogjuk használni az Azure CLI 1.0 `azure vm reset-access` metódust. A következő parancspéldákban cserélje le az értékeket, amelyek tartalmazzák a saját környezet értékekkel "példa".

## <a name="create-a-resource-group-and-linux-vm"></a>Egy erőforráscsoport és a Linux virtuális gép létrehozása
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Debian virtuális gép létrehozása
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Gyökér szintű jelszó alaphelyzetbe állítása
A gyökér szintű jelszó alaphelyzetbe állítása:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>SSH-kulcs visszaállítása
Az SSH-kulcs nem legfelső szintű felhasználó visszaállítása:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Felhasználó létrehozása
Felhasználó létrehozásához:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Felhasználó eltávolítása
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>SSHD alaphelyzetbe állítása
Alaphelyzetbe állítja a SSHD konfiguráció:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Részletes bemutató
### <a name="vmaccess-defined"></a>A vmaccess bővítmény definiálva:
A lemezt a Linux virtuális Gépet a hibák láthatók. Valamilyen módon alaphelyzetbe állítja a gyökér szintű jelszavát a Linux virtuális gép számára, vagy véletlenül törli a titkos SSH-kulcsot. Ha vissza a datacenter napban bekövetkezett, meg kell meghajtó van, és nyissa meg a kiszolgáló konzolján beolvasandó KVM. Az Azure VMAccess bővítmény gondol adott KVM kapcsolóéval, amely lehetővé teszi a hozzáférést a következőre Linux, vagy végezzen szintű konzol eléréséhez.

A részletes útmutatót fogjuk használni hosszú vmaccess bővítmény, amely nyers JSON-fájlokat használja.  Ezeket a vmaccess bővítmény JSON-fájlokat az Azure-sablonok alapján is hívható.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Ellenőrizze és javítsa ki a lemezt a Linux virtuális gépek vmaccess bővítmény használatával
Vmaccess bővítmény használatával elvégezhető egy fsck futtassa a Linux virtuális Gépet a lemezen.  Megteheti azt is, a lemez-ellenőrzés és egy lemezjavítás a vmaccess bővítmény használatával.

Ellenőrizze, és javítsa a lemezt használja a vmaccess bővítmény parancsfájlt:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Felhasználói hozzáférés visszaállítása a Linux vmaccess bővítmény használatával
Ha elvesztette a hozzáférést, legfelső szintű a Linux virtuális gépén, indítja el a vmaccess bővítmény parancsfájl a gyökér szintű jelszó alaphelyzetbe állítása.

A gyökér szintű jelszó alaphelyzetbe állításához a vmaccess bővítmény parancsprogram használata:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Az SSH-kulcs nem legfelső szintű felhasználó alaphelyzetbe állításához a vmaccess bővítmény parancsprogram használata:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Linux felhasználói fiókok kezelése a vmaccess bővítmény segítségével
VMAccess egy Python-parancsfájl, amely a Linux virtuális Gépet a felhasználók kezeléséhez a sudo vagy a root fiókjának és bejelentkezés nélkül is használható.

A felhasználó létrehozásához használja a vmaccess bővítmény parancsfájlt:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

A felhasználó törléséhez használja a vmaccess bővítmény parancsfájlt:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Alaphelyzetbe állítja a SSHD konfigurációs vmaccess bővítmény használatával
Ha a Linux virtuális gépek SSHD konfigurációs módosításokat, és az SSH-kapcsolat ellenőrzése a módosítások előtt zárja be, akkor előfordulhat, hogy meg kell akadályoznia SSH'ing vissza.  Vmaccess bővítmény használatával lehet visszaállítani a SSHD konfigurációs egy ismert helyes konfigurációra eseményazonosítójú SSH-n keresztül nélkül.

Alaphelyzetbe állítja a SSHD konfigurációs parancsfájllal a vmaccess bővítmény:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>További lépések
Frissítése Linux Azure VMAccess bővítmény használatával a módosításokat a futó Linux virtuális gép módszerrel.  Eszközök, például a felhő inicializálás és az Azure-sablonok segítségével módosíthatja a Linux virtuális gép rendszerindító.

[Virtuálisgép-bővítmények és szolgáltatásokkal kapcsolatban](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Linux Virtuálisgép-bővítmények az Azure Resource Manager sablonok készítése](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása során](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

