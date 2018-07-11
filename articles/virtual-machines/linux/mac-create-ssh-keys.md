---
title: SSH-kulcspárok létrehozása és használata Linux rendszerű virtuális gépekhez Azure-on | Microsoft Docs
description: Hogyan hozhat létre és használhat egy SSH nyilvános-titkos kulcspárt Linux rendszerű virtuális gépekhez az Azure-ban a hitelesítési folyamat biztonságának javítása érdekében.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928454"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: létrehozása és használata az SSH nyilvános-titkos kulcspárt Linux rendszerű virtuális gépekhez az Azure-ban
Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség jelszavakra a bejelentkezéshez. Ez a cikk bemutatja, hogyan hozhat létre gyorsan és használhatja az SSH nyilvános-titkos kulcspárt Linux rendszerű virtuális gépekhez. Ezeket a lépéseket az Azure Cloud Shellt, macOS vagy Linux gazdagép, a Linux Windows alrendszere és egyéb eszközöket, amelyek támogatják az OpenSSH hajthatja végre. 

További háttérinformációkért és a példákat lásd: [részletes lépései hozzon létre SSH-kulcspárok](create-ssh-keys-detailed.md).

További lehetőségek a létrehozásához és a egy Windows-számítógép SSH-kulcsok használata, lásd: [az SSH használata a Windows Azure-beli kulcsok](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Használja a `ssh-keygen` parancsot SSH nyilvános és titkos kulcs fájlok létrehozott alapértelmezés szerint a `~/.ssh` könyvtár. Megadhat egy másik helyre, és a egy további hozzáférési kódot (jelszót a titkos kulcsfájl eléréséhez) Ha kéri. Ha ssh-kulcs már létezik az aktuális helyen, ezeket a fájlokat írja felül.

```bash
ssh-keygen -t rsa -b 2048
```

Ha használja a [Azure CLI 2.0](/cli/azure) a virtuális gép létrehozásához, igény szerint létrehozhat SSH nyilvános és titkos kulcs fájljai futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--generate-ssh-keys` lehetőséget. A kulcsok a ~/.ssh címtárban tárolódnak. Vegye figyelembe, hogy az Ez a kapcsoló nem írja felül a kulcsokat, ha azok még léteznek az adott helyen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ha egy virtuális gép üzembe helyezése, adja meg a nyilvános SSH-kulcs
Hozhat létre egy Linux rendszerű virtuális gép által használt SSH-kulcsokat a hitelesítéshez, adja meg a nyilvános SSH-kulcs létrehozásakor a virtuális gép az Azure Portalon, a parancssori felület, Resource Manager-sablonok vagy más módszerekkel:

* [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Hozzon létre egy Linux rendszerű virtuális gép Azure-sablon használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha még nem ismeri a nyilvános SSH-kulcs formátumát, a nyilvános kulcs futtatásával megtekintheti `cat` módon, és cserélje le `~/.ssh/id_rsa.pub` a saját nyilvános kulcsának helyére:

```bash
cat ~/.ssh/id_rsa.pub
```

Ha másolja és beilleszti a nyilvános kulcsfájl tartalmát az Azure Portalon vagy egy Resource Manager-sablonban való használathoz, bizonyosodjon meg róla, hogy nem másol felesleges szóközöket. Például, ha macOS használata esetén adatcsatornán keresztül is a nyilvános kulcs fájlját (alapértelmezés szerint `~/.ssh/id_rsa.pub`) való **pbcopy** tartalmának (nincsenek más Linux programok is ugyanaz, mint például **xclip**).

A nyilvános kulcsot, amelyet az Azure-beli Linuxos virtuális gép alapértelmezés szerint van tárolva `~/.ssh/id_rsa.pub`, nem változtatta meg a helyet, a kulcsok létrehozásakor. Ha használja a [Azure CLI 2.0](/cli/azure) szeretne létrehozni a virtuális gép egy meglévő nyilvános kulccsal, adja meg az értéket, vagy a nyilvános kulcs helyét futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--ssh-key-value` lehetőséget. 

## <a name="ssh-to-your-vm"></a>A virtuális gép ssh-n
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi rendszeren, ssh-KAPCSOLATOT a virtuális IP-címe vagy DNS-neve a virtuális gépet. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a következő parancsban az rendszergazdájának felhasználóneve és a teljes tartománynevet (vagy IP-cím):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott hozzáférési kódot, adja meg a hozzáférési kódot, mikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami a találgatásos támadáson alapuló betörési kísérleteket sokkal drágábbá, ennek megfelelően nehezebbé teszi. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben leírt létrehozása egyszerű SSH-kulcspárral gyors használatra. 

* Ha az SSH-kulcspár dolgozunk további segítségre van szüksége, tekintse meg [részletes lépésekkel hozhat létre és kezelhet az SSH-kulcspárok](create-ssh-keys-detailed.md).

* Ha problémába ütközik az Azure virtuális gép SSH-kapcsolatokat, [hibaelhárítása SSH kapcsolatok az Azure Linux VM](troubleshoot-ssh-connection.md).


