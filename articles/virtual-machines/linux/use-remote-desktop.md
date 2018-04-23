---
title: A Linux virtuális gép távoli asztal használata az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja az Azure-ban a grafikus eszközök Linux virtuális gép kapcsolódni a távoli asztal (xrdp)
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 1aa75b87b01417b8864632b7a09539bd6be05d0b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Telepítse és konfigurálja a távoli asztal egy Linux virtuális Gépet az Azure-ban való kapcsolódáshoz
A parancssorból, a secure shell (SSH-) kapcsolaton keresztül általában felügyelt Linux virtuális gépek (VM) az Azure-ban. Új Linux vagy gyors hibaelhárítási helyzetekben, amikor a távoli asztal használata könnyebben lehet. Ez a cikk részletesen telepítése és konfigurálása az asztali környezetet ([xfce](https://www.xfce.org)) és a távoli asztal ([xrdp](http://www.xrdp.org)) a Linux virtuális gép használata a Resource Manager üzembe helyezési modellben.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk egy meglévő Ubuntu 16.04 LTS Azure-ban van szükség. Ha a virtuális gép létrehozása az kell, használja a következő módszerek egyikét:

- A [Azure CLI 2.0](quick-create-cli.md)
- A [Azure-portálon](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Egy asztali környezet telepítése a Linux virtuális gépen
A legtöbb Linux virtuális gépet az Azure-ban nem rendelkeznek egy asztali környezetben alapértelmezés szerint telepítve. Linux virtuális gépek általában felügyelt asztali környezet helyett SSH-kapcsolatokhoz. Nincsenek a Linux, amelyek segítségével különböző asztali környezetekhez. Attól függően, hogy a munkakörnyezet választott akkor előfordulhat, hogy használnak egy – 2 GB szabad lemezterület, és telepítse és konfigurálja a szükséges csomagokat 5 – 10 percig tarthat.

A következő példa telepíti az egyszerűsített [xfce4](https://www.xfce.org/) asztali környezetben egy Ubuntu 16.04 LTS virtuális gépen. Parancsok a némileg eltérő más terjesztési (használata `yum` Red Hat Enterprise Linux telepítését és konfigurálását a megfelelő `selinux` szabályokat, vagy használja `zypper` SUSE, például telepítése).

Első, az SSH-kapcsolatot a virtuális Gépet. Az alábbi példa nevű virtuális gép csatlakozik *myvm.westus.cloudapp.azure.com* a felhasználónevet használva a *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha Windows használ, és az SSH használatával további információra van szüksége, tekintse meg [SSH használata a Windows kulcsok](ssh-from-windows.md).

Következő lépésként telepítse a xfce használatával `apt` az alábbiak szerint:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>A távoli asztali kiszolgáló telepítése és konfigurálása
Most, hogy egy asztali környezetben telepített, konfigurálja a távoli asztali szolgáltatás a bejövő kapcsolatok figyelésére. [xrdp](http://xrdp.org) egy nyílt forráskódú Remote Desktop Protocol (RDP) kiszolgáló, amely elérhető a legtöbb Linux terjesztéseket, és jól xfce működik. Telepíthető xrdp az Ubuntu virtuális gép az alábbiak szerint:

```bash
sudo apt-get install xrdp
```

Mondja el xrdp milyen asztali környezet a munkamenet indításakor. Adja meg xrdp használandó xfce az asztali környezetet az alábbiak szerint:

```bash
echo xfce4-session >~/.xsession
```

Indítsa újra a xrdp szolgáltatást, a módosítások érvénybe lépéséhez az alábbiak szerint:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Egy helyi felhasználói fiók jelszavának beállítása
Ha létrehozott egy jelszót a felhasználói fiók létrehozása után a virtuális gép, kihagyhatja ezt a lépést. Ha csak SSH kulcs hitelesítést használ, és nem rendelkezik a helyi fiók jelszavának megadásához adjon meg egy jelszót, mielőtt xrdp jelentkezzen be a virtuális Gépet. xrdp nem fogadja el az SSH-kulcsok a hitelesítéshez. Az alábbi példában a felhasználói fiókhoz tartozó jelszót ad meg *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Adja meg a jelszót nem frissíti a SSHD konfigurációját, és lehetővé teszik a jelszavas bejelentkezéseket, ha jelenleg nem létezik. Biztonsági szempontból előfordulhat, hogy szeretne csatlakozni a virtuális gép SSH-alagút-alapú hitelesítést használ, és xrdp csatlakozzon. Ha igen, a távoli asztali forgalom engedélyezésére hálózati biztonsági csoport szabályt hoz létre a következő lépés kihagyásához.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>A távoli asztal forgalmat hálózati biztonsági csoport szabály létrehozása
A távoli asztal forgalom való elérni a Linux virtuális Gépet, a hálózati biztonsági csoport szabályt kell létrehozni, amely lehetővé teszi a TCP-port 3389-es elérni a virtuális Gépet. További információ a hálózati biztonsági csoportszabályok: [Mi az a hálózati biztonsági csoport?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Emellett [egy hálózati biztonsági szabály létrehozása az Azure portál segítségével](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az alábbi példa létrehoz egy hálózati biztonsági csoport szabály [az vm-port megnyitása](/cli/azure/vm#az_vm_open_port) porton *3389-es*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>A Linux virtuális gép csatlakozzon a távoli asztali ügyfél
Nyissa meg a helyi távoli asztali ügyfél, és kapcsolódjon az IP-cím vagy a Linux virtuális Gépet DNS-nevét. Írja be a felhasználónevet és jelszót a felhasználói fiók a virtuális gépen az alábbiak szerint:

![A távoli asztali ügyfél használatával xrdp kapcsolódni](./media/use-remote-desktop/remote-desktop-client.png)

Hitelesítés után a xfce asztali környezet betölteni, és keresse meg a következőhöz hasonló:

![asztali környezetben xfce xrdp keresztül](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Hibaelhárítás
Ha a Linux virtuális gép egy távoli asztali ügyfél nem tud csatlakozni, használja `netstat` a Linux virtuális gépén ellenőrzése, hogy a virtuális Gépet a figyeli az RDP-kapcsolatok az alábbiak szerint:

```bash
sudo netstat -plnt | grep rdp
```

A következő példa bemutatja a várt módon 3389-es TCP-porton VM:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Ha a *xrdp-sesman* szolgáltatás nem figyel, az Ubuntu virtuális gép indítsa újra a szolgáltatást az alábbiak szerint:

```bash
sudo service xrdp restart
```

Tekintse át naplók */var/log* a helyet, ennek okát a Ubuntu virtuális gép a szolgáltatás nem válaszol. A syslog is végezhet figyelést, a hibák megtekintése a távoli asztali kapcsolat tett kísérlet során:

```bash
tail -f /var/log/syslog
```

Előfordulhat, hogy más például Red Hat Enterprise Linux és SUSE Linux terjesztésekről különböző módokon újraindítja a szolgáltatásokat és alternatív naplófájl helye áttekintése.

Ha a távoli asztali ügyfél nem kapott választ, és nem látja a rendszernaplóba eseményeket, ez arra utal, hogy a távoli asztali forgalom nem tudja elérni a virtuális Gépet. Tekintse át a hálózati biztonsági csoportszabályok annak érdekében, hogy rendelkezik-e egy szabályt, amely engedélyezi a TCP-port 3389-es. További információkért lásd: [alkalmazás csatlakozási problémák](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>További lépések
Létrehozásával és az SSH-kulcsok használata a Linux virtuális gépek kapcsolatos további információkért lásd: [hozzon létre SSH-kulcsok Linux virtuális gépek Azure-ban](mac-create-ssh-keys.md).

Az SSH használata a Windows további információkért lásd: [SSH használata a Windows kulcsok](ssh-from-windows.md).

