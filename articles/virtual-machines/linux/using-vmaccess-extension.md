---
title: "Hozzáférés az Azure Linux virtuális gép visszaállítása |} Microsoft Docs"
description: "A rendszergazda felhasználók kezelése, és alaphelyzetbe állítja a hozzáférés a Linux virtuális gépeken a VMAccess bővítmény és az Azure CLI 2.0 használatával"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 235a6367ad317945cfeaaa6aae4e060208fb8e8e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Rendszergazda felhasználók, az SSH és az ellenőrzés kezeléséhez, vagy javítsa ki a Linux virtuális gépeken a VMAccess bővítmény használata az Azure CLI 2.0 lemezek
A lemezt a Linux virtuális Gépet a hibák láthatók. Valamilyen módon alaphelyzetbe állítja a gyökér szintű jelszavát a Linux virtuális gép számára, vagy véletlenül törli a titkos SSH-kulcsot. Ha vissza a datacenter napban bekövetkezett, meg kell meghajtó van, és nyissa meg a kiszolgáló konzolján beolvasandó KVM. Az Azure VMAccess bővítmény gondol adott KVM kapcsolóéval, amely lehetővé teszi a hozzáférést a következőre Linux, vagy végezzen szintű konzol eléréséhez.

Ez a cikk bemutatja, hogyan használható az Azure VMAccess bővítmény ellenőrizze vagy javítsa ki a lemez, alaphelyzetbe állítja a felhasználói hozzáférés, rendszergazdai fiókok kezelése, vagy visszaállítja az SSH-konfigurációt Linux. Az [Azure CLI 1.0-s](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket.


## <a name="ways-to-use-the-vmaccess-extension"></a>A VMAccess bővítmény használatának módjai
Kétféleképpen használható a VMAccess bővítmény a Linux virtuális gépeken:

* Használja az Azure CLI 2.0 és a szükséges paramétereket.
* [Használja a VMAccess bővítmény feldolgozó nyers JSON-fájlok](#use-json-files-and-the-vmaccess-extension) és majd rájuk.

A következő példákban [az vm felhasználói](/cli/azure/vm/user) parancsok. A következő lépésekkel lesz szüksége a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

## <a name="reset-ssh-key"></a>SSH-kulcs visszaállítása
Az alábbi példa visszaállítja az SSH-kulcs a felhasználó `azureuser` nevű virtuális gépen `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Új jelszó létrehozása
Az alábbi példában a felhasználó jelszava alaphelyzetbe állítása `azureuser` nevű virtuális gépen `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Indítsa újra az SSH
A következő példa az SSH démon újraindul, és visszaállítja az SSH-konfigurációt az alapértelmezett értékekre a nevű virtuális gép `myVM`:

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Hozzon létre felügyeleti sudo/felhasználót
Az alábbi példakód létrehozza a felhasználó nevű `myNewUser` rendelkező **sudo** engedélyek. A fiók egy SSH-kulcsot használ hitelesítést nevű virtuális gép `myVM`. Ez a módszer segítségével újra hozzáférést nyerni egy virtuális Gépet, abban az esetben aktuálisan használt hitelesítő adatok elvesztése vagy elfelejtése tervezték. Ajánlott eljárásként fiókok **sudo** engedélyeket kell korlátozni.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Felhasználó törlése
A következő példa egy megnevezett felhasználó törli `myNewUser` nevű virtuális gépen `myVM`:

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON-fájlok és a VMAccess bővítmény
Az alábbi példák nyers JSON-fájlokat használja. Használjon [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#az_vm_extension_set) majd hívni a JSON-fájlokat. A JSON-fájlok az Azure-sablonok alapján is hívható. 

### <a name="reset-user-access"></a>Felhasználói hozzáférés alaphelyzetbe állítása
Ha elvesztette a hozzáférést, legfelső szintű a Linux virtuális gépre, a felhasználó az SSH-kulcsot, vagy a jelszó alaphelyzetbe állítása a vmaccess bővítmény parancsfájl indíthatja el.

Alaphelyzetbe állítja a nyilvános SSH-kulcs egy olyan felhasználó, hozzon létre egy fájlt `reset_ssh_key.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse a `username` és `ssh_key` paraméterek:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Felhasználói jelszó alaphelyzetbe állítása, hozzon létre egy fájlt `reset_user_password.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse a `username` és `password` paraméterek:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Indítsa újra az SSH
Indítsa újra az SSH démon, és visszaállítja az SSH-konfigurációt az alapértelmezett értékekre, hozzon létre egy fájlt `reset_sshd.json`. Adja hozzá a következőket:

```json
{
  "reset_ssh": true
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Rendszergazdák kezelése

A felhasználó létrehozása **sudo** engedélyek, egy SSH-kulcsot használ, hozzon létre egy fájlt `create_new_user.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse a `username` és `ssh_key` paraméterek. Ez a módszer segítségével újra hozzáférést nyerni egy virtuális Gépet, abban az esetben aktuálisan használt hitelesítő adatok elvesztése vagy elfelejtése tervezték. Ajánlott eljárásként fiókok **sudo** engedélyeket kell korlátozni.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Felhasználó törlése, hozzon létre egy fájlt `delete_user.json` , és adja hozzá a következő tartalmat. Helyettesítse a saját értéke a `remove_user` paraméter:

```json
{
  "remove_user":"myNewUser"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Ellenőrizze, vagy javítsa ki a lemez
Vmaccess bővítmény használatával ellenőrizze, és javítsa ki egy lemezt, a Linux virtuális gép hozzáadott-e.

Ellenőrizze és javítsa ki a lemezt, hozzon létre egy fájlt `disk_check_repair.json` , és adja hozzá a beállítások a következő formátumban. Helyettesítse a saját nevét a következő `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

A vmaccess bővítmény parancsprogram végrehajtása:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>További lépések
Az Azure VMAccess bővítmény használatával Linux frissítése a módosításokat a futó Linux virtuális gép módszerrel. Eszközök, például a felhő inicializálás és az Azure Resource Manager-sablonok segítségével módosíthatja a Linux virtuális gép rendszerindító.

[Virtuálisgép-bővítmények és a Linux funkcióit](extensions-features.md)

[Linux Virtuálisgép-bővítmények az Azure Resource Manager sablonok készítése](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása során](using-cloud-init.md)

