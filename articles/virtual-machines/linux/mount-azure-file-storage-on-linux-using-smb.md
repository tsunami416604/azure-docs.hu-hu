---
title: Azure-fájltárolás csatlakoztatása Linuxos virtuális gépeken SMB használatával
description: Az Azure File storage csatlakoztatása Linuxos virtuális gépeken az SMB használatával az Azure CLI-vel
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066653"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Azure-fájltárolás csatlakoztatása Linuxos virtuális gépeken SMB használatával

Ez a cikk bemutatja, hogyan használhatja az Azure File storage szolgáltatás linuxos virtuális gép en egy SMB-csatlakoztatás az Azure CLI használatával. Az Azure File storage fájlmegosztásokat kínál a felhőben a szabványos SMB protokoll használatával. 

A fájltárolás a szabványos SMB protokollt használó felhőbeli fájlmegosztásokat kínál. Az SMB 3.0-s operációs rendszert támogató operációs rendszerből csatlakoztathat fájlmegosztást. Ha SMB-csatlakoztatást használ Linuxon, egyszerű biztonsági mentéseket kap egy robusztus, állandó archiválási tárolóhelyre, amelyet egy SLA támogat.

A fájlok virtuális gépről egy SMB-tárolóba való áthelyezése a fájltárolón található fájlok kalkulátora nagyszerű módja a naplók hibakeresésének. Ugyanaz az SMB-megosztás helyileg csatlakoztatható maces, Linuxos vagy Windows-os munkaállomáshoz. Az SMB nem a legjobb megoldás a Linux vagy az alkalmazásnaplók valós idejű streamelésére, mivel az SMB protokoll nem ilyen nehéz naplózási feladatok kezelésére készült. Egy dedikált, egységes naplózási réteg eszköz, mint a Fluentd lenne a jobb választás, mint az SMB gyűjtésére Linux és alkalmazás naplózási kimenet.

Ez az útmutató megköveteli, hogy az Azure CLI 2.0.4-es vagy újabb verzióját futassza. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *USA keleti részén.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy új tárfiókot a létrehozott erőforráscsoporton belül az [az storage fiók létrehozása használatával.](/cli/azure/storage/account) Ebben a példában létrehoz egy *mySTORAGEACCT nevű\<tárfiókot>,* és a storage-fiók nevét a **STORAGEACCT**változóba helyezi. A tárfiókneveknek egyedinek kell lenniük, és egy számot fűznek `$RANDOM` a végéhez, hogy egyedivé tegyék.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>A tárolókulcs beszerezése

Amikor létrehoz egy tárfiókot, a fiókkulcsok párokban jönnek létre, így a szolgáltatás megszakítása nélkül elforgathatók. Amikor a pár második kulcsára vált, új kulcspárt hoz létre. Az új tárfiókkulcsok mindig párokban jönnek létre, így mindig van legalább egy fel nem használt tárfiókkulcs, amely készen áll az átállásra.

A tárfiók kulcsainak megtekintése az [az tárfiókkulcslistájával.](/cli/azure/storage/account/keys) Ez a példa az 1- es kulcs értékét tárolja a **STORAGEKEY** változóban.

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Hozza létre a Fájltárolási megosztást az [az storage share create](/cli/azure/storage/share)használatával. 

A megosztási neveknek kisbetűsnek, számoknak és kötőjelnek kell lenniük, de nem kezdődhetnek kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

Ebben a példában létrehoz egy *share* nevű megosztást 10-GiB kvótával. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Csatlakoztatási pont létrehozása

Az Azure-fájlmegosztás linuxos számítógépre való csatlakoztatásához győződjön meg arról, hogy telepítve van a **CIFS-utils** csomag. A telepítési útmutatót [lásd: A CIFS-utils csomag telepítése a Linux-disztribúcióhoz](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Az Azure Files SMB protokollt használ, amely a 445-ös TCP-porton keresztül kommunikál.  Ha nem sikerül az Azure-fájlmegosztás csatlakoztatása, győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös TCP-portot.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>A megosztás csatlakoztatása

Csatlakoztassa az Azure-fájlmegosztást a helyi címtárba. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

A fenti parancs a [csatlakoztatási](https://linux.die.net/man/8/mount) parancsot használja az Azure fájlmegosztás csatlakoztatására, valamint a [CIF-re jellemző beállításokcsatlakoztatására.](https://linux.die.net/man/8/mount.cifs) Pontosabban, a file_mode és dir_mode beállítások at `0777`fájlok at és könyvtárakat engedély . Az `0777` engedély olvasási, írási és végrehajtási engedélyeket ad az összes felhasználónak. Ezeket az engedélyeket úgy módosíthatja, hogy az értékeket más [chmod engedélyekkel](https://en.wikipedia.org/wiki/Chmod)helyettesíti. Más [CIF-beállításokat](https://linux.die.net/man/8/mount.cifs) is használhat, például a gid-et vagy az uid-t. 


## <a name="persist-the-mount"></a>A csatlakoztatás megőrzése

A Linux virtuális gép újraindításakor a csatlakoztatott SMB-megosztás levan szerelve a leállítás során. Az SMB-megosztás újracsatlakoztatásához adjon hozzá egy sort a Linux /etc/fstab fájlhoz. A Linux az fstab fájlt használja a rendszerindítási folyamat során csatlakoztatni kívánt fájlrendszerek felsorolásához. Az SMB-megosztás hozzáadása biztosítja, hogy a fájltárolási megosztás a Linux virtuális gép véglegesen csatlakoztatott fájlrendszere legyen. A fájltároló SMB-megosztás hozzáadása egy új virtuális géphez a felhőalapú init használata esetén lehetséges.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Az éles környezetek fokozott biztonsága érdekében a hitelesítő adatokat az fstab-on kívül kell tárolnia.

## <a name="next-steps"></a>További lépések

- [Linuxos virtuális gép testreszabása a létrehozás során a felhőalapú init használatával](using-cloud-init.md)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md)
- [Azure lemeztitkosítás Linuxos virtuális gépekhez](disk-encryption-overview.md)

