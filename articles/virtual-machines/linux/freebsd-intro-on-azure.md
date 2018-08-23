---
title: Bevezetés a FreeBSD Azure-on |} A Microsoft Docs
description: FreeBSD virtuális gépek használata az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: dfbdc9e3091255267afe6c60363b7f93c4623e02
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058724"
---
# <a name="introduction-to-freebsd-on-azure"></a>Bevezetés a FreeBSD Azure-on
Ez a cikk a FreeBSD virtuális gépek Azure-ban futó áttekintést nyújt.

## <a name="overview"></a>Áttekintés
FreeBSD, a Microsoft Azure egy speciális operációs rendszerhez használt power modern kiszolgálók, asztali számítógépek, és beágyazott platformok.

A Microsoft Corporation van FreeBSD lemezképeket elérhetővé tétele az Azure-ban a [Azure VM-Vendégügynök](https://github.com/Azure/WALinuxAgent/) előre konfigurálva van. Jelenleg a következő FreeBSD verziók képként által kínált Microsoft:

- FreeBSD 10.3-kiadás
- FreeBSD 10.4-kiadás
- FreeBSD 11.1-kiadás

Az ügynök felelős a FreeBSD virtuális gép és a műveleteket, mint az első használatkor (felhasználónév, jelszó vagy SSH-kulcsot, állomásnév, stb.) a virtuális gép kiépítése, és szelektív Virtuálisgép-bővítmények a funkció engedélyezése az Azure-hálót közötti kommunikációt.

FreeBSD jövőbeli verzióiban, mint a stratégia az, hogy legyen naprakész, és adja meg a legújabb kiadásaihoz érhető el azok közzé lettek téve a FreeBSD kiadási mérnöki csapata követően rövid időn belül.

## <a name="deploying-a-freebsd-virtual-machine"></a>FreeBSD virtuális gép üzembe helyezése
FreeBSD virtuális gép üzembe helyezése egy olyan egyszerű folyamat, az Azure Portalról az Azure Marketplace-lemezkép használatával:

- [FreeBSD 10.3 az Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103)
- [FreeBSD 10.4 az Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.1 az Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Az Azure CLI 2.0 – FreeBSD virtuális gép létrehozása a FreeBSD
Először telepítenie kell [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ellenére, hogy a következő parancs a FreeBSD gépen.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Ha a FreeBSD gépen nincs telepítve a bash, futtassa a következő parancs a telepítés előtt. 

```bash
sudo pkg install bash
```

Ha python nincs telepítve, a freebsd rendszerű gépén, futtassa a következő parancsokat a telepítés előtt. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

A telepítés során a rendszer felkéri `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Ha válaszol `y` , és adja meg `/etc/rc.conf` , `a path to an rc file to update`, előfordulhat, hogy megfeleljen a probléma `ERROR: [Errno 13] Permission denied`. A probléma megoldásához, meg kell adjon az írási jogosultságot ellen a fájlt az aktuális felhasználó `etc/rc.conf`.

Most jelentkezzen be az Azure-ba, és a FreeBSD virtuális gép létrehozása. Az alábbiakban látható egy példa FreeBSD 11.0-s virtuális gép létrehozásához. Azt is megteheti a paraméter `--public-ip-address-dns-name` egy újonnan létrehozott nyilvános IP-címek egy globálisan egyedi DNS-névvel. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ezután bejelentkezhet a FreeBSD virtuális gépre, amelyet a fenti telepítési kimenetében az ip-címen keresztül. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>FreeBSD Virtuálisgép-bővítmények
Az alábbiakban a FreeBSD támogatott Virtuálisgép-bővítmények.

### <a name="vmaccess"></a>VMAccess
A [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) bővítmény is:

* Az eredeti sudo felhasználó jelszavának alaphelyzetbe állítása.
* Hozzon létre egy új sudo felhasználó megadott jelszóval.
* Állítsa be a nyilvános állomás kulcsát, a megadott kulccsal.
* Állítsa vissza a virtuális gépek üzembe helyezésekor, ha a gazdagép kulcs nincs megadva, a megadott nyilvános állomás kulcsát.
* Nyissa meg az SSH-port (22), és állítsa vissza az sshd_config reset_ssh értéke igaz.
* Távolítsa el a meglévő felhasználót.
* Ellenőrizze a lemezek.
* Javítsa ki a hozzáadott lemezt.

### <a name="customscript"></a>CustomScript
A [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) bővítmény is:

* Ha meg van adva, töltse le a testre szabott parancsfájlokat az Azure Storage- vagy külső nyilvános tárterület (például a GitHub).
* A futtatni kívánt BelépésiPont-szkriptet.
* Támogatja a beágyazott parancsokat.
* A rendszerhéj- és Python-szkriptek Windows-stílusú soremelés automatikusan konvertálni.
* Távolítsa el AJ rendszerhéj- és Python-szkriptek automatikusan.
* Bizalmas adatok védelme az CommandToExecute.

> [!NOTE]
> FreeBSD virtuális gépek csak a CustomScript verziót támogatja 1.x már.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Hitelesítés: felhasználónevek, jelszavak és az SSH-kulcsok
FreeBSD virtuális gépek az Azure portal használatával hoz létre, amikor meg kell adnia egy felhasználónevet, jelszót vagy nyilvános SSH-kulcsot.
Az Azure-ban FreeBSD virtuális gép üzembe helyezése a felhasználónevek nem egyeznie kell a rendszerfiókok nevével (UID < 100) már jelen van a virtuális gép ("root", a példában).
Jelenleg csak az RSA SSH-kulcs használata támogatott. Többsoros SSH-kulccsal kell kezdődnie `---- BEGIN SSH2 PUBLIC KEY ----` , és `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>SUPERUSER felhasználói jogosultságai beszerzése
Az Azure-beli virtuális gép példány üzembe helyezése során megadott felhasználói fiók a rendszerjogosultságú fiókot. A csomag a sudo a közzétett FreeBSD kép lett telepítve.
Után ezzel a fiókkal jelentkezett be, akkor futtathat parancsokat legfelső szintű használatával a parancs szintaxisát.

```
$ sudo <COMMAND>
```

Igény szerint szerezheti be a legfelső szintű rendszerhéj használatával `sudo -s`.

## <a name="known-issues"></a>Ismert problémák
A [Azure VM-Vendégügynök](https://github.com/Azure/WALinuxAgent/) 2.2.2 rendelkezik [ismert probléma] verziója (https://github.com/Azure/WALinuxAgent/pull/517) , amelynek hatására a kiépítés sikertelen a FreeBSD virtuális géphez az Azure-ban. A javítás verziók által rögzített [Azure VM-Vendégügynök](https://github.com/Azure/WALinuxAgent/) 2.2.3 verzió vagy újabb. 

## <a name="next-steps"></a>További lépések
* Lépjen a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111) FreeBSD virtuális gép létrehozásához.
