---
title: Csatlakoztatási Azure File storage a Linux virtuális gépek az Azure CLI 1.0-s SMB használatával |} Microsoft Docs
description: Azure File storage csatlakoztatásáról a Linux virtuális gépeken SMB használatával
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Csatlakoztatási Azure File storage a Linux virtuális gépek az Azure CLI 1.0-s SMB használatával

Ez a cikk bemutatja, hogyan csatlakoztatása az Azure File storage a Linux virtuális gép a Server Message Block (SMB) protokoll használatával. A File storage biztosít fájlmegosztások szabványos SMB-protokollal a felhőben. Követelmények:

* Egy [Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) nyilvános és titkos kulcs fájlok](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>CLI-verziók használata
A feladat a következő parancssori felület (CLI) verziók egyikével hajthatja végre:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Az Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-a következő generációs CLI erőforrás felügyeleti telepítési modell


## <a name="quick-commands"></a>Gyors parancsok
A feladatnak a gyors, kövesse a jelen szakaszban szereplő lépéseket. Részletes információkat és a környezetben, kezdődjenek a ["Részletes forgatókönyv"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) szakasz.

### <a name="prerequisites"></a>Előfeltételek
* Egy erőforráscsoportot
* Egy Azure virtuális hálózat
* Az SSH a hálózati biztonsági csoportok bejövő
* Egy alhálózaton
* Egy Azure storage-fiók
* Az Azure storage-fiók kulcsok
* Az Azure File storage-megosztás
* Linux virtuális gép

Cserélje le olyan saját beállításaival.

### <a name="create-a-directory-for-the-local-mount"></a>Hozzon létre egy könyvtárat a helyi csatlakoztatási

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>A fájltároló SMB-megosztás a csatlakoztatási pont csatlakoztatása

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Újraindítás után is megmaradjanak a csatlakoztatási
Adja hozzá a következő sort `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

A File storage fájlmegosztások a felhőben, amelyek a szabványos SMB-protokollt biztosít. A legújabb kiadásra frissüljön a File storage is minden os, amely támogatja az SMB 3.0 lehet csatlakoztatni egy fájlmegosztást. Az SMB-csatlakoztatási Linux rendszeren való használatakor egy robusztus, állandó archiválási tárolóhelyre szolgáltatásiszint-szerződésben garantált által támogatott kap egyszerű biztonsági mentést.

Helyezze át a fájlokat egy virtuális gépről a File storage egy üzemeltetett SMB-csatlakoztatási kiváló módja annak, hogy hibakeresési naplókat. Ennek oka az, az SMB-megosztáshoz lehet csatlakoztatni helyileg a Mac, Linux vagy a Windows munkaállomás felé áramlik. Az SMB nem a legjobb megoldás az adatfolyam-Linux vagy alkalmazásnaplók valós időben, mert az SMB protokoll nem lett elkészítve ilyen gyakori naplózási feladatok kezelésére. Dedikált, egységes naplózási réteg eszközhöz hasonló eszközökkel Fluentd jobb megoldás, mint az SMB lenne a Linux és a naplózás a kimeneti alkalmazás gyűjtéséhez.

A részletes forgatókönyv azt létrehozásához először létre kell hoznia a File storage-megosztás szükséges előfeltételeket, és csatlakoztathatja SMB-protokollal a Linux virtuális gép.

1. Egy Azure storage-fiók létrehozása az alábbi kód használatával:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. A tárfiók kulcsait megjelenítése.

    Amikor létrehoz egy tárfiókot, a kulcsait jönnek létre párok, hogy a szolgáltatás megszakítás nélkül forgatható el. Amikor a második kulcsot a párok, hozzon létre új kulcspár. Új tárfiókkulcsok mindig párosával győződjön meg arról, hogy mindig legyen legalább egy nem használt tárkulcsot készen áll a Váltás jönnek létre. A tárfiók kulcsait megjelenítéséhez használja a következő kódot:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. A tárolási fájlmegosztás létrehozása.

    A File storage-megosztás az SMB-megosztás tartalmazza. A kvóta mindig gigabájt (GB) van megadva. A File storage-megosztás létrehozásához használja a következő kódot:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Hozzon létre a csatlakoztatásipont-könyvtárat.

    Az SMB-megosztáson való csatlakoztatásához a Linux fájlrendszer létre kell hoznia egy helyi könyvtárba. Bármi írt, vagy olvassa el a helyi csatlakoztatási könyvtárból továbbíthatja a rendszer az SMB-fájlmegosztást, amely a File storage számára. A következő könyvtár létrehozásakor, a következő kódot használja:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Az SMB-fájlmegosztást csatlakoztatni az alábbi kód használatával:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Az SMB-csatlakoztatási újraindítások keresztül megőrzéséhez.

    A Linux virtuális gép újraindításakor a csatlakoztatott SMB-megosztáson fürtköteteiről leállítás során. Csatlakoztassa újra az SMB-megosztás a rendszerindítás, hozzá kell adnia egy sort a Linux /etc/fstab. Linux fstab fájlt használja, amely a rendszerindítás során csatlakoztatni kell a fájlrendszerek listázásához. Az SMB-megosztás hozzáadása biztosítja, hogy a File storage-megosztást véglegesen csatlakoztatott fájlrendszer a Linux virtuális gép számára. A fájltároló SMB-megosztási ad hozzá egy új virtuális gép felhőalapú inicializálás használata esetén lehetséges.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>További lépések

- [Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása során](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux virtuális gép lemezeinek titkosítani az Azure parancssori felület használatával](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
