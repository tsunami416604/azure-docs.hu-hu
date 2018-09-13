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
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: 4b86749f8a2e68dd5bae11ae440db4d6524e3a91
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713765"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: létrehozása és használata az SSH nyilvános-titkos kulcspárt Linux rendszerű virtuális gépekhez az Azure-ban

Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség jelszavakra a bejelentkezéshez. Ez a cikk bemutatja, hogyan hozhat létre gyorsan és használhatja az SSH nyilvános-titkos kulcspárt Linux rendszerű virtuális gépekhez. Ezeket a lépéseket az Azure Cloud Shellt, macOS vagy Linux gazdagép, a Linux Windows alrendszere és egyéb eszközöket, amelyek támogatják az OpenSSH hajthatja végre. 

> [!NOTE]
> SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint vannak konfigurálva a jelszavak le van tiltva, így nagymértékben növeli a nehézsége kitehetők találgatásos támadások. 

További háttérinformációkért és a példákat lásd: [részletes lépései hozzon létre SSH-kulcspárok](create-ssh-keys-detailed.md).

További lehetőségek a létrehozásához és a egy Windows-számítógép SSH-kulcsok használata, lásd: [az SSH használata a Windows Azure-beli kulcsok](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Használja a `ssh-keygen` parancsot SSH nyilvános és titkos kulcs fájljai. Alapértelmezés szerint ezek a fájlok jönnek létre a ~/.ssh címtárban. Egy másik helyre, és a egy nem kötelező jelszót is megadhat (*jelszót*) a titkos kulcsfájl eléréséhez. Ha ssh-kulcs ezzel a névvel már létezik az adott helyen, ezeket a fájlokat írja felül.

A következő parancsot egy SSH-kulcspár, RSA-titkosítás és 2048 bit hosszúságú használatával hoz létre:

```bash
ssh-keygen -t rsa -b 2048
```

Használatakor a [Azure CLI 2.0](/cli/azure) virtuális Gépen való létrehozásához a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) parancsot, igény szerint hozhat létre SSH nyilvános és titkos kulcs fájlokat a `--generate-ssh-keys` lehetőséget. A kulcsfontosságú fájlokat tárolja a ~/.ssh címtárban, hacsak nincs másképpen megadva az a `--ssh-dest-key-path` lehetőséget. A `--generate-ssh-keys` beállítás nem felülírja a meglévő kulcsfájlok, helyette a hibát adnak vissza. A következő parancsban cserélje le a *VMname* és *RGname* a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Adja meg a nyilvános SSH-kulccsal, egy virtuális géphez

Hozzon létre egy Linux rendszerű virtuális gép által használt SSH-kulcsokat a hitelesítéshez, adja meg a nyilvános SSH-kulcsot, amikor létrehozza a virtuális gép az Azure portal, Azure CLI, Azure Resource Manager-sablonok vagy egyéb módszerek használatával:

* [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Hozzon létre egy Linux rendszerű virtuális gép Azure-sablon használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha még nem ismeri a nyilvános SSH-kulcs formátumát, megjelenítheti a nyilvános kulcsot az alábbi `cat` parancsot, és cserélje le `~/.ssh/id_rsa.pub` elérési útja és fájlneve a saját nyilvános kulcs fájlját, szükség esetén:

```bash
cat ~/.ssh/id_rsa.pub
```

Egy tipikus nyilvános kulcs értékét a következőhöz hasonló:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Ha másolja és illessze be az Azure portal vagy a Resource Manager-sablon használata a nyilvános kulcsfájl tartalmát, ellenőrizze, hogy ne másolja ki a bármilyen záró szóközt. A macOS egy nyilvános kulcs másolásához adatcsatornán keresztül is a nyilvános kulcsfájl **pbcopy**. Ehhez hasonlóan a Linux, adatcsatornán keresztül is a nyilvános kulcs fájlját a programok például **xclip**.

A nyilvános kulcsot, amelyet az Azure-beli Linuxos virtuális gép alapértelmezés szerint van tárolva ~ /.ssh/id_rsa.pub, kivéve, ha a kulcspár létrehozásakor megadott egy másik helyen. Használatához a [Azure CLI 2.0](/cli/azure) szeretne létrehozni a virtuális gép egy meglévő nyilvános kulccsal, adja meg az értéket, és opcionálisan a nyilvános kulcs használatával helyét a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) parancsot a `--ssh-key-value` lehetőséget. A következő parancsban cserélje le a *VMname*, *RGname*, és *keyFile* a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi rendszeren, SSH-t a virtuális IP-címe vagy DNS-neve a virtuális gépet. A következő parancsban cserélje le a *azureuser* és *myvm.westus.cloudapp.azure.com* az rendszergazdájának felhasználóneve és a teljes tartománynevet (vagy IP-cím):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott egy hozzáférési kódot, adja meg a jelszót, amikor a rendszer kéri, a bejelentkezés során. A virtuális gép bekerül a ~/.ssh/known_hosts fájlt, és a rendszer nem kéri újra amíg vagy a nyilvános kulcs az Azure virtuális gépek változásainak csatlakozni, vagy a kiszolgáló neve pedig kikerül a ~/.ssh/known_hosts.

## <a name="next-steps"></a>További lépések

* SSH-kulcspárok való használatáról további információkért lásd: [részletes lépésekkel hozhat létre és kezelhet az SSH-kulcspárok](create-ssh-keys-detailed.md).

* Ha az SSH-kapcsolatok az Azure virtuális gépeire nehézségeket, lásd: [hibaelhárítása SSH kapcsolatok az Azure Linux VM](troubleshoot-ssh-connection.md).
