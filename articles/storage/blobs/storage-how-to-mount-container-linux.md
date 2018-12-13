---
title: Hogyan lehet Azure Blob-tároló csatlakoztatása fájlrendszerként Linux rendszeren |} A Microsoft Docs
description: Egy Azure Blob storage-tároló FUSE való csatlakoztatás linuxon
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: seguler
ms.openlocfilehash: 2374875512bba55409ef43906acb20238c77158f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268461"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Blob-tároló csatlakoztatása fájlrendszerként blobfuse az útmutató

## <a name="overview"></a>Áttekintés
[Blobfuse](https://github.com/Azure/azure-storage-fuse) van a virtuális fájlrendszer illesztőprogramja az Azure Blob storage. Blobfuse lehetővé teszi a meglévő blokkblobok adataival a tárfiókban lévő hozzáférnie a Linux-fájlrendszer. Az Azure Blob storage-objektum tárolási szolgáltatás, de nem rendelkezik a hierarchikus névtér. Blobfuse ezt a virtuális könyvtár séma használatával a ferde "/" elválasztóként névteret biztosít.  

Ez az útmutató bemutatja, hogyan blobfuse használja, és a Linux és a hozzáférési adatokat a Blob storage-tároló csatlakoztatható. Blobfuse kapcsolatos további információkért olvassa el a részleteket [a blobfuse tárház](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nem garantálja a 100 %-os POSIX megfelelőségi egyszerűen rendszerhez történő kérelmek [Blob REST API-k](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Ha például átnevezési műveleteket elemiek POSIX, de nem a blobfuse.
> Egy natív fájlrendszer és blobfuse közötti különbségekről teljes listájáért látogasson el [a blobfuse forráskód adattára](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Blobfuse telepítése Linux rendszeren
Blobfuse bináris fájlok elérhetők a [Linux a Microsoft szoftverek tárolóhelyekkel](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) Ubuntun és RHEL disztribúciókat. Blobfuse telepíteni azokat a disztribúciókat, konfigurálja a listából a tárházak egyikét. A bináris fájlokat a forrás-kódblokkot is létrehozható a [Azure Storage telepítési lépéseket](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) Ha nincsenek elérhető a disztribúció nem bináris fájlokat.

Ubuntu 14.04 és 16.04 Blobfuse történő telepítést támogatja. Győződjön meg arról, hogy rendelkezik a telepített verziókat valamelyikével, a következő parancs futtatásával:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>A Microsoft csomagtárház konfigurálása
Konfigurálja a [Linux Csomagtárház Microsoft-termékek](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Tegyük fel, az Enterprise Linux 6 terjesztési:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Hasonlóképpen, módosítsa az URL-címe `.../rhel/7/...` egy Enterprise Linux 7 terjesztési ponthoz.

Egy Ubuntu 14.04 terjesztési egy másik példa:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Hasonlóképpen, módosítsa az URL-címe `.../ubuntu/16.04/...` egy Ubuntu 16.04 terjesztési ponthoz.

### <a name="install-blobfuse"></a>Blobfuse telepítése

Egy Ubuntu/Debian terjesztési:
```bash
sudo apt-get install blobfuse
```

Egy vállalati linuxos:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Csatlakoztatási előkészítése
Blobfuse natív hasonló teljesítményt nyújt a fájlrendszer, a puffer és gyorsítótárazzák a megnyitott fájlokat egy ideiglenes útvonal megkövetelésével. Az ideiglenes elérési út válassza ki a legtöbb nagy teljesítményű lemez vagy egy ramdisk használja a legjobb teljesítmény érdekében. 

> [!NOTE]
> Blobfuse minden megnyitott fájl tartalmát az ideiglenes elérési úton tárolja. Ellenőrizze, hogy nincs elég hely a megnyitott fájlokat befogadásához. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Nem kötelező) Használja a ramdisk ideiglenes elérési útja
A következő példában létrehozunk egy 16 GB-os és a egy könyvtárat a blobfuse ramdisk. Válassza ki a saját igényei szerint méretét. A ramdisk lehetővé teszi, hogy a blobfuse megnyitott fájlok és 16 GB-nál. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>SSD-t használja, mint egy ideiglenes elérési útja
Az Azure-ban rendelkezésre álló ideiglenes lemezek (SSD) használhat a virtuális gépeken blobfuse közel valós idejű puffert biztosít. Az Ubuntu-disztribúció, a rövid élettartamú lemez csatlakoztatva van a "/ mnt". A Red Hat és a CentOS-disztribúció, a lemez csatlakoztatva van a "/ mnt/resource /".

Győződjön meg arról, hogy a felhasználó férhet hozzá az ideiglenes elérési út:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>A tárfiók hitelesítő adatainak konfigurálása
Blobfuse szükséges a hitelesítő adatokat kell tárolni a fájlt a következő formátumban: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

Miután létrehozta ezt a fájlt, ügyeljen arra, hogy korlátozza a hozzáférést, így a többi felhasználó nem tudja olvasni azt.
```bash
chmod 700 fuse_connection.cfg
```

> [!NOTE]
> Ha létrehozta a konfigurációs fájlt a Windows, ügyeljen arra, hogy futtassa `dos2unix` megtisztítása, és a fájl konvertálása Unix formátumban. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Hozzon létre egy üres könyvtárra való csatlakoztatás
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Csatlakoztatás

> [!NOTE]
> Csatlakoztatási beállítások teljes listájáért tekintse [a blobfuse tárház](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Csatlakoztatási blobfuse futtassa a következő parancsot a felhasználóval. Ez a parancs csatlakoztatja a megadott tároló "/ path/to/fuse_connection.cfg" arra a helyre "/ mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

A blokkblobok, a normál API-k fájlrendszeren keresztül, akkor most már elérhetők. A felhasználó, aki a címtárban csatlakoztatja az hozzá tud férni, amely biztonságossá teszi a hozzáférés alapértelmezés szerint egyedül. Ahhoz, hogy a hozzáférést minden felhasználó számára, akkor is csatlakoztathatja a beállítás keresztül ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>További lépések

* [Blobfuse kezdőlapja](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [A jelentés blobfuse kapcsolatos problémák](https://github.com/Azure/azure-storage-fuse/issues) 

