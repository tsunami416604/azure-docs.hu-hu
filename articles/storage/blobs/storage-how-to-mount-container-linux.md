---
title: "Azure Blob Storage tárolóban csatlakoztatásáról a fájlrendszer Linux |} Microsoft Docs"
description: "Csatlakoztassa az Azure Blob storage tárolók biztosító rendelkező Linux"
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>A Blob storage csatlakoztatásáról a fájlrendszer a blobfuse (előzetes verzió)

## <a name="overview"></a>Áttekintés
[Blobfuse](https://github.com/Azure/azure-storage-fuse) egy virtuális fájlrendszer-illesztőprogram van az Azure Blob-tároló, amely lehetővé teszi a meglévő blokk blob adatait a tárfiók eléréséhez a Linux fájlrendszeren keresztül. Az Azure Blob Storage egy olyan objektum tárolási szolgáltatás, és így nem kell a hierarchikus névtér. Ez a virtuális könyvtár séma használatával ferde a '/' elválasztó használatával névteret biztosít a Blobfuse.  

Ez az útmutató bemutatja, hogyan blobfuse használja, és a Linux és a hozzáférési adatok egy Blob storage tárolót csatlakoztatható. Blobfuse kapcsolatos további tudnivalókért olvassa el a részleteket [blobfuse összetevőtárházat](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nem garantálja a 100 %-os POSIX megfelelőség egyszerűen rendszerhez történő kérelmek [Blob REST API-k](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Például nevezze át olyan POSIX, de nem a blobfuse atomi.
> A fájlrendszer és a blobfuse közötti különbségek teljes listájának megtekintéséhez keresse fel a [a blobfuse forráskódraktárban](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Blobfuse telepítése Linux rendszerre
Blobfuse bináris érhetők el a [a Microsoft szoftverek tárolóhelyekkel Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Blobfuse telepítéséhez konfigurálja ezeket a tárolóhelyekkel egyikét.

### <a name="configure-the-microsoft-package-repository"></a>A Microsoft csomag adattár konfigurálása
Konfigurálja a [Linux csomag tárház a Microsoft-termékek](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Tegyük fel, az Enterprise Linux 6 terjesztési:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Hasonlóképpen, módosítsa az URL-címét a `.../rhel/7/...` egy Enterprise Linux 7 terjesztési ponthoz.

Egy másik példa a egy Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Hasonlóképpen, módosítsa az URL-címét a `.../ubuntu/16.04/...` egy Ubuntu 16.04 terjesztési ponthoz.

### <a name="install-blobfuse"></a>Blobfuse telepítése

Egy Ubuntu/Debian terjesztési:
```bash
sudo apt-get install blobfuse
```

Az Enterprise Linux terjesztési:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Készítse elő a csatlakoztatása
Blobfuse ideiglenes elérési útvonalat kell megadni a fájlrendszer puffer és gyorsítótárazza a megnyitott fájlokat, amely segít a natív hasonló teljesítményt biztosít. Az ideiglenes mappa útvonalát válassza ki a legtöbb performant lemezt, vagy a ramdisk használjon a legjobb teljesítmény érdekében. 

> [!NOTE]
> Blobfuse minden megnyitott fájl tartalmát tárolja az ideiglenes mappa útvonalát. Ügyeljen arra, hogy elegendő lemezterület rendelkeznie ahhoz, hogy megfeleljen a megnyitott fájlokat. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Választható) A ramdisk ideiglenes mappa útvonalát használja
Az alábbi példakód létrehozza a ramdisk 16 GB, valamint a blobfuse könyvtár létrehozása. Válassza ki a igényei szerint méretét. A ramdisk lehetővé teszi, hogy a blobfuse megnyitott fájlok legfeljebb 16 GB-nál. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Az ideiglenes mappa útvonalát egy SSD használata
Az Azure segítségével a rövid élettartamú meghajtón (SSD) érhető el a virtuális gépeken adja meg a kis késleltetésű puffer blobfuse. Ubuntu azokat a terjesztéseket, a rövid élettartamú lemez van csatlakoztatva: "/ mnt" mivel csatlakoztatva van a "/ mnt/erőforrás /" a RedHat és CentOS terjesztéseket.

Győződjön meg arról, a felhasználó rendelkezik hozzáférési jogosultsággal az ideiglenes mappa útvonalát:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>A Tárfiók hitelesítő adatainak konfigurálása
Blobfuse szükséges a hitelesítő adatok tárolását a fájlt a következő formátumban: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Ha létrehozta ezt a fájlt, győződjön meg arról, korlátozhatja a hozzáférést, ezért nincs más felhasználói tudják elolvasni.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Hozzon létre egy üres könyvtárat csatlakoztatása
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>csatlakoztatása

> [!NOTE]
> A csatlakoztatási lehetőségek teljes listájáért tekintse meg [blobfuse összetevőtárházat](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Ahhoz, hogy csatlakoztatási blobfuse futtassa a következő parancsot a felhasználóval. Ez a parancs a megadott tárolóhoz csatlakoztatja "/ path/to/fuse_connection.cfg", a helye "/ mycontainer".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Hozzáférés a blokkblobok a rendszeres fájlrendszerrel API-k most kell. Vegye figyelembe, hogy a csatlakoztatott könyvtár csak elérhető csatlakoztatni, a felhasználó, amely biztosítja a hozzáférést. Ha azt szeretné, hogy minden felhasználó hozzáférést, akkor is csatlakoztathatja a beállítás keresztül ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>További lépések

* [Blobfuse kezdőlap](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [A jelentés blobfuse problémák](https://github.com/Azure/azure-storage-fuse/issues) 

