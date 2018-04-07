---
title: A Linux virtuális gépeken, az SMB-csatlakoztatási Azure File storage |} Microsoft Docs
description: A Linux virtuális gépeken SMB használata az Azure CLI 2.0 Azure File storage csatlakoztatásáról
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
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: de200c9b18b9d27325bcb92e0d27e83ad7c65811
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>A Linux virtuális gépeken, az SMB-csatlakoztatási Azure fájltároló

Ez a cikk bemutatja, hogyan használják a Azure storage szolgáltatás a Linux virtuális gép az SMB-csatlakoztatási használata az Azure CLI 2.0. Az Azure File storage kínál a felhőben, szabványos SMB protokollt használó fájlmegosztások. Az [Azure CLI 1.0-s](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket. Követelmények:

- [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
- [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Gyors parancsok

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
Ehhez adja hozzá a következő sort a `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

A File storage fájlmegosztások a felhőben, amelyek a szabványos SMB-protokollt biztosít. A legújabb kiadásra frissüljön a File storage is minden os, amely támogatja az SMB 3.0 lehet csatlakoztatni egy fájlmegosztást. Az SMB-csatlakoztatási Linux rendszeren való használatakor egy robusztus, állandó archiválási tárolóhelyre szolgáltatásiszint-szerződésben garantált által támogatott kap egyszerű biztonsági mentést.

Helyezze át a fájlokat egy virtuális gépről a File storage egy üzemeltetett SMB-csatlakoztatási kiváló módja annak, hogy hibakeresési naplókat. Ennek oka az, az SMB-megosztáshoz lehet csatlakoztatni helyileg a Mac, Linux vagy a Windows munkaállomás felé áramlik. Az SMB nem a legjobb megoldás az adatfolyam-Linux vagy alkalmazásnaplók valós időben, mert az SMB protokoll nem lett elkészítve ilyen gyakori naplózási feladatok kezelésére. Dedikált, egységes naplózási réteg eszközhöz hasonló eszközökkel Fluentd jobb megoldás, mint az SMB lenne a Linux és a naplózás a kimeneti alkalmazás gyűjtéséhez.

A részletes forgatókönyv azt létrehozásához először létre kell hoznia a File storage-megosztás szükséges előfeltételeket, és csatlakoztathatja SMB-protokollal a Linux virtuális gép.

1. Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create) ahhoz, hogy a fájlmegosztás.

    Nevű erőforráscsoport létrehozásához `myResourceGroup` az "USA nyugati régiója" helyen, használja a következő példát:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Az Azure storage-fiók létrehozása [az storage-fiók létrehozása](/cli/azure/storage/account#az_storage_account_create) a tényleges fájlok tárolásához.

    A Standard_LRS tárolási SKU használatával mystorageaccount nevű tárfiók létrehozásához használja a következő példát:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. A tárfiók kulcsait megjelenítése.

    Amikor létrehoz egy tárfiókot, a kulcsait jönnek létre párok, hogy a szolgáltatás megszakítás nélkül forgatható el. Amikor a második kulcsot a párok, hozzon létre új kulcspár. Új tárfiókkulcsok mindig párosával győződjön meg arról, hogy mindig legyen legalább egy nem használt tárfiók kulcsa készen áll a Váltás jönnek létre.

    A tárfiók kulcsait a megtekintése a [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list). A tárfiók a kulcsok az a megnevezett `mystorageaccount` a következő példában látható:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Egy kulcs kibontásához használja a `--query` jelzőt. Az alábbi példa kibontja az első kulcsát (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. A tárolási fájlmegosztás létrehozása.

    A File storage-megosztás az SMB-megosztás tartalmaz [az storage-megosztás létrehozása](/cli/azure/storage/share#az_storage_share_create). A kvóta mindig gigabájt (GB) van megadva. Az egyik kulcsának fázisra az előző `az storage account keys list` parancsot. Hozzon létre egy megosztást, 10 GB-os kvótával mystorageshare nevű az alábbi példa szerint:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Hozzon létre egy csatlakoztatásipont-könyvtárat.

    Hozzon létre egy helyi könyvtárat a Linux-fájlrendszer, az SMB-megosztáson való csatlakoztatásához. Bármi írt, vagy olvassa el a helyi csatlakoztatási könyvtárból továbbíthatja a rendszer az SMB-fájlmegosztást, amely a File storage számára. Hozzon létre egy helyi könyvtárat, /mnt/mymountdirectory, használja az alábbi példa:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Csatlakoztassa az SMB-megosztáshoz, a helyi könyvtárba.

    Az alábbiak szerint adja meg a saját tárolási fiók felhasználónevét és a tárfiók kulcsa a csatlakoztatási hitelesítő adatait:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Az SMB-csatlakoztatási újraindítások keresztül megőrzéséhez.

    A Linux virtuális gép újraindításakor a csatlakoztatott SMB-megosztáson fürtköteteiről leállítás során. A Linux /etc/fstab vonal felvétele az SMB-megosztáshoz, a rendszerindító csatlakoztatható. Linux fstab fájlt használja, amely a rendszerindítás során csatlakoztatni kell a fájlrendszerek listázásához. Az SMB-megosztás hozzáadása biztosítja, hogy a File storage-megosztást véglegesen csatlakoztatott fájlrendszer a Linux virtuális gép számára. A fájltároló SMB-megosztási ad hozzá egy új virtuális gép felhőalapú inicializálás használata esetén lehetséges.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountdirectory cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>További lépések

- [Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása során](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux virtuális gép lemezeinek titkosítani az Azure parancssori felület használatával](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
