---
title: Azure Linux rendszerű virtuális gép hozzáférésének visszaállítása | Microsoft Docs
description: A rendszergazda felhasználók kezelése és a Linux rendszerű virtuális gépek hozzáférésének alaphelyzetbe állítása a VMAccess-bővítmény és az Azure CLI használatával
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
ms.openlocfilehash: 447c10037503c627092bb23e23b4fe1ee88ca45d
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173955"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>A rendszergazda felhasználók, SSH és a Linux rendszerű virtuális gépeken található lemezek megkeresése és javítása az Azure CLI-vel való VMAccess-bővítmény használatával
## <a name="overview"></a>Áttekintés
A Linux rendszerű virtuális gépen lévő lemez hibákat jelenít meg. Valahogy alaphelyzetbe állítja a linuxos virtuális gép legfelső szintű jelszavát, vagy véletlenül törölte az SSH titkos kulcsát. Ha az adatközpont napjaiban történt vissza, akkor oda kell vezetnie, majd meg kell nyitnia a KVM-t, hogy lekérje a kiszolgáló konzolját. Tekintse át az Azure VMAccess bővítményt, amely a KVM-kapcsolót használja, amely lehetővé teszi a konzol elérését a Linuxhoz való hozzáférés alaphelyzetbe állításához vagy a lemez szintű karbantartáshoz.

Ebből a cikkből megtudhatja, hogyan használhatja az Azure VMAccess bővítményt a lemezek vizsgálatához és javításához, a felhasználói hozzáférés alaphelyzetbe állításához, a rendszergazdai felhasználói fiókok kezeléséhez, illetve a Linux SSH-konfigurációjának frissítéséhez, ha Azure Resource Manager virtuális gépekként fut. Ha klasszikus virtuális gépeket kell kezelnie – kövesse a [klasszikus VM-dokumentációban](../linux/classic/reset-access-classic.md)található utasításokat. 
 
> [!NOTE]
> Ha a VMAccess bővítmény használatával állítja alaphelyzetbe a virtuális gép jelszavát a HRE bejelentkezési bővítmény telepítése után, újra kell futtatnia a HRE-bejelentkezési bővítményt, hogy újra engedélyezze a HRE-bejelentkezést a gépen.

## <a name="prerequisites"></a>Előfeltételek
### <a name="operating-system"></a>Operációs rendszer

A virtuális gépek hozzáférési bővítménye a következő Linux-disztribúciók esetében futtatható:

| Disztribúció | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS és 12,04 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 +, 7.1 + |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 és 12 |
| OpenSuse | openSUSE LEAP 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Az VMAccess bővítmény használatának módjai
A Linux rendszerű virtuális gépeken kétféleképpen használhatja a VMAccess-bővítményt:

* Használja az Azure CLI-t és a szükséges paramétereket.
* [Használjon nyers JSON-fájlokat a VMAccess-bővítmény folyamatán](#use-json-files-and-the-vmaccess-extension) , majd járjon el.

Az alábbi példák az [az VM User](/cli/azure/vm/user) parancsait használják. Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="update-ssh-key"></a>SSH-kulcs frissítése
Az alábbi példa frissíti a felhasználó `azureuser` SSH-kulcsát a nevű `myVM`virtuális gépen:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **MEGJEGYZÉS:** A `az vm user update` parancs hozzáfűzi az új nyilvános kulcs szövegét `~/.ssh/authorized_keys` a virtuális gép rendszergazdai felhasználójának fájljához. Ez nem helyettesíti vagy nem távolítja el a meglévő SSH-kulcsokat. Ez a beállítás nem távolítja el az üzembe helyezéskor vagy a későbbi frissítésekben beállított korábbi kulcsokat a VMAccess-bővítmény használatával.

## <a name="reset-password"></a>Másik jelszó kérése
A következő példa alaphelyzetbe állítja a felhasználó `azureuser` jelszavát a nevű `myVM`virtuális gépen:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH újraindítása
Az alábbi példa újraindítja az SSH démont, és visszaállítja az SSH-konfigurációt egy nevű `myVM`virtuális gép alapértelmezett értékeire:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Felügyeleti/sudo-felhasználó létrehozása
A következő példa egy **sudo** engedélyekkel `myNewUser` ellátott felhasználót hoz létre. A fiók SSH-kulcsot használ a hitelesítéshez a nevű `myVM`virtuális gépen. Ez a módszer úgy lett kialakítva, hogy segítse a virtuális gépekhez való hozzáférés visszaszerzését abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtenek. Ajánlott eljárásként korlátozni kell a **sudo** engedélyekkel rendelkező fiókokat.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Felhasználó törlése
A következő példa töröl egy nevű `myNewUser` felhasználót a nevű `myVM`virtuális gépen:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>A JSON-fájlok és a VMAccess-bővítmény használata
Az alábbi példák nyers JSON-fájlokat használnak. Az [az VM Extension set](/cli/azure/vm/extension) paranccsal hívja meg a JSON-fájlokat. Ezek a JSON-fájlok az Azure-sablonokból is meghívhatók. 

### <a name="reset-user-access"></a>Felhasználói hozzáférés alaphelyzetbe állítása
Ha megszakadt a hozzáférése a Linux rendszerű virtuális gép gyökeréhez, elindíthat egy VMAccess-szkriptet a felhasználó SSH-kulcsának vagy jelszavának frissítéséhez.

Egy felhasználó nyilvános SSH-kulcsának frissítéséhez hozzon létre egy nevű `update_ssh_key.json` fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a `username` és `ssh_key` a paraméterek saját értékeit:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Egy felhasználói jelszó alaphelyzetbe állításához hozzon `reset_user_password.json` létre egy nevű fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a `username` és `password` a paraméterek saját értékeit:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

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
Az SSH démon újraindításához és az SSH-konfiguráció alapértelmezett értékre való visszaállításához hozzon létre egy nevű `reset_sshd.json`fájlt. Adja hozzá a következő tartalmat:

```json
{
  "reset_ssh": true
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

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

Ha olyan **sudo** -engedélyekkel rendelkező felhasználót szeretne létrehozni, amely SSH-kulcsot használ a hitelesítéshez `create_new_user.json` , hozzon létre egy nevű fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a `username` és `ssh_key` a paraméterek saját értékeit. Ez a módszer úgy lett kialakítva, hogy segítse a virtuális gépekhez való hozzáférés visszaszerzését abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtenek. Ajánlott eljárásként korlátozni kell a **sudo** engedélyekkel rendelkező fiókokat.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Egy felhasználó törléséhez hozzon létre egy nevű `delete_user.json` fájlt, és adja hozzá a következő tartalmat. Helyettesítse be a saját értékét `remove_user` a következő paraméterhez:

```json
{
  "remove_user":"myNewUser"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>A lemez keresése vagy javítása
A VMAccess használatával a linuxos virtuális géphez hozzáadott lemezeket is megtekintheti és kijavíthatja.

A lemez vizsgálatához és kijavításához hozzon létre egy `disk_check_repair.json` nevű fájlt, és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a saját értékét a következő `repair_disk`névre:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

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
