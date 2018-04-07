---
title: Bevezetés az Azure freebsd rendszerű |} Microsoft Docs
description: További információ az Azure-on freebsd rendszerű virtuális gépek használatáról
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
ms.openlocfilehash: 9c7cf223eab3e989436e12c39b122f2aee7619a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-freebsd-on-azure"></a>Az Azure-on freebsd rendszerű bemutatása
Ez a témakör az Azure-ban freebsd rendszerű virtuális gépet futtató áttekintését.

## <a name="overview"></a>Áttekintés
Freebsd rendszerű, a Microsoft Azure egy speciális számítógép operációs rendszerének power modern kiszolgálók, asztali számítógépek, használatával, és a beágyazott platformokon.

A Microsoft Corporation van elérhetővé képeket freebsd rendszerű az Azure szolgáltatásban az a [Azure virtuális gép Vendégügynökének](https://github.com/Azure/WALinuxAgent/) előre konfigurálva van. Jelenleg a következő freebsd rendszerű verziók képként által felajánlott Microsoft:

- Freebsd rendszerű 10.3-kiadás
- Freebsd rendszerű 11.0-kiadás
- Freebsd rendszerű 11.1-kiadás

Az ügynök nem felelős a freebsd rendszerű virtuális gép és a műveletek, például az első használatkor (felhasználónév, jelszó vagy SSH-kulcsot, állomásnév, stb.) a virtuális gép kiépítése, és szelektív Virtuálisgép-bővítmények a funkció engedélyezése az Azure-hálót közötti kommunikációt.

Jövőbeli verzióiban freebsd rendszerű, mint a stratégia mindig naprakész, és lehetővé teszi a legfrissebb verziókban elérhető azok közzététele a freebsd rendszerű kiadás mérnöki csapat követően rövid időn belül.

## <a name="deploying-a-freebsd-virtual-machine"></a>Freebsd rendszerű virtuális gép telepítése
Egy egyszerű folyamatot a lemezkép használatával az Azure-portálon az Azure piactérről freebsd rendszerű virtuális gép telepítése:

- [Az Azure piactéren 10.3 freebsd rendszerű](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [Az Azure piactéren 11.0 freebsd rendszerű](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [Az Azure piactéren 11.1 freebsd rendszerű](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Az Azure CLI 2.0 keresztül freebsd rendszerű virtuális gép létrehozása az freebsd rendszerű
Először telepítenie kell [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , ha a következő parancsot egy freebsd rendszerű gépen.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Ha a bash nincs telepítve a freebsd rendszerű számítógépre, futtassa a következő parancsot a telepítés előtt. 

```bash
sudo pkg install bash
```

Ha a python nincs telepítve a freebsd rendszerű számítógépre, futtassa a következő parancsokat a telepítés előtt. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

A telepítés során a rendszer felkéri `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Válasz `y` , és írja be `/etc/rc.conf` , `a path to an rc file to update`, előfordulhat, hogy megfeleljen a probléma `ERROR: [Errno 13] Permission denied`. A probléma megoldásához, akkor adja meg az írási jogosultság szemben a fájl aktuális felhasználó számára `etc/rc.conf`.

Most már Azure-e jelentkezni, és a freebsd rendszerű virtuális gép létrehozása. Az alábbiakban példája 11.0 freebsd rendszerű virtuális gép létrehozása. Azt is megteheti a paraméter `--public-ip-address-dns-name` egy újonnan létrehozott nyilvános IP-cím globálisan egyedi DNS-név. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ezután bejelentkezhet a freebsd rendszerű virtuális géphez az IP-cím, amelyet a központi telepítés fent kimenetében keresztül. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Freebsd rendszerű Virtuálisgép-bővítmények
Az alábbiakban freebsd rendszerű támogatott Virtuálisgép-bővítmények.

### <a name="vmaccess"></a>VMAccess
A [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) bővítmény is:

* Az eredeti sudo felhasználó jelszavának alaphelyzetbe állítása.
* Hozzon létre egy új felhasználót sudo megadott jelszót.
* Állítsa be a nyilvános állomás kulcsát a megadott kulccsal.
* Alaphelyzetbe állítja a nyilvános állomás kulcsát, ha a gazdagép kulcs nem áll rendelkezésre a virtuális gép kiépítése során megadott.
* Nyissa meg az SSH-port (22-es), és állítsa vissza a sshd_config, ha reset_ssh értéke TRUE.
* Távolítsa el a meglévő felhasználót.
* Ellenőrizze a lemezek.
* Javítsa ki az felvett lemezekről.

### <a name="customscript"></a>CustomScript
A [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) bővítmény is:

* Ha a megadott, le az egyéni parancsfájlok Azure Storage vagy a külső nyilvános tárhelyen (például GitHub).
* A belépési pont parancsprogrammal.
* Támogatja a soron belüli parancsokat.
* Windows-stílusú sortörés a rendszerhéj- és Python parancsfájlok automatikusan konvertálni.
* Távolítsa el azt AJ rendszerhéjat, és a Python parancsfájlok automatikusan.
* CommandToExecute bizalmas adatok védelme érdekében.

> [!NOTE]
> Freebsd rendszerű virtuális gép csak támogatja CustomScript mostanra 1.x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Hitelesítési: felhasználóneveket, jelszavakat és SSH-kulcsok
Freebsd rendszerű virtuális gép létrehozása az Azure portál használatával, meg kell adnia egy felhasználónevet, jelszót vagy nyilvános SSH-kulcsot.
Felhasználónevek Azure freebsd rendszerű virtuális gép üzembe helyezéséhez nem felelhet meg rendszerfiókok nevét (UID < 100) már szerepel a virtuális gép ("Gyökér", például).
Jelenleg csak az RSA SSH-kulcsának használata támogatott. A többsoros SSH-kulcs a következővel kell kezdődnie `---- BEGIN SSH2 PUBLIC KEY ----` és végződhet `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Felügyelő jogosultságokat megszerzésére
A felhasználói fiók az Azure virtuálisgép-példány telepítése során megadott egy rendszerjogosultságú fiókot. A csomag a sudo a közzétett freebsd rendszerű kép telepítése.
Miután jelentkezett be a felhasználói fiókon keresztül, a parancsszintaxis segítségével parancsok futtathatja legfelső szintű.

```
$ sudo <COMMAND>
```

Opcionálisan beszerezhet egy legfelső szintű rendszerhéj használatával `sudo -s`.

## <a name="known-issues"></a>Ismert problémák
A [Azure virtuális gép Vendégügynökének](https://github.com/Azure/WALinuxAgent/) 2.2.2 rendelkezik [ismert probléma] verziója (https://github.com/Azure/WALinuxAgent/pull/517) , ami a kiépítés sikertelen freebsd rendszerű virtuális gép az Azure-on. A javítás által rögzített [Azure virtuális gép Vendégügynökének](https://github.com/Azure/WALinuxAgent/) 2.2.3 verziója és a későbbi kibocsátásokban megtörténik. 

## <a name="next-steps"></a>További lépések
* Ugrás a [Azure piactér](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) freebsd rendszerű virtuális gép létrehozása.
