---
title: SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez
description: Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez a hitelesítési folyamat biztonságának javítása érdekében.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 2da8264b7a1d0ad2ec485f106457cef18f233261
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843905"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: nyilvános SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez

A Secure Shell (SSH) kulcspár használatával olyan virtuális gépeket hozhat létre az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség a bejelentkezéshez szükséges jelszavakra. Ebből a cikkből megtudhatja, hogyan hozhat gyorsan elő és használhat nyilvános SSH-kulcspárt Linux rendszerű virtuális gépekhez. Ezeket a lépéseket a Azure Cloud Shell, egy macOS-vagy Linux-gazdagép, a Linux Windows alrendszere és az OpenSSH-t támogató egyéb eszközök segítségével hajthatja végre. 

> [!NOTE]
> Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami jelentősen növeli a találgatásos kitalált támadások nehézségét. 

További hátteret és példákat a következő témakörben talál: [SSH-kulcspár létrehozásának részletes lépései](create-ssh-keys-detailed.md).

Az SSH-kulcsok Windows-számítógépen való létrehozásával és használatával kapcsolatos további lehetőségekért lásd: [az ssh-kulcsok használata az Azure-ban Windowson](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Használja az `ssh-keygen` parancsot az SSH nyilvános és titkos kulcs fájljainak létrehozásához. Alapértelmezés szerint ezek a fájlok a ~/.ssh könyvtárban jönnek létre. Megadhat egy másik helyet és egy opcionális*jelszót (jelszó*) a titkos kulcs fájljának eléréséhez. Ha a megadott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

Az alábbi parancs egy SSH-kulcspárt hoz létre RSA-titkosítással és 4096-es hosszúságú használatával:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Ha az [Azure CLI](/cli/azure) -vel hozza létre a virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal, lehetősége van az SSH nyilvános és titkos kulcs fájljainak létrehozására az `--generate-ssh-keys` lehetőség használatával. A kulcs fájljait a ~/.ssh könyvtárban tárolja a rendszer, hacsak másként nincs megadva a `--ssh-dest-key-path` kapcsoló. A `--generate-ssh-keys` lehetőség nem írja felül a létező kulcsokat, hanem hibát ad vissza. A következő parancsban cserélje le a *VMname* és a *RGname* értéket a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Nyilvános SSH-kulcs megadása virtuális gép telepítésekor

SSH-kulcsokat használó Linux rendszerű virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát a virtuális gép létrehozásakor a Azure Portal, az Azure CLI, a Azure Resource Manager sablonok vagy más módszerek használatával:

* [Linuxos virtuális gép létrehozása a Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos virtuális gép létrehozása Azure-sablon használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha nem ismeri a nyilvános SSH-kulcs formátumát, a következő `cat` paranccsal jelenítheti meg a nyilvános kulcsot, és szükség esetén lecserélheti a `~/.ssh/id_rsa.pub`t a saját nyilvános kulcsfájl elérési útjára és fájlnevére:

```bash
cat ~/.ssh/id_rsa.pub
```

Egy tipikus nyilvánoskulcs-érték a következő példához hasonlóan néz ki:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Ha a Azure Portal vagy egy Resource Manager-sablonban használni kívánt nyilvánoskulcs-fájl tartalmát másolja és illeszti be, ügyeljen arra, hogy ne másolja a záró szóközöket. Nyilvános kulcs macOS-ben történő másolásához a nyilvános kulcs fájlját `pbcopy`ba. A Linux rendszerben hasonlóan a nyilvános kulcsfájl is átadható olyan programoknak, mint például a `xclip`.

A Linux rendszerű virtuális gépen az Azure-ban tárolt nyilvános kulcs alapértelmezés szerint a ~/.ssh/id_rsa. pub helyen található, hacsak nem adott meg másik helyet a kulcspár létrehozásakor. Ha az [Azure CLI 2,0](/cli/azure) használatával szeretné létrehozni a virtuális gépet egy meglévő nyilvános kulccsal, adja meg az értéket és opcionálisan a nyilvános kulcs helyét az az [VM create](/cli/azure/vm#az-vm-create) paranccsal a `--ssh-key-value` kapcsolóval. A következő parancsban cserélje le a *VMname*, a *RGname*és a *keyFile* értéket a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Ha több SSH-kulcsot szeretne használni a virtuális géppel, megadhatja azokat szóközzel tagolt listában, például a `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`.


## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren lévő titkos kulccsal, SSH-val a virtuális gépre a virtuális gép IP-címét vagy DNS-nevét használva. A következő parancsban cserélje le az *azureuser* és a *myvm.westus.cloudapp.Azure.com* nevet a rendszergazdai felhasználónévvel és a teljes TARTOMÁNYNÉVVEL (vagy IP-címmel):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor jelszót adott meg, írja be a jelszót, amikor a rendszer a bejelentkezési folyamat során kéri. A virtuális gép hozzá van adva a ~/.ssh/known_hosts-fájlhoz, és a rendszer nem kéri újra a kapcsolódást, amíg az Azure-beli virtuális gép nyilvános kulcsa nem változik, vagy a kiszolgáló neve el lesz távolítva a ~/.ssh/known_hostsból.

Ha a virtuális gép az igény szerinti hozzáférési szabályzatot használja, a virtuális géphez való kapcsolódáshoz a hozzáférést kell kérnie. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Következő lépések

* Az SSH-kulcspár használatával kapcsolatos további információkért tekintse meg az [SSH-kulcspár létrehozásának és kezelésének részletes lépéseit](create-ssh-keys-detailed.md).

* Ha problémái vannak az Azure-beli virtuális gépek SSH-kapcsolataival, tekintse meg az [SSH-kapcsolatok Azure Linux rendszerű virtuális géppel való megoldásával kapcsolatos](troubleshoot-ssh-connection.md)problémát
