---
title: Az Azure Blob storage csatlakoztatása fájlrendszerként Linuxon | Microsoft dokumentumok
description: Azure Blob-tároló csatlakoztatása FUSE-val Linuxon
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061430"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Blob storage csatlakoztatása fájlrendszerként blobfuse-szal

## <a name="overview"></a>Áttekintés
[A Blobfuse](https://github.com/Azure/azure-storage-fuse) az Azure Blob storage virtuális fájlrendszer-illesztőprogramja. A Blobfuse lehetővé teszi a meglévő blokkblob-adatok elérését a tárfiókban a Linux fájlrendszeren keresztül. A Blobfuse a virtuális könyvtársémát használja a perjellel "/" határolóként.  

Ez az útmutató bemutatja, hogyan használhatja a blobfuse, és csatlakoztatni egy Blob storage-tároló Linuxon és az adatok eléréséhez. Ha többet szeretne megtudni a blobfuse-ról, olvassa el [a részleteket a blobfuse tárházban.](https://github.com/Azure/azure-storage-fuse)

> [!WARNING]
> A Blobfuse nem garantálja a 100%-os POSIX-megfelelőséget, mivel egyszerűen lefordítja a kérelmeket [a Blob REST API-kba.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) Például az átnevezési műveletek atomi aPOSIX, de nem blobfuse.
> A natív fájlrendszer és a blobfuse közötti különbségek teljes listáját [a blobfuse forráskód tárházában](https://github.com/azure/azure-storage-fuse)keresse fel.
> 

## <a name="install-blobfuse-on-linux"></a>Blobfuse telepítése Linuxra
A Blobfuse bináris fájlok [a Microsoft Szoftvertárházaklinuxon](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) érhetők el Ubuntu és RHEL disztribúciókhoz. Blobfuse telepítéséhez ezeket a disztribúciákat konfigurálja a listából az egyik tárházat. A bináris fájlokat a forráskódból is létrehozhatja az [Azure Storage telepítési lépéseit](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) követve, ha nincsenek bináris fájlok a terjesztéshez.

A Blobfuse támogatja az Ubuntu 14.04, 16.04 és 18.04-es telepítést. Futtassa ezt a parancsot, és győződjön meg arról, hogy a telepített verziók valamelyike telepítve van:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>A Microsoft csomagtár konfigurálása
Konfigurálja a [Microsoft-termékek Linux csomagtárát.](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)

Például egy Enterprise Linux 6 disztribúcióesetén:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Hasonlóképpen módosítsa az `.../rhel/7/...` URL-címet úgy, hogy egy enterprise Linux 7 disztribúcióra mutasson.

Egy másik példa egy Ubuntu 14.04 disztribúcióra:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Hasonlóképpen módosítsa az `.../ubuntu/16.04/...` URL-t egy másik Ubuntu verzióra, vagy `.../ubuntu/18.04/...` hivatkozzon rá.

### <a name="install-blobfuse"></a>Blobfuse telepítése

Ubuntu/Debian disztribúción:
```bash
sudo apt-get install blobfuse
```

Vállalati Linux-disztribúció esetén:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Felkészülés a szerelésre
A Blobfuse natív teljesítményt nyújt azáltal, hogy a fájlrendszerben ideiglenes elérési utat kell megkövetelni a megnyitott fájlok puffereléséhez és gyorsítótárazásához. Ehhez az ideiglenes elérési úthoz válassza ki a legeredményesebb lemezt, vagy használjon ramdisk-et a legjobb teljesítmény érdekében. 

> [!NOTE]
> A Blobfuse az összes megnyitott fájltartalmat az ideiglenes elérési úton tárolja. Győződjön meg arról, hogy elegendő hely áll az összes megnyitott fájl elhelyezésére. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Nem kötelező) Ramdisk használata az ideiglenes elérési úthoz
A következő példa létrehoz egy 16 GB-os ramlemezt és egy könyvtárat a blobfuse számára. Válassza ki a méretet az ön igényeinek megfelelően. Ez a ramdisk lehetővé teszi a blobfuse számára a 16 GB-os méretű fájlok megnyitását. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>SSD használata ideiglenes elérési útként
Az Azure-ban használhatja a rövid élettartamú lemezek (SSD) elérhető a virtuális gépeken, hogy egy kis késésű puffer blobfuse. Az Ubuntu disztribúciókban ez az efemer lemez '/mnt' lemezre van szerelve. A Red Hat és CentOS disztribúciókban a lemez '/mnt/resource/' (A lemez) '/mnt/resource/' meghajtóra van csatlakoztatva.

Győződjön meg arról, hogy a felhasználó hozzáfér az ideiglenes elérési úthoz:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>A tárfiók hitelesítő adatainak konfigurálása
A Blobfuse használatához a hitelesítő adatokat a következő formátumban kell tárolni egy szövegfájlban: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Az `accountName` előtag a tárfiók - nem a teljes URL-t.

Hozza létre ezt a fájlt a következő használatával:

```
touch ~/fuse_connection.cfg
```

Miután létrehozta és szerkesztette ezt a fájlt, győződjön meg arról, hogy korlátozza a hozzáférést, hogy más felhasználók ne olvashassák.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Ha a konfigurációs fájlt Windows rendszeren hozta `dos2unix` létre, győződjön meg arról, hogy a fájl fertőtlenítéséhez és Unix formátumba való konvertálásához fut. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Üres könyvtár létrehozása a csatlakoztatáshoz
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Csatlakoztatás

> [!NOTE]
> A csatlakoztatási beállítások teljes listáját [a blobfuse-tárházban](https://github.com/Azure/azure-storage-fuse#mount-options)ellenőrizze.  
> 

Blobfuse csatlakoztatásához futtassa a következő parancsot a felhasználóval. Ez a parancs a "/path/to/fuse_connection.cfg" sorban megadott tárolót a "/mycontainer" helyre csatlakoztatja.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Most már hozzáférhet a blokkblobokhoz a rendszeres fájlrendszer API-kon keresztül. A címt csatlakoztató felhasználó az egyetlen személy, aki alapértelmezés szerint hozzáférhet a könyvtárhoz, amely biztosítja a hozzáférést. Ahhoz, hogy minden felhasználó számára hozzáférhessen, csatlakoztathatja a kapcsolót. ```-o allow_other``` 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>További lépések

* [Blobfuse kezdőlapja](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Blobfuse-problémák jelentése](https://github.com/Azure/azure-storage-fuse/issues) 

