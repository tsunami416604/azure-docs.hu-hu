---
title: "Helyezze át a fájlokat, és az SCP Azure Linux virtuális gépekről |} Microsoft Docs"
description: "Biztonságosan helyezze a fájlokat, és onnan egy Linux virtuális Gépet az Azure-ban a szolgáltatáskapcsolódási pont és egy SSH-kulcspárral."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: 736f7c11ec3de04f1ad52ee29d0a4c952c9b0545
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Helyezze át a fájlokat, és onnan Linux virtuális gépet a szolgáltatáskapcsolódási pont

Ez a cikk bemutatja, hogyan helyezze át a fájlok a munkaállomáson akár az Azure Linux virtuális gép vagy egy Azure Linux virtuális gép le a munkaállomás biztonságos másolása (SCP). Fájlok a munkaállomáson és a Linux virtuális gép közötti áthelyezése, gyorsan és biztonságosan, fontos az Azure-infrastruktúra felügyeletéhez. 

Ez a cikk a Linux virtuális gép üzembe helyezett Azure használatával kell [SSH nyilvános és titkos kulcs fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Szükség egy SCP-ügyfél a helyi számítógépen. SSH épül, és az alapértelmezett rendszerhéjakba legtöbb Linux és Mac számítógépek és az egyes Windows ismertetése szerepel.

## <a name="quick-commands"></a>Gyors parancsok

A Linux virtuális gép legfeljebb fájl másolása

```bash
scp file azureuser@azurehost:directory/targetfile
```

Másolja át a fájlt a Linux virtuális gép le

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Példaként, azt egy Azure-alapú konfigurációs fájl áthelyezése legfeljebb egy Linux virtuális gép és a naplófájl könyvtárának le lekéréses mindkét szolgáltatáskapcsolódási pont és az SSH-kulcsok használatával.   

## <a name="ssh-key-pair-authentication"></a>SSH-kulcspár hitelesítés

A szállítási réteg SSH SCP-t használ. SSH kezeli a hitelesítést a cél gazdagépen, és a fájl egy titkosított csatornán SSH alapértelmezés szerint biztosított áthelyezi azt. SSH hitelesítés a felhasználónevek és jelszavak használható. Ajánlott biztonsági eljárásként azonban SSH nyilvános és titkos kulcsos hitelesítés használata ajánlott. SSH hitelesítette a kapcsolatot, ha a szolgáltatáskapcsolódási pont majd megkezdi a fájl másolása. Használja a megfelelően konfigurált `~/.ssh/config` és az SSH nyilvános és titkos kulcsokat, a szolgáltatáskapcsolódási pont kapcsolat csak a kiszolgáló nevét (vagy IP-cím) segítségével hozhatók létre. Ha csak egy SSH-kulcsot, SCP megkeresi azt a `~/.ssh/` könyvtár, és azok felhasználását alapértelmezés szerint jelentkezzen be a virtuális gép.

Konfigurálásáról további információt a `~/.ssh/config` és az SSH nyilvános és titkos kulcsok, lásd: [hozzon létre SSH-kulcsok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Szolgáltatáskapcsolódási pont a Linux virtuális gép egy fájlt

Az első példában másolja azt egy Azure konfigurációs fájl legfeljebb egy Linux virtuális Gépet, amely az automation telepítéséhez használatos. Ez a fájl tartalmazza az Azure API hitelesítő adatait, amely tartalmazza a titkos kulcsok, mert a biztonsági fontos. A titkosított alagút SSH által biztosított védelmet nyújt a fájl tartalmát.

A következő parancsot, másolja át a helyi *.azure/config* fájl egy Azure virtuális gépre, a teljes tartománynévvel *myserver.eastus.cloudapp.azure.com*. Az Azure virtuális gépen a rendszergazda felhasználónév *azureuser*. A fájl céloz meg a */home/azureuser/* könyvtár. A parancs a saját értékeit helyettesítse.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Szolgáltatáskapcsolódási pont a könyvtárat, a Linux virtuális gépek

Az ebben a példában a Linux virtuális gép le a munkaállomás egy könyvtárat a naplófájlok másolja azt. Egy naplófájlt is, vagy nem tartalmazhatnak bizalmas vagy titkos adatok. Azonban a szolgáltatáskapcsolódási pont biztosítja a naplófájlok tartalmát vannak titkosítva. A legegyszerűbb módja a naplózási könyvtár és a fájlok a munkaállomáson le miközben lehetőség van a biztonságos SCP használata a fájlok átvitele esetén.

A következő parancsot, másolja át a fájlokat a */otthoni/azureuser/logs/* könyvtárhoz, az Azure virtuális gépen, hogy a helyi könyvtárban:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` cli jelzőt a rekurzív módon másolása a fájlok és könyvtárak annak a könyvtárnak a ponttól, szerepel a parancs arra utasítja a szolgáltatáskapcsolódási pont.  Is láthatja, hogy a parancssori szintaxist hasonló egy `cp` -parancs másolásával.

## <a name="next-steps"></a>Következő lépések

* [Kezelheti a felhasználókat, az SSH és az ellenőrzése vagy javítása Azure virtuális gépeken Linux a VMAccess bővítmény használatával lemezek](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)