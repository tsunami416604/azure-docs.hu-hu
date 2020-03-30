---
title: Hozzáférés visszaállítása egy Azure Linux-alapú virtuális géphez
description: Felügyeleti felhasználók kezelése és hozzáférés visszaállítása Linux os virtuális gépeken a VMAccess-bővítmény és az Azure CLI használatával
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250360"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Felügyeleti felhasználók, SSH kezelése, valamint lemezek ellenőrzése vagy javítása Linux os virtuális gépeken a VMAccess-bővítmény használatával az Azure CLI-vel
## <a name="overview"></a>Áttekintés
A Linux virtuális gép lemeze hibákat jelenít meg. Valahogy visszaállítja a Linux VM gyökérjelszavát, vagy véletlenül törölte az SSH titkos kulcsát. Ha ez történt vissza a nap az adatközpont, akkor kell vezetni oda, majd nyissa meg a KVM-hoz kap a kiszolgáló konzolon. Gondoljon az Azure VMAccess-bővítményre úgy, mint a KVM-kapcsolóra, amely lehetővé teszi a konzol elérését a Linuxhoz való hozzáférés alaphelyzetbe állításához vagy a lemezszintű karbantartás hoz.

Ez a cikk bemutatja, hogyan használhatja az Azure VMAccess-bővítményt egy lemez ellenőrzésére vagy javítására, a felhasználói hozzáférés alaphelyzetbe állítására, a felügyeleti felhasználói fiókok kezelésére vagy az SSH-konfiguráció frissítésére Linuxon, amikor Az Azure Resource Manager virtuális gépekként futnak. Ha klasszikus virtuális gépeket kell kezelnie , kövesse a [klasszikus virtuális gép dokumentációjában](../linux/classic/reset-access-classic.md)található utasításokat. 
 
> [!NOTE]
> Ha a VMAccess-bővítmény segítségével alaphelyzetbe állítja a virtuális gép jelszavát az AAD bejelentkezési bővítmény telepítése után, újra kell futtatnia az AAD bejelentkezési bővítményt az AAD bejelentkezés i agéphez való újbóli engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek
### <a name="operating-system"></a>Operációs rendszer

A VM Access-bővítmény a következő Linux-disztribúciókon futtatható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS és 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 és 12 |
| Opensuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>A VMAccess bővítmény használati módjai
A VMAccess-bővítményt kétféleképpen használhatja a Linux-virtuális gépeken:

* Használja az Azure CLI és a szükséges paramétereket.
* [Használja a nyers JSON-fájlokat, amelyeket a VMAccess-bővítmény folyamata során,](#use-json-files-and-the-vmaccess-extension) majd a cselekvés.

A következő példák [az vm felhasználói](/cli/azure/vm/user) parancsokat használnak. A lépések végrehajtásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)

## <a name="update-ssh-key"></a>SSH-kulcs frissítése
A következő példa frissíti a felhasználó `azureuser` SSH-kulcsát a névvel ellátott `myVM`virtuális gépen:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **MEGJEGYZÉS:** A `az vm user update` parancs hozzáfűzi az új `~/.ssh/authorized_keys` nyilvános kulcs szövegét a virtuális gép rendszergazdai felhasználója számára. Ez nem helyettesíti és nem távolítja el a meglévő SSH-kulcsokat. Ez nem távolítja el a telepítéskor beállított korábbi kulcsokat vagy a VMAccess-bővítményen keresztül i.

## <a name="reset-password"></a>Új jelszó létrehozása
A következő példa visszaállítja `azureuser` a felhasználó jelszavát a névvel ellátott `myVM`virtuális gépen:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH újraindítása
A következő példa újraindítja az SSH démont, és visszaállítja az `myVM`SSH-konfigurációt az alapértelmezett értékekre egy virtuális gép en:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Rendszergazdai/sudo felhasználó létrehozása
A következő példa `myNewUser` **sudo** engedélyekkel elnevezett felhasználót hoz létre. A fiók SSH-kulcsot használ a hitelesítéshez a nevű `myVM`virtuális gépen. Ez a módszer segít a virtuális géphez való hozzáférés visszanyerésében abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtődnek. Ajánlott eljárásként a **sudo** engedélyekkel rendelkező fiókokat korlátozni kell.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Felhasználó törlése
A következő példa töröl `myNewUser` egy felhasználót, `myVM`akit a névvel ellátott virtuális gépről neveztek el:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>A JSON-fájlok és a VMAccess bővítmény használata
A következő példák nyers JSON-fájlokat használnak. Használja [az vm kiterjesztés készlet,](/cli/azure/vm/extension) majd hívja meg a JSON-fájlokat. Ezek a JSON-fájlok azure-sablonokból is hívhatók. 

### <a name="reset-user-access"></a>Felhasználói hozzáférés alaphelyzetbe állítása
Ha elvesztette a hozzáférést a linuxos virtuális gép root, elindíthat egy VMAccess-parancsfájlt a felhasználó SSH-kulcsának vagy jelszavának frissítéséhez.

Egy felhasználó SSH nyilvános kulcsának frissítéséhez `update_ssh_key.json` hozzon létre egy elnevezett fájlt, és adjon hozzá beállításokat a következő formátumban. Helyettesítse saját `username` értékeit a és `ssh_key` a paraméterek:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

A felhasználói jelszó alaphelyzetbe állításához hozzon létre egy elnevezett `reset_user_password.json` fájlt, és adjon hozzá beállításokat a következő formátumban. Helyettesítse saját `username` értékeit a és `password` a paraméterek:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH újraindítása
Az SSH démon újraindításához és az SSH-konfiguráció alapértelmezett értékekre `reset_sshd.json`való visszaállításához hozzon létre egy nevű fájlt. Adja hozzá a következő tartalmat:

```json
{
  "reset_ssh": true
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Rendszergazdai felhasználók kezelése

Ha olyan **sudo** engedélyekkel rendelkező felhasználót szeretne létrehozni, amely `create_new_user.json` SSH-kulcsot használ a hitelesítéshez, hozzon létre egy elnevezett fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse a `username` saját `ssh_key` értékeit a és a paramétereket. Ez a módszer segít a virtuális géphez való hozzáférés visszanyerésében abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtődnek. Ajánlott eljárásként a **sudo** engedélyekkel rendelkező fiókokat korlátozni kell.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Felhasználó törléséhez hozzon létre `delete_user.json` egy nevű fájlt, és adja hozzá a következő tartalmat. Helyettesítse a `remove_user` saját értékét a paraméterrel:

```json
{
  "remove_user":"myNewUser"
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>A lemez ellenőrzése vagy javítása
A VMAccess használatával ellenőrizheti és javíthatja a Linux virtuális géphez hozzáadott lemezt is.

A lemez ellenőrzéséhez, majd javításához `disk_check_repair.json` hozzon létre egy nevű fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse saját értékét `repair_disk`a következő nevével:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

A VMAccess-parancsfájl végrehajtása a következővel:

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
