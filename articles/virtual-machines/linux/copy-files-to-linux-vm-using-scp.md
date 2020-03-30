---
title: Fájlok áthelyezése Az Azure Linux-alapú virtuális gépekre és az SCP-vel
description: Biztonságosan áthelyezi a fájlokat egy Linux virtuális gépre az Azure-ban scp és egy SSH kulcspár használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969603"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Fájlok áthelyezése Linux os virtuális gépre scp használatával

Ez a cikk bemutatja, hogyan helyezhetát át a fájlokat a munkaállomásról egy Azure Linux virtuális gépre, vagy egy Azure Linux virtuális gépről a munkaállomásra a biztonságos másolás (SCP) használatával. A munkaállomás és a Linux-alapú virtuális gép közötti fájlok gyors és biztonságos áthelyezése elengedhetetlen az Azure-infrastruktúra kezeléséhez. 

Ebben a cikkben az [Azure-ban sSH nyilvános és titkos kulcsfájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával üzembe helyezett Linux virtuális gépre van szükség. A helyi számítógéphez is szüksége van egy SCP-ügyfélre. Az SSH tetejére épül, és a legtöbb Linux és Mac számítógép alapértelmezett Bash héjában és néhány Windows rendszerhéjban szerepel.

## <a name="quick-commands"></a>Gyors parancsok

Fájl másolása a Linux virtuális gépre

```bash
scp file azureuser@azurehost:directory/targetfile
```

Fájl másolása le a Linux virtuális gépről

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Példaként egy Azure-konfigurációs fájlt egy Linux virtuális gépre helyezünk át, és lehúzunk egy naplófájl-címtárat, mind Az SCP, mind az SSH-kulcsok használatával.   

## <a name="ssh-key-pair-authentication"></a>SSH kulcspár-hitelesítés

Az SCP ssh-t használ a szállítási réteghez. Az SSH kezeli a hitelesítést a célállomáson, és a fájlt egy titkosított alagútban helyezi át, amely alapértelmezés szerint az SSH-val van ellátva. Az SSH-hitelesítéshez felhasználónevek és jelszavak használhatók. Az SSH nyilvános és személyes kulcsos hitelesítése azonban biztonsági ajánlott eljárásként ajánlott. Miután az SSH hitelesítette a kapcsolatot, az SCP megkezdi a fájl másolását. A megfelelően `~/.ssh/config` konfigurált és SSH nyilvános és személyes kulcsok használatával az SCP-kapcsolat csak egy kiszolgálónév (vagy IP-cím) használatával jön létre. Ha csak egy SSH-kulccsal rendelkezik, az SCP megkeresi azt a `~/.ssh/` címtárban, és alapértelmezés szerint azt használja a virtuális gépbe való bejelentkezéshez.

Az SSH nyilvános `~/.ssh/config` és személyes kulcsainak konfigurálásáról az [SSH-kulcsok létrehozása című](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)témakörben talál további információt.

## <a name="scp-a-file-to-a-linux-vm"></a>SCP egy fájlt egy Linux vm

Az első példában egy Azure-konfigurációs fájlt másolunk egy Linux virtuális gépre, amely az automatizálás üzembe helyezéséhez szolgál. Mivel ez a fájl tartalmazza az Azure API hitelesítő adatait, amelyek titkos kulcsokat tartalmaznak, a biztonság fontos. Az SSH által biztosított titkosított alagút védi a fájl tartalmát.

A következő parancs a helyi *.azure/config* fájlt egy Azure-beli virtuális gépbe másolja fqdn *myserver.eastus.cloudapp.azure.com.* Az Azure virtuális gép felügyeleti felhasználóneve az *azureuser.* A fájl a */home/azureuser/* könyvtárba kerül. A parancsban a saját értékek et helyettesítheti.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP egy könyvtár egy Linux virtuális gépről

Ebben a példában a naplófájlok könyvtárát a Linux virtuális gépről a munkaállomásra másoljuk. A naplófájl bizalmas vagy titkos adatokat tartalmazhat, vagy nem. Az SCP használata azonban biztosítja a naplófájlok titkosítását. Az SCP használata a fájlok átviteléhez a legegyszerűbb módja annak, hogy a naplókönyvtárat és a fájlokat levigye a munkaállomásra, miközben biztonságos is.

A következő parancs az Azure VM */home/azureuser/logs/* könyvtárában lévő fájlokat másolja a helyi /tmp könyvtárba:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` jelző arra utasítja az SCP-t, hogy rekurzívan másolja a fájlokat és könyvtárakat a parancsban felsorolt könyvtár pontjáról.  Figyelje meg azt is, hogy a `cp` parancssori szintaxis hasonló a másolási parancshoz.

## <a name="next-steps"></a>További lépések

* [Felhasználók, SSH- és lemezei kezelése és javítása az Azure Linux virtuális gépeken a VMAccess-bővítmény használatával](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
