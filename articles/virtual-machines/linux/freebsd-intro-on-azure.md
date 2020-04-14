---
title: Bevezetés a FreeBSD Azure-on való használatába
description: Tudjon meg többet a FreeBSD virtuális gépek azure-beli használatáról
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: 0825a29b45ea701315a57ff5248731e64e29de32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261635"
---
# <a name="introduction-to-freebsd-on-azure"></a>Bevezetés a FreeBSD Azure-on való használatába
Ez a cikk áttekintést nyújt a FreeBSD virtuális gépek Azure-beli futtatásáról.

## <a name="overview"></a>Áttekintés
A FreeBSD for Microsoft Azure egy fejlett számítógépes operációs rendszer, amely modern szerverek, asztali számítógépek és beágyazott platformok működtetésére szolgál.

A Microsoft Corporation előre konfigurált [azure-vendégügynökkel](https://github.com/Azure/WALinuxAgent/) teszi elérhetővé a FreeBSD lemezképeit az Azure-ban. Jelenleg a Microsoft a következő FreeBSD verziókat kínálja képként:

- [FreeBSD 10.4 az Azure Piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 az Azure Piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 az Azure Piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

Az ügynök felelős a FreeBSD VM és az Azure-háló közötti kommunikációért olyan műveletek esetén, mint például a virtuális gép első használatkor történő kiépítése (felhasználónév, jelszó vagy SSH-kulcs, állomásnév stb.), valamint a virtuális gép szelektív bővítményei funkcióinak engedélyezése.

Ami a FreeBSD jövőbeli verzióit illeti, a stratégia az, hogy naprakészek maradjanak, és röviddel a FreeBSD kiadásmérnöki csapata által kiadott kiadás után elérhetővé tegyék a legújabb kiadásokat.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>FreeBSD Virtuális gép létrehozása az Azure CLI-n keresztül freeBSD-n
Először telepítenünk kell az [Azure CLI-t,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ha a parancsot követjük freeBSD-gépen.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Ha a bash nincs telepítve a FreeBSD készülékünkre, futtassuk a következő parancsot a telepítés előtt. 

```bash
sudo pkg install bash
```

Ha a Python nincs telepítve a FreeBSD készülékünkre, a telepítés előtt futtassuk a következő parancsokat. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

A telepítés során a `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`rendszer megkérdezi, hogy. Ha `y` válaszol, `/etc/rc.conf` és `a path to an rc file to update`adja meg a `ERROR: [Errno 13] Permission denied`, akkor felel meg a problémát . A probléma megoldásához adja meg az írási jogot `etc/rc.conf`az aktuális felhasználónak a fájllal szemben.

Most bejelentkezhet az Azure-ba, és létrehozhatja FreeBSD virtuális gépét. Az alábbiakban egy példa a FreeBSD 11.0 VM létrehozására. A paramétert `--public-ip-address-dns-name` globálisan egyedi DNS-névvel is hozzáadhatja egy újonnan létrehozott nyilvános IP-címhez. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ezután bejelentkezhetünk a FreeBSD Virtuális Gépünkre azon az IP-címen keresztül, amely a fenti telepítés kimenetében jelent meg. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-bővítmények a FreeBSD-hez
A FreeBSD-ben a következőek támogatott virtuális gépbővítmények.

### <a name="vmaccess"></a>VMAccess
A [VMAccess-bővítmény:](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

* Állítsa alaphelyzetbe az eredeti sudo felhasználó jelszavát.
* Hozzon létre egy új sudo felhasználót a megadott jelszóval.
* Állítsa be a nyilvános állomáskulcsot a megadott kulccsal.
* Állítsa alaphelyzetbe a virtuális gép kiépítése során biztosított nyilvános állomáskulcsot, ha az állomáskulcs nincs megadva.
* Nyissa meg az SSH portot (22), és állítsa vissza a sshd_config, ha reset_ssh igaz értékre van állítva.
* Távolítsa el a meglévő felhasználót.
* Ellenőrizze a lemezeket.
* Hozzáadott lemez javítása.

### <a name="customscript"></a>CustomScript
A [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) bővítmény:

* Ha meg van adva, töltse le a testreszabott parancsfájlokat az Azure Storage-ból vagy a külső nyilvános tárhelyről (például a GitHubról).
* Futtassa a belépési pont parancsfájlt.
* Támogatja a szövegközi parancsokat.
* Windows-stílusú újvonal konvertálása a rendszerhéjban és a Python-parancsfájlokban automatikusan.
* Távolítsa el az anyagjegyzéket és a Python-parancsfájlokat automatikusan.
* Bizalmas adatok védelme a CommandToExecute fájlban.

> [!NOTE]
> A FreeBSD VM mostanra már csak a CustomScript 1.x verzióját támogatja.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Hitelesítés: felhasználónevek, jelszavak és SSH-kulcsok
Amikor az Azure Portalon keresztül hozunk létre FreeBSD virtuális gépet, meg kell adnod egy felhasználónevet, jelszót vagy SSH nyilvános kulcsot.
A FreeBSD virtuális gépek Azure-beli telepítéséhez használt felhasználónevek nem egyeznek meg a virtuális gépben már jelen lévő rendszerfiókok (UID <100) neveivel (például a gyökér).
Jelenleg csak az RSA SSH kulcs támogatott. A többsoros SSH-kulcsnak a-val `---- BEGIN SSH2 PUBLIC KEY ----` kell kezdődnie és végződnie. `---- END SSH2 PUBLIC KEY ----`

## <a name="obtaining-superuser-privileges"></a>Rendszergazdai jogosultságok beszerzése
A virtuálisgép-példány Azure-beli üzembe helyezése során megadott felhasználói fiók egy kiemelt jogosultságú fiók. A sudo csomag a közzétett FreeBSD lemezképbe lett telepítve.
Miután bejelentkezett ezen a felhasználói fiókon keresztül, a parancsokat gyökérként futtathatja a parancs szintaxisával.

```
$ sudo <COMMAND>
```

A használatával tetszés szerint `sudo -s`beszerezhetgyökér-héjat.

## <a name="known-issues"></a>Ismert problémák
Az [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) 2.2.2-es verziójának van egy ismert [problémája,](https://github.com/Azure/WALinuxAgent/pull/517) amely a FreeBSD virtuális gép azure-beli üzembe építési hibáját okozza. A javítást az [Azure VM Vendégügynök](https://github.com/Azure/WALinuxAgent/) 2.2.3-as és újabb verziójú verziói rögzítették. 

## <a name="next-steps"></a>További lépések
* A FreeBSD Virtuális gép létrehozásához látogasson el az [Azure Piactérre.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
