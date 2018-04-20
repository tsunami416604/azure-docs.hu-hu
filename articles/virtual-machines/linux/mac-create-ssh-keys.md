---
title: SSH-kulcspárok létrehozása és használata Linux rendszerű virtuális gépekhez Azure-on | Microsoft Docs
description: Hogyan hozhat létre, és az SSH nyilvános-titkos kulcsból álló kulcspárt használata Linux virtuális gépek Azure-ban a hitelesítési folyamat a biztonság növelése érdekében.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyorsműveletek: létrehozhat és használhat egy SSH nyilvános-titkos kulcsból álló kulcspárt Linux virtuális gépek Azure-ban
Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség jelszavakra a bejelentkezéshez. Ez a cikk bemutatja, hogyan gyorsan hozhat létre, és az SSH nyilvános-titkos kulcsot tartalmazó fájlt pár használhat Linux virtuális gépekhez. Ezeket a lépéseket az Azure-felhő rendszerhéj, egy macOS vagy Linux gazdagép, a Linux Windows alrendszere, és egyéb eszközöket, amely támogatja a protokoll OpenSSH hajthatja végre. 

További háttér és példák: [SSH létrehozásának részletes lépései kulcs párok](create-ssh-keys-detailed.md).

További lehetőségek hozhat létre és használhat SSH-kulcsok Windows rendszerű számítógépeken, lásd: [SSH használata a Windows Azure-kulcsok](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Használja a `ssh-keygen` parancs SSH nyilvános és titkos kulcs fájlok által létrehozott alapértelmezett létrehozásához a `~/.ssh` könyvtár. Megadhat egy másik helyre, és egy további jelszót (a titkos kulcsfájl eléréséhez szükséges jelszót) Ha kéri. Ha egy SSH-kulcspárral megtalálható az aktuális helyen, a rendszer felülírja ezeket a fájlokat.

```bash
ssh-keygen -t rsa -b 2048
```

Ha használja a [Azure CLI 2.0](/cli/azure) a virtuális gép létrehozásához szükség létrehozhat SSH nyilvános és titkos kulcs fájlok futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--generate-ssh-keys` lehetőséget. A kulcsok ~/.ssh könyvtárban tárolják. Vegye figyelembe, hogy az Ez a kapcsoló nem írja felül a kulcsok, ha már léteznek az adott helyre.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>A virtuális gép telepítésekor adja meg a nyilvános SSH-kulcs
Hozzon létre egy Linux virtuális gép SSH-kulcsokat használ, adja meg a nyilvános SSH-kulcsot az Azure-portált használja, CLI, virtuális gép létrehozásakor Resource Manager-sablonok vagy más módszerrel:

* [Linux virtuális gép létrehozása az Azure portállal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha még nem ismeri a nyilvános SSH-kulcs formátuma, megjelenik a nyilvános kulcs futtatásával `cat` következőképpen, cseréje `~/.ssh/id_rsa.pub` a saját nyilvánoskulcs-fájl helye:

```bash
cat ~/.ssh/id_rsa.pub
```

Ha másolja és beilleszti a nyilvános kulcsfájl tartalmát az Azure Portalon vagy egy Resource Manager-sablonban való használathoz, bizonyosodjon meg róla, hogy nem másol felesleges szóközöket. Például macOS használatakor átadható a nyilvános kulcs fájlját (alapértelmezés szerint `~/.ssh/id_rsa.pub`) való **pbcopy** tartalmának (nincsenek más Linux programok, amelyek az ugyanaz, mint például **xclip**).

A nyilvános kulcsot, amelyet a Linux virtuális Gépet az Azure-ban tárolt alapértelmezés szerint ki van `~/.ssh/id_rsa.pub`, kivéve, ha a hely megváltozott, a kulcsok létrehozása után. Használatakor a [Azure CLI 2.0](/cli/azure) a virtuális gép egy meglévő nyilvános kulcs létrehozásához adja meg az értéket, vagy a hely a nyilvános kulcs futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--ssh-key-value` lehetőséget. 

## <a name="ssh-to-your-vm"></a>SSH-kapcsolatot a virtuális gép
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi számítógépen, a virtuális géphez az IP-cím vagy a DNS-neve, a virtuális gép SSH. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a rendszergazdai felhasználónevet és a teljes tartománynév (vagy IP-címet) az alábbi parancs:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott hozzáférési kódot, adja meg a hozzáférési kódot, mikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami a találgatásos támadáson alapuló betörési kísérleteket sokkal drágábbá, ennek megfelelően nehezebbé teszi. 

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a egyszerű SSH-kulcspárral használatának gyors létrehozása. 

* Ha az SSH-kulcspárral használható további segítségre van szüksége, tekintse meg [részletes lépéseket létrehozásához és kezeléséhez az SSH-kulcs párok](create-ssh-keys-detailed.md).

* Ha problémába ütközik az SSH-kapcsolatok egy Azure virtuális gépre, tekintse meg a [hibaelhárítása SSH kapcsolódik az Azure Linux virtuális gép](troubleshoot-ssh-connection.md).


