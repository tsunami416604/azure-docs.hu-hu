---
title: Távoli asztal használata Linux os virtuális géphez az Azure-ban
description: Megtudhatja, hogy miként telepítheti és konfigurálhatja a Távoli asztalt (xrdp) linuxos virtuális géphez való csatlakozáshoz az Azure-ban grafikus eszközök használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2e97442d4104f52c1a76ba8cd1d81c99508bb242
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605189"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Telepítse és konfigurálja a Távoli asztalt, hogy linuxos virtuális géphez csatlakozzon az Azure-ban
Az Azure-ban a Linux virtuális gépek (VM-ek) kezelése általában a parancssorból történik egy biztonságos rendszerhéj (SSH) kapcsolat használatával. Amikor új a Linux, vagy a gyors hibaelhárítási forgatókönyvek, a távoli asztal használata egyszerűbb lehet. Ez a cikk részletezi, hogyan telepíthet és konfigurálhat asztali környezetet ([xfce](https://www.xfce.org)) és távoli asztalt ([xrdp](http://xrdp.org)) a Linux virtuális géphez az Erőforrás-kezelő telepítési modell használatával.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk egy meglévő Ubuntu 18.04 LTS virtuális gép az Azure-ban. Ha virtuális gép létrehozásához szükséges, használja az alábbi módszerek egyikét:

- Az [Azure CLI](quick-create-cli.md)
- Az [Azure portál](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Asztali környezet telepítése linuxos virtuális gépre
Az Azure-ban a legtöbb Linux-alapú virtuális gép alapértelmezés szerint nincs telepítve asztali környezettel. A Linux virtuális gépeket általában SSH-kapcsolatok, nem pedig asztali környezet használatával kezelik. Vannak különböző asztali környezetek Linux, hogy lehet választani. Az asztali környezet től függően előfordulhat, hogy 1–2 GB lemezterületet foglal el, és 5–10 percet vesz igénybe az összes szükséges csomag telepítése és konfigurálása.

A következő példa telepíti a könnyű [xfce4](https://www.xfce.org/) asztali környezetben egy Ubuntu 18.04 LTS vm. A más disztribúciók parancsai kis mértékben eltérnek (a `yum` Red Hat Enterprise Linux ra való telepítéshez és a megfelelő `selinux` szabályok konfigurálásához, vagy például a SUSE-ra való `zypper` telepítéshez).

Először is, SSH a virtuális gép. A következő példa csatlakozik a *myvm.westus.cloudapp.azure.com* nevű virtuális géphez az *azureuser*felhasználónevével. Használja a saját értékeit:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha Windows rendszert használ, és további információra van szüksége az SSH használatával kapcsolatban, olvassa el [az SSH-kulcsok használata a Windows rendszerrel című témakört.](ssh-from-windows.md)

Ezután telepítse az `apt` xfce-t az alábbiak szerint:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Távoli asztali kiszolgáló telepítése és konfigurálása
Most, hogy telepítve van egy asztali környezet, állítson be egy távoli asztali szolgáltatást a bejövő kapcsolatok figyelésére. [Az xrdp](http://xrdp.org) egy nyílt forráskódú RDP (Remote Desktop Protocol) kiszolgáló, amely a legtöbb Linux disztribúción elérhető, és jól működik az xfce-vel. Telepítse az xrdp-t az Ubuntu VM-re az alábbiak szerint:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Mondja el az xrdp-nek, hogy milyen asztali környezetet használjon a munkamenet indításakor. Konfigurálja az xrdp-t úgy, hogy az xfce-t használja asztali környezetként az alábbiak szerint:

```bash
echo xfce4-session >~/.xsession
```

Indítsa újra az xrdp szolgáltatást, hogy a módosítások az alábbiak szerint lépjenek érvénybe:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Helyi felhasználói fiók jelszavának beállítása
Ha a virtuális gép létrehozásakor jelszót hozott létre a felhasználói fiókjához, hagyja ki ezt a lépést. Ha csak SSH-kulcshitelesítést használ, és nincs beállítva helyi fiókjelszó, adjon meg egy jelszót, mielőtt az xrdp használatával bejelentkezne a virtuális gépre. Az xrdp nem tudja elfogadni az SSH-kulcsokat a hitelesítéshez. A következő példa az azureuser felhasználói fiók jelszavát adja *meg:*

```bash
sudo passwd azureuser
```

> [!NOTE]
> A jelszó megadása nem frissíti az SSHD-konfigurációt, hogy lehetővé tegye a jelszóbejelentkezéseket, ha jelenleg nem. Biztonsági szempontból előfordulhat, hogy szeretne csatlakozni a virtuális géphez egy SSH-alagút kulcsalapú hitelesítés használatával, majd csatlakozzon az xrdp-hez. Ha igen, hagyja ki a következő lépést a hálózati biztonsági csoportszabály létrehozásához a távoli asztali forgalom engedélyezéséhez.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Hálózati biztonsági csoport szabály létrehozása a Távoli asztali forgalom számára
Ahhoz, hogy a Távoli asztali forgalom elérje a Linux virtuális gép, létre kell hozni egy hálózati biztonsági csoport szabályt, amely lehetővé teszi a TCP a 3389-es porton, hogy elérje a virtuális gép. A hálózati biztonsági csoport szabályairól a [Mi a hálózati biztonsági csoport?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Az [Azure Portal segítségével hálózati biztonsági csoportszabályt](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)is létrehozhat.

A következő példa létrehoz egy hálózati biztonsági csoport szabályt [az az vm nyitott porttal](/cli/azure/vm#az-vm-open-port) a *3389-es*porton. Az Azure CLI-ből, nem az SSH-munkamenetből a virtuális géphez nyissa meg a következő hálózati biztonsági csoportszabályt:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>A Linux os virtuális gép csatlakoztatása távoli asztali ügyféllel
Nyissa meg a helyi távoli asztali klienst, és csatlakozzon a Linux virtuális gép IP-címéhez vagy DNS-nevéhez. Írja be a felhasználói fiók felhasználónevét és jelszavát a virtuális gépen az alábbiak szerint:

![Csatlakozás az xrdp-hez a Távoli asztali ügyfélhasználatával](./media/use-remote-desktop/remote-desktop-client.png)

A hitelesítés után az xfce asztali környezet betöltődik, és a következő példához hasonlóan jelenik meg:

![xfce asztali környezet az xrdp-n keresztül](./media/use-remote-desktop/xfce-desktop-environment.png)

Ha a helyi RDP-ügyfél hálózati szintű hitelesítést (NLA) használ, előfordulhat, hogy le kell tiltania ezt a kapcsolatbeállítást. Az XRDP jelenleg nem támogatja az NLA-t. Alternatív RDP-megoldásokat is megnézhet, amelyek támogatják az NLA-t, például [a FreeRDP-t.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Hibaelhárítás
Ha nem tud csatlakozni a Linux virtuális géphez `netstat` egy távoli asztali kliens használatával, használja a Linux virtuális gép segítségével ellenőrizze, hogy a virtuális gép figyeli-e az RDP-kapcsolatokat az alábbiak szerint:

```bash
sudo netstat -plnt | grep rdp
```

A következő példa a 3389-es TCP-porton figyelő virtuális gép látható a várt módon:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Ha az *xrdp-sesman* szolgáltatás nem figyel, egy Ubuntu vm újraindítása a szolgáltatás az alábbiak szerint:

```bash
sudo service xrdp restart
```

Tekintse át a logokat a */var/log* az Ubuntu VM a jelzések, hogy miért a szolgáltatás nem válaszol. A syslog figyelése távoli asztali kapcsolat során is figyelheti a hibákmegtekintését:

```bash
tail -f /var/log/syslog
```

Más Linux disztribúciók, például a Red Hat Enterprise Linux és a SUSE különböző módon indítható újra a szolgáltatások és a naplófájlok alternatív helyei.

Ha nem kap választ a távoli asztali ügyfél, és nem lát semmilyen eseményt a rendszernaplóban, ez a viselkedés azt jelzi, hogy a távoli asztali forgalom nem éri el a virtuális gép. Tekintse át a hálózati biztonsági csoport szabályait, és győződjön meg arról, hogy rendelkezik a TCP 3389-es porton való engedélyezésére vonatkozó szabállyal. További információt az [Alkalmazáscsatlakozási problémák elhárítása című témakörben talál.](../windows/troubleshoot-app-connection.md)


## <a name="next-steps"></a>További lépések
Az SSH-kulcsok Linuxos virtuális gépeken való létrehozásáról és használatáról további információt az [SSH-kulcsok létrehozása linuxos virtuális gépekhez az Azure-ban című témakörben talál.](mac-create-ssh-keys.md)

Az SSH Windows rendszerből való használatáról az [SSH-kulcsok használata a Windows rendszerben](ssh-from-windows.md)című témakörben talál további információt.

