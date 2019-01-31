---
title: Helyezze át a fájlokat az Azure Linux virtuális gépek, a szolgáltatáskapcsolódási pont |} A Microsoft Docs
description: Biztonságosan áthelyezni a fájlokat, és a egy Linux rendszerű virtuális gép az Azure-ban a szolgáltatáskapcsolódási pont és a egy SSH-kulcspár.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 7d5b2d2ee7e7320fb8bf91c8a62a0f46c403c977
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459553"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Helyezze át a fájlokat, és a egy SCP használata Linux rendszerű virtuális gépekről

Ez a cikk bemutatja, hogyan helyezze át a fájlokat, akár az Azure Linux VM a munkaállomásáról, vagy az Azure Linux VM le a munkaállomáson biztonságos másolás (SCP) használatával. Fájlok áthelyezése a munkaállomáson és a egy Linux rendszerű virtuális gép között, gyorsan és biztonságosan, fontos az Azure-infrastruktúra kezelésére alkalmas. 

Ebben a cikkben egy Linux rendszerű virtuális gép az Azure-ban üzembe helyezett kell [SSH nyilvános és titkos kulcs fájljai](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A helyi számítógépen kell egy SCP-ügyfelet is. SSH-ra épülő, és a legtöbb Linux és Mac számítógépek és a bizonyos Windows parancskörnyezet alapértelmezett bash tartalmazza.

## <a name="quick-commands"></a>Gyors parancsok

A Linux rendszerű virtuális gép legfeljebb fájl másolása

```bash
scp file azureuser@azurehost:directory/targetfile
```

Fájl másolása le a Linux rendszerű virtuális gépekről

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Példa arra, hogy áthelyezése az Azure konfigurációs fájl legfeljebb egy Linux rendszerű virtuális gép és a naplófájl könyvtárának le lekéréses mindkét szolgáltatáskapcsolódási pont és az SSH-kulcsok használatával.   

## <a name="ssh-key-pair-authentication"></a>SSH-kulcspár hitelesítést

Szolgáltatáskapcsolódási pont a szállítási réteg SSH használ. SSH kezeli a hitelesítést, a cél gazdagépen, és áthelyezi azt a fájlt egy titkosított csatornán, alapértelmezés szerint az SSH-n keresztül biztosított. SSH-hitelesítésre szolgáló felhasználónév és jelszó használható. Azonban SSH nyilvános és titkos kulcsos hitelesítés ajánlott legjobb biztonsági megoldásként. Miután SSH hitelesítette a kapcsolatot, az SCP majd megkezdi a fájl másolását. Vagyis a megfelelően konfigurált használatával `~/.ssh/config` és az SSH nyilvános és titkos kulcsokat, a szolgáltatáskapcsolódási pont kapcsolat is létesíthető, csak a kiszolgáló nevét (vagy IP-cím). Ha csak egy SSH-kulcsot, SCP keres a a `~/.ssh/` könyvtárat, és jelentkezzen be a virtuális gép alapértelmezés szerint használ.

Konfigurálásáról további információt a `~/.ssh/config` SSH nyilvános és titkos kulcsokat, és [SSH-kulcsok létrehozása](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Szolgáltatáskapcsolódási pont egy fájlt a Linuxos virtuális gép

Az első példa hogy másolja egy Azure konfigurációs fájlt, amellyel automatizálás telepítése Linux virtuális gép legfeljebb. Ez a fájl tartalmazza az Azure API hitelesítő adatait, amely tartalmazza a titkos kulcsokat, mert a biztonsági fontos. A titkosított csatornán, az SSH által biztosított védelmet nyújt a fájl tartalmát.

A következő parancsot, másolja át a helyi *.azure-/ konfigurációs* fájlt a teljes Tartománynevet az Azure virtuális gép *myserver.eastus.cloudapp.azure.com*. Az Azure virtuális gépen a rendszergazda felhasználónév *azureuser*. A fájl céloz meg, a */home/azureuser/* könyvtár. Helyettesítse be ezt a parancsot a saját értékeire.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Szolgáltatáskapcsolódási pont egy könyvtárat egy Linux rendszerű virtuális gépekről

Ebben a példában a Linux rendszerű virtuális gép le a munkaállomáson a naplófájlokat tartalmazó könyvtár másolja azt. Előfordulhat, hogy egy naplófájlt, vagy előfordulhat, hogy nem tartalmaznak bizalmas vagy titkos adatokat. Azonban a szolgáltatáskapcsolódási pont biztosítja a naplófájlok tartalmát vannak titkosítva. A fájlok átviteléhez szolgáltatáskapcsolódási pont használata esetén a legegyszerűbb módja, miközben lehetőség van a biztonságos a naplózási könyvtár és a fájlok a munkaállomáson le.

A következő parancsot, másolja át a fájlokat a */home/azureuser/logs/* könyvtárat a helyi könyvtárban, az Azure virtuális gépen:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` parancssorifelület-jelző arra utasítja a szolgáltatáskapcsolódási pont rekurzív módon Másolás a fájlokat és könyvtárakat az a pont a könyvtár szerepel a parancsot.  Szintén figyelje meg, hogy a parancssori szintaxist hasonló egy `cp` -parancs másolásával.

## <a name="next-steps"></a>További lépések

* [Kezelheti a felhasználókat, az SSH és ellenőrzést, vagy javítsa ki a lemezeket az Azure Linux rendszerű virtuális gépekhez a VMAccess bővítmény használata](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
