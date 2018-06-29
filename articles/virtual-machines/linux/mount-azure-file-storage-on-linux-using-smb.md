---
title: A Linux virtuális gépeken, az SMB-csatlakoztatási Azure File storage |} Microsoft Docs
description: A Linux virtuális gépeken SMB használata az Azure CLI Azure File storage csatlakoztatásáról
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
ms.openlocfilehash: 2019324030b2e4c469d0b9ba937fb40a9d0675f1
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099711"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>A Linux virtuális gépeken, az SMB-csatlakoztatási Azure fájltároló


Ez a cikk bemutatja, hogyan használható az Azure File storage szolgáltatást a Linux virtuális gép egy SMB-csatlakoztatási használata az Azure parancssori felület. Az Azure File storage kínál a felhőben, szabványos SMB protokollt használó fájlmegosztások. 

A File storage fájlmegosztások a felhőben, amelyek a szabványos SMB-protokollt biztosít. Az összes operációs rendszer, amely támogatja az SMB 3.0-s fájlmegosztás lehet csatlakoztatni. Az SMB-csatlakoztatási Linux rendszeren való használatakor egy robusztus, állandó archiválási tárolóhelyre szolgáltatásiszint-szerződésben garantált által támogatott kap egyszerű biztonsági mentést.

Helyezze át a fájlokat egy virtuális gépről a File storage egy üzemeltetett SMB-csatlakoztatási kiváló módja annak, hogy hibakeresési naplókat. Az SMB-megosztáshoz helyileg a Mac, Linux vagy a Windows munkaállomás felé áramlik lehet csatlakoztatni. Az SMB nem a legjobb megoldás az adatfolyam-Linux vagy alkalmazásnaplók valós időben, mert az SMB protokoll nem lett elkészítve ilyen gyakori naplózási feladatok kezelésére. Dedikált, egységes naplózási réteg eszközhöz hasonló eszközökkel Fluentd jobb megoldás, mint az SMB lenne a Linux és a naplózás a kimeneti alkalmazás gyűjtéséhez.

Ez az útmutató megköveteli, hogy az Azure parancssori felület 2.0.4 verziót futtat vagy újabb. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot nevű *myResourceGroup* a a *USA keleti régiója* helyét.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy új tárfiókot, a létrehozott, erőforráscsoportban [az storage-fiók létrehozása](/cli/azure/storage/account#create). Ez a példa létrehoz egy nevű tárfiók *mySTORAGEACCT<random number>*  és a storage-fiók nevét a változóban **STORAGEACCT**. Tárolási fiók egyedieknek kell lenniük, használatával `$RANDOM` több hozzáfűzi a célból, amellyel egyedivé teszi.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>A biztonságitár-kulcs beszerzése

Amikor létrehoz egy tárfiókot, a kulcsait jönnek létre párok, hogy a szolgáltatás megszakítás nélkül forgatható el. Amikor a második kulcsot a párok, hozzon létre új kulcspár. Új tárfiókkulcsok mindig létrejönnek párok, így mindig kell legalább egy nem használt tárfiók kulcsa készen áll a váltani.

A tárfiók kulcsait használatával megtekintése [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list). Ebben a példában a kulcs 1 értékét tárolja a **StorageKey TULAJDONSÁGÁT** változó.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Hozzon létre a fájlkiszolgáló tároló megosztás az [az storage-megosztás létrehozása](/cli/azure/storage/share#create). 

A fájlmegosztás neve kell lennie minden kisbetűket, számokat és kötőjeleket egyetlen, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

Ez a példa létrehoz egy megosztást nevű *megosztás* 10-GiB kvótával. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Hozzon létre egy csatlakoztatási pontot

Az Azure fájlmegosztás csatlakoztatása a Linux rendszerű számítógépen, győződjön meg arról, hogy kell a **cifs-utils** csomag telepítve. A telepítési utasításokért lásd: [a cifs-utils telepítéséhez a Linux-disztribúció](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Az Azure Files SMB protokollt használ, amely 445-ös TCP-porton keresztül kommunikál.  Ha gondja csatlakoztatása az Azure-fájlmegosztáshoz, győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös TCP-portot.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>A megosztás csatlakoztatásához

Csatlakoztassa az Azure-megosztás és a helyi címtárszolgáltatásban. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```



## <a name="persist-the-mount"></a>A csatlakoztatási megőrzése

A Linux virtuális gép újraindításakor a csatlakoztatott SMB-megosztáson fürtköteteiről leállítás során. A Linux /etc/fstab vonal felvétele az SMB-megosztáshoz, a rendszerindító csatlakoztatható. Linux fstab fájlt használja, amely a rendszerindítás során csatlakoztatni kell a fájlrendszerek listázásához. Az SMB-megosztás hozzáadása biztosítja, hogy a File storage-megosztást véglegesen csatlakoztatott fájlrendszer a Linux virtuális gép számára. A fájltároló SMB-megosztási ad hozzá egy új virtuális gép felhőalapú inicializálás használata esetén lehetséges.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
A biztonság fokozása érdekében a termelési környezetben tárolja a hitelesítő adatok között az fstab kívül.

## <a name="next-steps"></a>További lépések

- [Felhő inicializálás segítségével testre szabhatja a Linux virtuális gép létrehozása során](using-cloud-init.md)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md)
- [Linux virtuális gép lemezeinek titkosítani az Azure parancssori felület használatával](encrypt-disks.md)

