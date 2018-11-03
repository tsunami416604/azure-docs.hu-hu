---
title: Azure File storage csatlakoztatása az SMB-vel Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Hogyan csatlakoztathatja az Azure File storage SMB-vel az Azure CLI-vel Linux rendszerű virtuális gépeken
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: fde8892f7a32d7b5405eef6661bbf29098325178
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958678"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Azure File storage csatlakoztatása az SMB-, Linux rendszerű virtuális gépeken

Ez a cikk bemutatja, hogyan használható az Azure File storage szolgáltatást egy Linux rendszerű virtuális gépen egy SMB-csatlakoztatási használatával az Azure CLI használatával. Az Azure File storage kínál a felhőben, a szabványos SMB protokollt használó fájlmegosztások. 

A File storage biztosít a fájlmegosztások a felhőben, amelyek a szabványos SMB-protokollt használják. Az összes operációs rendszer, amely támogatja az SMB 3.0-s fájlmegosztás lehet csatlakoztatni. Egy SMB-csatlakoztatás Linux rendszeren való használatakor egyszerű biztonsági mentések kap egy robusztus, állandó archiválási tárolóhelyre szolgáltatásiszint-szerződés által támogatott.

Fájlokat helyez át egy virtuális Gépet egy SMB-csatlakoztatási üzemeltetett, File Storage kiválóan alkalmas lehet hibákat keresni a naplókban. Az SMB-megosztáson helyileg is csatlakoztathatók a Mac, Linux vagy Windows-munkaállomás. Az SMB nem a legjobb megoldás Linux streameléshez vagy alkalmazásnaplók valós időben, mert az SMB protokoll nem épített ilyen gyakori naplózási feladatok kezelésére. Egy dedikált, egyesített naplózási réteg eszköz Fluentd például SMB-nál jobb választás lenne Linux és a naplózás a kimeneti alkalmazás összegyűjtése.

Ez az útmutató megköveteli, hogy futtat-e az Azure CLI 2.0.4-es vagy újabb. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot *myResourceGroup* a a *USA keleti Régiójában* helyét.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új storage-fiókot az erőforráscsoportban létrehozott, [az tárfiók létrehozása](/cli/azure/storage/account#create). Ez a példa létrehoz egy tárfiókot, nevű *mySTORAGEACCT<random number>*  és a tárfiók nevét a változóban **STORAGEACCT**. Tárfiókok nevének egyedinek kell lennie, használatával `$RANDOM` egy számot fűz a végfelhasználók egyedi legyen.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>A storage-kulcs lekérése

Amikor létrehoz egy tárfiókot, a fiókkulcsok címpárral jönnek létre, hogy a szolgáltatás megszakítás nélkül forgatható el. Amikor a párok a második kulcsot, hozzon létre egy új kulcspárt. Új tárfiókkulcsok mindig létrehozása párok, így mindig kell legalább egy nem használt tárfiókkulcs készen áll a váltás.

A tárfiók kulcsaihoz használatával megtekintheti [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list). Ebben a példában a kulcs 1 értékét tárolja a **StorageKey TULAJDONSÁGÁT** változó.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Hozzon létre a File storage használatával [az tárolási fájlmegosztás létrehozása](/cli/azure/storage/share#create). 

Megosztás nevének kell lennie az összes kisbetűket, számokat és kötőjeleket, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

Ez a példa létrehoz egy megosztásban *myshare* egy 10 GiB-kvótával. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Hozzon létre egy csatlakoztatási pont

Az Azure-fájlmegosztás csatlakoztatása a Linux rendszerű számítógépen, ellenőrizze, hogy kell a **cifs-utils** csomag telepítve van. A telepítési utasításokért lásd: [telepítse a cifs-utils csomagot a Linux-disztribúció](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Az Azure Files SMB protokollt használ, amely a TCP 445-ös porton keresztül kommunikál.  Ha problémába ütközik az Azure-fájlmegosztás csatlakoztatása, győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös port.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>A megosztás csatlakoztatásához

Csatlakoztassa az Azure-fájlmegosztás a helyi könyvtárba. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

A fenti parancs használja a [csatlakoztatási](https://linux.die.net/man/8/mount) parancs segítségével csatlakoztassa az Azure-fájlmegosztást és a jellemző beállításokat [cifs](https://linux.die.net/man/8/mount.cifs). Pontosabban, a file_mode és dir_mode beállítások set-fájlok és könyvtárak engedélyt `0777`. A `0777` engedélyt biztosít olvasási, írási és végrehajtási engedélyek minden felhasználó számára. Ezek az engedélyek módosításához az értékeket cserélje le a más [chmod engedélyek](https://en.wikipedia.org/wiki/Chmod). Is használhatja más [cifs](https://linux.die.net/man/8/mount.cifs) beállítások, például a csoportazonosító vagy egyedi azonosítója. 


## <a name="persist-the-mount"></a>A csatlakoztatási megőrzése

A Linux rendszerű virtuális gép újraindításakor a csatlakoztatott SMB-megosztáson fürtköteteiről leállítása közben. Újból csatlakoztatni az SMB-megosztás a rendszerindítás, a Linux /etc/fstab, adjon hozzá egy sort. Linux az fstab fájlt használja, a rendszerindítási folyamat során csatlakoztatni kell a fájlrendszerek listában. Az SMB-megosztás hozzáadása biztosítja, hogy a File storage-megosztást véglegesen csatlakoztatott fájlrendszer a Linux rendszerű virtuális gép számára. A File storage SMB-megosztás hozzáadása egy új virtuális Gépet, akkor lehetséges, ha a cloud-init használata.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Az éles környezetben fokozott biztonság érdekében tárolja a hitelesítő adatok fstab-en kívül.

## <a name="next-steps"></a>További lépések

- [A cloud-init használatával testre szabhatja a Linux rendszerű virtuális gép létrehozása során](using-cloud-init.md)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md)
- [Az Azure CLI használatával egy Linux rendszerű virtuális Gépen található lemezek titkosítása](encrypt-disks.md)

