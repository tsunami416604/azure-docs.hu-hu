---
title: Az Azure Linux rendszerű virtuális Gépekhez való hozzáférés alaphelyzetbe állítása |} A Microsoft Docs
description: Rendszergazda felhasználók kezelése és hozzáférés a VMAccess bővítmény, az Azure CLI 2.0 használatával Linux virtuális gépek visszaállítása
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 51c203c746a5256924033ebe48d9ddfdc3823b16
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415070"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Kezelheti a rendszergazda felhasználókat, az SSH és az ellenőrzés, vagy javítsa ki a VMAccess bővítmény használata az Azure CLI 2.0 használatával Linux rendszerű virtuális gépek lemezeinek
## <a name="overview"></a>Áttekintés
A Linuxos virtuális gép lemezét hibák láthatók. Valahogy a legfelső szintű jelszó alaphelyzetbe állítása, a Linux rendszerű virtuális gép vagy véletlenül törölt a titkos SSH-kulcs. Amely történt, az adatközpontok napban vissza, ha a meghajtó van, és nyissa meg a kiszolgálói konzolnál beolvasni a KVM kell. Úgy az Azure VMAccess bővítmény, mint a KVM kapcsoló, amely lehetővé teszi a hozzáférés visszaállítása, Linux vagy a lemez szintű karbantartásával konzol eléréséhez.

Ez a cikk bemutatja, hogyan használhatja az Azure VMAccess bővítmény ellenőrzése vagy javítása, a lemez, felhasználói hozzáférés alaphelyzetbe állítása, rendszergazdai felhasználói fiókok kezelése vagy Linux rendszeren az SSH-konfigurációját frissíteni, ha Azure Resource Manager virtuális gépeken futnak. Ha klasszikus virtuális gépek – kezelésére van szüksége, kövesse az megtalálható a [klasszikus virtuális gép dokumentációja](../linux/classic/reset-access-classic.md). 

## <a name="prerequisites"></a>Előfeltételek
### <a name="operating-system"></a>Operációs rendszer

A Virtuálisgép-hozzáférési bővítmény ezen Linux-disztribúció futtatható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 12.04 LTS és |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 + 7.1 + |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 – 12 |
| OpenSuse | openSUSE azt 42.2 + |
| CentOS | A centOS 6.3 + 7.0 + |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>A VMAccess bővítmény használatával
Két módon, hogy a VMAccess bővítmény a Linuxos virtuális gépeken használhatja:

* Az Azure CLI 2.0 és a szükséges paramétereket használja.
* [Használja a nyers JSON-fájlokat, és a VMAccess bővítmény feldolgozására](#use-json-files-and-the-vmaccess-extension) , és ezután reagálhat rájuk.

A következő példákban [az a virtuális gép felhasználójának](/cli/azure/vm/user) parancsokat. Ezeket a lépéseket a legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

## <a name="update-ssh-key"></a>SSH-kulcs frissítése
Az alábbi példa frissíti az SSH-kulcsot a felhasználó `azureuser` nevű virtuális gépre `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Megjegyzés:** a `az vm user update` parancs hozzáfűzi az új nyilvános kulcs szövegét, a `~/.ssh/authorized_keys` a virtuális gépen a rendszergazda felhasználó fájlt. Ez nem cserélje le, vagy távolítsa el minden meglévő SSH-kulcsokat. Ez nem távolítja el korábbi kulcsok beállítása üzembe helyezési idő vagy a VMAccess bővítmény használatával ezt követő frissítésekben.

## <a name="reset-password"></a>Új jelszó létrehozása
A következő példa alaphelyzetbe állítja a jelszót a felhasználó `azureuser` nevű virtuális gépre `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Indítsa újra az SSH
A következő példa újraindítja az SSH démon, és visszaállítja az SSH-konfigurációt az alapértelmezett értékekre nevű virtuális gép `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Hozzon létre egy felügyeleti sudo/felhasználó
A következő példában létrehozunk egy nevű felhasználó `myNewUser` a **sudo** engedélyeket. A fiók hitelesítéséhez nevű virtuális gépre SSH-kulcsot használ `myVM`. Ez a módszer segít a virtuális Géphez való hozzáférés visszaszerzéséhez, abban az esetben jelenlegi hitelesítő adatok vannak elveszíti vagy elfelejti tervezték. Ajánlott eljárásként fiókok **sudo** engedélyeket kell korlátozni.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Felhasználó törlése
Az alábbi példával törölhet egy nevű felhasználó `myNewUser` nevű virtuális gépre `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON-fájlok és a VMAccess bővítmény használata
Az alábbi példák a nyers JSON-fájlokat használja. Használat [az virtuálisgép-bővítmény csoportot](/cli/azure/vm/extension#az_vm_extension_set) , majd hívja meg a JSON-fájlokat. Ezek a JSON-fájlok is meghívhat az Azure-sablonok. 

### <a name="reset-user-access"></a>Felhasználói hozzáférés alaphelyzetbe állítása
Ha a legfelső szintű hozzáférés elvesztette a Linux rendszerű virtuális gépen, indíthatja el a vmaccess bővítmény parancsfájl frissíteni egy felhasználó SSH-kulcs vagy jelszó.

A nyilvános SSH-kulcsot, hogy egy felhasználó frissítéséhez hozzon létre egy fájlt `update_ssh_key.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse be a `username` és `ssh_key` paraméterek:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Felhasználói jelszó alaphelyzetbe állítása, hozzon létre egy fájlt `reset_user_password.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse be a `username` és `password` paraméterek:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Indítsa újra az SSH
Az SSH démon újraindításához, és az SSH-konfiguráció visszaállítása az alapértelmezett értékeket, hozzon létre egy fájlt `reset_sshd.json`. Adja hozzá az alábbi tartalommal:

```json
{
  "reset_ssh": true
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Rendszergazda felhasználók kezelése

A felhasználó létrehozása **sudo** az engedélyeket, amelyek SSH-kulcsot használ a hitelesítéshez, hozzon létre egy fájlt `create_new_user.json` , és adja hozzá a beállítások a következő formátumban. A saját értékeit helyettesítse be a `username` és `ssh_key` paramétereket. Ez a módszer segít a virtuális Géphez való hozzáférés visszaszerzéséhez, abban az esetben jelenlegi hitelesítő adatok vannak elveszíti vagy elfelejti tervezték. Ajánlott eljárásként fiókok **sudo** engedélyeket kell korlátozni.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

A felhasználó törléséhez hozzon létre egy fájlt `delete_user.json` , és adja hozzá az alábbi tartalommal. Helyettesítse be a saját értékét a `remove_user` paramétert:

```json
{
  "remove_user":"myNewUser"
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Ellenőrizze, vagy javítsa ki a lemezt
Vmaccess bővítmény használatához ellenőrizze, és javítsa ki egy lemezt a Linux rendszerű virtuális géphez hozzáadott-e.

Ellenőrizze és javítsa ki a lemezt, hozzon létre egy fájlt `disk_check_repair.json` , és adja hozzá a beállítások a következő formátumban. Illessze be a saját nevét értéket `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Hajtsa végre a vmaccess bővítmény parancsfájlt:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
