---
title: SSH-kulcspár létrehozása és használata Linuxos virtuális gépekhez az Azure-ban
description: SSH nyilvános és titkos kulcspár létrehozása és használata linuxos virtuális gépekhez az Azure-ban a hitelesítési folyamat biztonságának növelése érdekében.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969042"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: SSH nyilvános és privát kulcspár létrehozása és használata Linuxos virtuális gépekhez az Azure-ban

Egy biztonságos rendszerhéj (SSH) kulcspár, hozhat létre virtuális gépek (VM-ek) az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség jelszavak bejelentkezésére. Ez a cikk bemutatja, hogyan hozhat létre és használhat gyorsan egy SSH nyilvános és titkos kulcsú fájlpárlinuxos virtuális gépekhez. Ezeket a lépéseket az Azure Cloud Shell, egy macOS vagy Linux gazdagép, a Windows Alrendszer Linuxhoz és az OpenSSH-t támogató egyéb eszközökkel hajthatja végre. 

> [!NOTE]
> Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint le vannak tiltva a jelszavakkal, ami nagyban növeli a találgatásos találgatások nehézségét. 

További hátteret és példákat az [SSH-kulcspárok létrehozásának részletes lépései](create-ssh-keys-detailed.md)című témakörben talál.

Az SSH-kulcsok Windows-számítógépen történő létrehozásának és használatának további módjairól az [SSH-kulcsok használata az Azure-ban a Windows rendszerrel.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

A `ssh-keygen` paranccsal SSH nyilvános és személyes kulcsfájlokat hozhat létre. Alapértelmezés szerint ezek a fájlok a ~/.ssh könyvtárban jönnek létre. Megadhat egy másik helyet, és egy opcionális jelszót (*jelszót*) a személyes kulcsfájl eléréséhez. Ha az adott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

A következő parancs létrehoz egy SSH kulcspárt RSA titkosítással és 4096 bithosszal:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Ha az [Azure CLI](/cli/azure) segítségével hozza létre a virtuális gép az [az vm create](/cli/azure/vm#az-vm-create) paranccsal, `--generate-ssh-keys` opcionálisan sSH nyilvános és személyes kulcsfájlokat hozhat létre a beállítás használatával. A kulcsfájlok a ~/.ssh könyvtárban tárolódnak, `--ssh-dest-key-path` hacsak nincs más ként megadva. A `--generate-ssh-keys` beállítás nem írja felül a meglévő kulcsfájlokat, hanem hibát ad vissza. A következő parancsban cserélje le a *VMname* és *rGname nevet* a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>SSH nyilvános kulcs biztosítása virtuális gép telepítésekor

Ha SSH-kulcsokat használó Linux virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát, amikor létrehozza a virtuális gépaz Azure Portalon, az Azure CLI-n, az Azure Resource Manager-sablonokon vagy más módszereken keresztül:

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha nem ismeri az SSH nyilvános kulcs formátumát, a következő `cat` paranccsal jelenítheti meg a nyilvános kulcsot, és szükség esetén lecserélheti `~/.ssh/id_rsa.pub` a saját nyilvános kulcsfájljának elérési útját és fájlnevét:

```bash
cat ~/.ssh/id_rsa.pub
```

Egy tipikus nyilvános kulcs érték a következő példához hasonlóan néz ki:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Ha másolja és illessze be a nyilvános kulcsú fájl tartalmát az Azure Portalon vagy egy Resource Manager-sablonban, győződjön meg arról, hogy nem másol semmilyen követő szóközt. Ha nyilvános kulcsot szeretne másolni a macOS rendszerben, a rendszer a programba is átviheti `pbcopy`a nyilvános kulcsfájlt. A Linuxhoz hasonlóan a nyilvános kulcsfájlt is `xclip`átadhatja olyan programoknak, mint a .

A nyilvános kulcs, amely et a Linux virtuális gép az Azure-ban alapértelmezés szerint a ~/.ssh/id_rsa.pub, kivéve, ha a kulcspár létrehozásakor megadott egy másik helyet. Az [Azure CLI 2.0](/cli/azure) használatával hozza létre a virtuális gép egy meglévő nyilvános kulcs, adja meg az értéket, `--ssh-key-values` és szükség szerint a helyét a nyilvános kulcs az az [vm create](/cli/azure/vm#az-vm-create) parancs a beállítással. A következő parancsban cserélje le a *VMname*, *RGname*és *keyFile* fájlt a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Ha több SSH-kulcsot szeretne használni a virtuális géppel, megadhatja őket egy `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`szóköznel elválasztott listában, például ebben.


## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

A nyilvános kulcs üzembe helyezése az Azure virtuális gép, és a személyes kulcs a helyi rendszeren, SSH a virtuális gép a virtuális gép IP-címét vagy DNS-nevét használja. A következő parancsban cserélje le az *azureuser t* és *myvm.westus.cloudapp.azure.com* a rendszergazda felhasználónevére és a teljesen minősített tartománynévre (vagy IP-címre):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor megadott egy jelszót, írja be a jelszót, amikor a bejelentkezési folyamat során a rendszer kéri. A virtuális gép hozzáadódik a ~/.ssh/known_hosts fájlhoz, és nem kéri, hogy csatlakozzon újra, amíg az Azure virtuális gép nyilvános kulcsa nem változik, vagy a kiszolgáló nevét el nem távolítja a ~/.ssh/known_hosts.

Ha a virtuális gép a just-in-time hozzáférési szabályzatot használja, hozzáférést kell kérnie, mielőtt csatlakozhatna a virtuális géphez. A just-in-time szabályzatról a [Virtuálisgép-hozzáférés kezelése az just in time házirend használatával című témakörben](../../security-center/security-center-just-in-time.md)talál további információt.

## <a name="next-steps"></a>További lépések

* Az SSH kulcspárokkal való munkáról az [SSH-kulcspárok létrehozásának és kezelésének részletes lépései](create-ssh-keys-detailed.md)című témakörben talál további információt.

* Ha nehézségekbe ütközik az Azure-beli virtuális gépekhez való SSH-kapcsolatok, olvassa [el az SSH-kapcsolatok hibaelhárítása Azure Linux os virtuális géphez című témakört.](troubleshoot-ssh-connection.md)
