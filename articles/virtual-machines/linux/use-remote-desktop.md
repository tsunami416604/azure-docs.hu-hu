---
title: Linux virtuális géphez a távoli asztal használata az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Linux rendszerű virtuális gép az Azure-ban a grafikus eszközök használatával kapcsolódni a távoli asztal (xrdp)
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 44b0f8b3d11b737ad88f7d33b036b52d24b70e33
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960327"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Telepítse és konfigurálja az Azure-beli Linuxos virtuális gép kapcsolódni a távoli asztal
Linux rendszerű virtuális gépek (VM) az Azure-ban általában kezelhetők a parancssorból a secure shell-(SSH) kapcsolatot használ. Ha új, Linux, illetve gyors hibaelhárítási forgatókönyveket, a távoli asztal használata egyszerűbb lehet. Ez a cikk részletesen bemutatja egy asztali környezet telepítéséhez és konfigurálásához ([xfce](https://www.xfce.org)) és a távoli asztal ([xrdp](http://www.xrdp.org)) a Linux rendszerű virtuális gép a Resource Manager üzemi modell használatával.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk egy meglévő Ubuntu 16.04 LTS virtuális Gépet az Azure-ban van szükség. Ha szeretne létrehozni egy virtuális Gépet, használja a következő módszerek egyikét:

- A [az Azure CLI](quick-create-cli.md)
- Az [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Egy asztali környezet telepítése a Linux rendszerű virtuális gépen
A legtöbb Linux rendszerű virtuális gépek az Azure-ban nem kell egy asztali környezet alapértelmezés szerint telepítve. Linux rendszerű virtuális gépek általában egy asztali környezet helyett az SSH-kapcsolatok használatával történik. Nincsenek a Linux, amelyek segítségével különböző asztali környezetekben. Asztali környezet válaszaitól függően előfordulhat, hogy egy – 2 GB szabad lemezterület használata, és telepítése és konfigurálása a szükséges csomagokat 5-10 percet is igénybe vehet.

A következő példa telepíti az egyszerűsített [xfce4](https://www.xfce.org/) asztali környezet egy Ubuntu 16.04 LTS virtuális gép. Parancsok, a többi a disztribúciókat némileg eltérőek lehetnek (használata `yum` Red Hat Enterprise Linux-alapú telepítése és konfigurálása a megfelelő `selinux` szabályokat, vagy használja `zypper` SUSE, például telepíthető).

Első, a virtuális gép ssh-KAPCSOLATOT. Az alábbi példa csatlakozik a virtuális gép nevű *myvm.westus.cloudapp.azure.com* , amelynek *azureuser*. A saját értékeit használja:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha Windows használ, és az SSH-val további információra van szüksége, tekintse meg [az SSH használata a Windows-kulcsok](ssh-from-windows.md).

Ezután telepítse a xfce használatával `apt` módon:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>A távoli asztali kiszolgáló telepítése és konfigurálása
Most, hogy telepítve van egy asztali környezetben, konfigurálja egy távoli asztali szolgáltatás figyelni a bejövő kapcsolatokat. [xrdp](http://xrdp.org) egy nyílt forráskódú Remote Desktop Protocol (RDP)-kiszolgáló, amely a legtöbb Linux-disztribúció elérhető, és jól működik a xfce. Xrdp a Ubuntu virtuális Gépen a következőképpen telepítheti:

```bash
sudo apt-get install xrdp
sudo systemctl enable xrdp
```

Mondja el xrdp milyen asztali környezet használata a munkamenet indításakor. Xrdp xfce módon az asztali környezet használatához konfigurálja:

```bash
echo xfce4-session >~/.xsession
```

Indítsa újra a xrdp szolgáltatást, a módosítások érvénybe a következőképpen:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Egy helyi felhasználói fiók jelszavának beállítása
Ha létrehozott egy jelszót a felhasználói fiókjához a virtuális gép létrehozásakor, kihagyhatja ezt a lépést. Ha csak az SSH-kulcsos hitelesítést, és nem rendelkezik egy helyi fiók jelszavának beállítása, adjon meg egy jelszót, mielőtt xrdp jelentkezzen be a virtuális Gépre. xrdp nem fogadja el az SSH-kulcsokat a hitelesítéshez. Az alábbi példa meghatározza a felhasználói fiókhoz tartozó jelszó *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Adja meg a jelszót nem lehet frissíteni az SSHD konfiguráció engedélyezze a jelszavas bejelentkezéseket, ha jelenleg nem létezik. Biztonsági szempontból előfordulhat, hogy szeretne csatlakozni a virtuális Géphez SSH-alagút-alapú hitelesítéssel rendelkező, és kapcsolódjon xrdp. Ha igen, hagyja ki a következő lépés egy hálózati biztonsági csoportra vonatkozó szabályt, hogy a távoli asztali forgalmat létrehozásával.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>A távoli asztali forgalmat a hálózati biztonsági csoport szabály létrehozása
Hogy a távoli asztal adatforgalom elérje a Linuxos virtuális gép, hálózati biztonsági csoport szabályt kell létrehozni, amely lehetővé teszi, hogy elérje a virtuális Gépét a 3389-es TCP. Hálózati biztonsági csoport szabályai kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Emellett [az Azure portal használatával hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A következő példában létrehozunk egy hálózati biztonsági csoportra vonatkozó szabályt az [az vm open-port](/cli/azure/vm#az-vm-open-port) porton *3389-es*. Az Azure parancssori felületen nem az SSH-munkamenetből a virtuális géphez, nyissa meg a következő hálózati biztonsági csoportra vonatkozó szabályt:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>A Linux rendszerű virtuális gép csatlakoztatása a távoli asztali ügyfél
Nyissa meg a helyi távoli asztali ügyfél, és kapcsolódjon az IP-cím vagy a Linux rendszerű virtuális gép DNS-nevét. Adja meg a felhasználónevet és jelszót a felhasználói fiók a virtuális Gépen a következő:

![A távoli asztali ügyfél használatával xrdp csatlakozni](./media/use-remote-desktop/remote-desktop-client.png)

Hitelesítés után a xfce asztali környezet betöltése, és a következő példához hasonlóan néz ki:

![xfce asztali környezetben keresztül xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Ha a helyi RDP-ügyfél a hálózati szintű hitelesítés (NLA) használ, szükség lehet, hogy kapcsolati beállítás letiltása. XRDP jelenleg nem támogatja a NLA. Alternatív támogató NLA, mint például az RDP-megoldások is megtekintheti [FreeRDP](http://www.freerdp.com).


## <a name="troubleshoot"></a>Hibaelhárítás
Ha a Linux virtuális Gépet egy távoli asztali ügyfél nem tud csatlakozni, használja a `netstat` a Linux rendszerű virtuális gépen, győződjön meg arról, hogy a virtuális gép figyel az RDP-kapcsolatok a következő:

```bash
sudo netstat -plnt | grep rdp
```

Az alábbi példa bemutatja a virtuális gép a várt módon 3389-es TCP-portot figyeli:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Ha a *xrdp-sesman* szolgáltatás nem figyel, Ubuntu VM-en indítsa újra a szolgáltatást a következő:

```bash
sudo service xrdp restart
```

Tekintse át naplók */var/log* a helyet is, ezért az Ubuntu virtuális Gépen a szolgáltatás nem válaszol. A syslog is figyelheti a hibák megtekintéséhez távoli asztali kapcsolódási kísérlet során:

```bash
tail -f /var/log/syslog
```

Előfordulhat, hogy más Linux-disztribúciók, például a Red Hat Enterprise Linux és SUSE módjai, indítsa újra a szolgáltatásokat, és tekintse át a másik naplófájl helye.

Ha nem kap választ a távoli asztali ügyfél, és nem jelenik meg a rendszernaplóba eseményeket, ez a viselkedés azt jelzi, hogy a távoli asztali forgalmat nem tudja elérni a virtuális gép. Tekintse át a hálózati biztonsági csoport szabályait, gondoskodjon arról, hogy egy szabályt a 3389-es porton a TCP engedélyezéséhez. További információkért lásd: [alkalmazások csatlakozási hibáinak elhárítása](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>További lépések
Létrehozásával és SSH-kulcsok használata Linux rendszerű virtuális gépekkel kapcsolatos további információkért lásd: [hozzon létre SSH-kulcsok az Azure-beli Linux rendszerű virtuális gépek](mac-create-ssh-keys.md).

Az SSH együttes Windows információkért lásd: [az SSH használata a Windows-kulcsok](ssh-from-windows.md).

