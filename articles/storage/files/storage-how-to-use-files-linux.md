---
title: Az Azure Files használata Linuxszal |} A Microsoft Docs
description: 'Útmutató: Azure-fájlmegosztás csatlakoztatása SMB-n keresztül Linux rendszeren.'
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 4b844fe50623782f23c1819c14eb7626eb9506cf
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614946"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Azure-fájlmegosztások használatával Linux-disztribúciók csatlakoztathatók a [SMB-kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). Ez a cikk bemutatja az Azure-fájlmegosztások csatlakoztatására kétféleképpen: az igény a `mount` paranccsal, és a rendszerindítási bejegyzés létrehozásával `/etc/fstab`.

> [!NOTE]  
> Annak érdekében, hogy csatlakoztassa Azure-fájlmegosztás, például a helyszínen vagy egy másik Azure-régióban található Azure-régión kívül, az operációs rendszer támogatnia kell az SMB 3.0 titkosítás működésének.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Azure-fájlmegosztások a Linux és a cifs-utils csomag történő csatlakoztatásának előfeltételei
<a id="smb-client-reqs"></a>
* **Válasszon egy Linux-disztribúció, a csatlakoztatási igényeinek.**  
      Az Azure Files csatlakoztathatók, vagy az SMB 2.1 és az SMB 3.0-n keresztül. Az ügyfelek számára a helyszíni vagy más Azure-régióban érkező kapcsolatokat az Azure Files visszautasítja az SMB 2.1-es (vagy SMB 3.0 titkosítás nélkül). Ha *biztonságos átvitelre van szükség* engedélyezve van egy storage-fiókok az Azure Files csak akkor engedélyezi a kapcsolatok használatával az SMB 3.0-s titkosítással.
    
    SMB 3.0 titkosítás támogatása a Linux kernel verziója 4.11 jelent meg, és a népszerű Linux-disztribúciókra vonatkozó régebbi kernel-verzióknál backported lett. Ez a dokumentum közzétételének időpontjában a következő disztribúciók az Azure-katalógusból támogatja a csatlakoztatáshoz szükséges beállítás van megadva a tábla fejléceit az. 

* **Minimális ajánlott verzió a megfelelő csatlakozási képességeit (2.1 vagy SMB 3.0-s verziójú SMB-verzió)**    
    
    |   | SMB 2.1 <br>(Csatlakoztatása a virtuális gépek ugyanazon Azure-régióban) | SMB 3.0 <br>(Csatlakoztatása a helyszíni, a régiók közötti) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04-es + | 16.04 + |
    | RHEL | 7 + | 7.5 + |
    | CentOS | 7 + |  7.5 + |
    | Debian | 8 + |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |
    
    Ha a Linux-disztribúció nem szerepel itt, akkor ellenőrizheti, hogy a Linux kernel verziója a következő paranccsal:    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**A cifs-utils csomag telepítve van.**  
    A cifs-utils csomag is telepíthető a használata a Csomagkezelőt a Linux-disztribúció. 

    A **Ubuntu** és **Debian-alapú** disztribúciók, használja a `apt-get` Csomagkezelő:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    A **RHEL** és **CentOS**, használja a `yum` Csomagkezelő:

    ```bash
    sudo yum install cifs-utils
    ```

    A **openSUSE**, használja a `zypper` Csomagkezelő:

    ```bash
    sudo zypper install cifs-utils
    ```

    Más disztribúciókon, használja a megfelelő Csomagkezelő vagy [összeállítása forrásból](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)
    
* **A csatlakoztatott megosztást könyvtár engedélyeinek vonatkozó**: az engedélyt az alábbi példákban `0777` van segítségével adjon olvasási, írási és végrehajtási engedélyek minden felhasználó számára. Más lecserélheti [chmod engedélyek](https://en.wikipedia.org/wiki/Chmod) igény szerint. 

* **Tárfiók neve**: Azure-fájlmegosztások csatlakoztatásához szüksége a tárfiók nevére.

* **Tárfiók kulcsa**: Azure-fájlmegosztások csatlakoztatásához szüksége az elsődleges (vagy másodlagos) tárkulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg, hogy a 445-ös port nyitva**: SMB - 445-ös TCP-porton keresztül kommunikál ellenőrizze, tekintse meg, ha a tűzfal nem blokkolja a TCP portokat a 445-ös, az ügyfél gépéről.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Az Azure-beli fájlmegosztás igény szerinti az csatlakoztatása `mount`
1. **[Telepítse a cifs-utils csomagot a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: egy mappát a csatlakoztatási pont hozható létre tetszőleges a fájlrendszerben, de általános egyezmény a alatt létrehozásához a `/mnt` mappát. Példa:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Az Azure-fájlmegosztás csatlakoztatása a csatlakoztatási paranccsal**: ne felejtse el kicserélni `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, és `<mount-point>` a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja SMB 3.0-s titkosítással (lásd: [ismertetése SMB ügyfélkövetelmények](#smb-client-reqs) további információ), használja `3.0` a `<smb-version>`. Linux-disztribúció, amelyek nem támogatják az SMB 3.0-s titkosítással, használja a `2.1` a `<smb-version>`. Vegye figyelembe, hogy Azure-fájlmegosztások csak csatlakoztathatók kívül egy Azure-régiót (például a helyszínen vagy más Azure-régióban) az SMB 3.0-s. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Ha elkészült az Azure-fájlmegosztást használja, előfordulhat, hogy használjon `sudo umount <mount-point>` a fájlmegosztás leválasztásához.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Az Azure-fájlmegosztást az állandó csatlakoztatási pont létrehozása `/etc/fstab`
1. **[Telepítse a cifs-utils csomagot a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: egy mappát a csatlakoztatási pont hozható létre tetszőleges a fájlrendszerben, de általános egyezmény a alatt létrehozásához a `/mnt` mappát. Bárhol is hoz létre, vegye figyelembe a mappa az abszolút elérési út. Ha például az a következő parancs létrehoz egy új mappát `/mnt` (az elérési út abszolút elérési utat).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Hozzon létre egy hitelesítő adatait tartalmazó fájlt a felhasználónevet (a tárfiók neve) és a jelszót (a tárfiók-kulcsot), a fájlmegosztás tárolásához.** Ne felejtse el kicserélni `<storage-account-name>` és `<storage-account-key>` a környezetének megfelelő információkkal. 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **A hitelesítő adatait tartalmazó fájlt engedélyeinek módosítása, így csak a legfelső szintű olvassa el vagy módosítsa a jelszót fájlt is.** Mivel a tárfiók kulcsát lényegében a storage-fiókhoz tartozó felettes rendszergazdai jelszót, az engedélyek beállítása a fájlt úgy, hogy csak a legfelső szintű hozzáférhetnek a fontos, hogy az alacsonyabb jogosultságú felhasználók nem sikerült beolvasni a tárfiókkulcsot.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Fűzze hozzá a következő sort a következő paranccsal `/etc/fstab`** : ne felejtse el kicserélni `<storage-account-name>`, `<share-name>`, `<smb-version>`, és `<mount-point>` a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja SMB 3.0-s titkosítással (lásd: [ismertetése SMB ügyfélkövetelmények](#smb-client-reqs) további információ), használja `3.0` a `<smb-version>`. Linux-disztribúció, amelyek nem támogatják az SMB 3.0-s titkosítással, használja a `2.1` a `<smb-version>`. Vegye figyelembe, hogy Azure-fájlmegosztások csak csatlakoztathatók kívül egy Azure-régiót (például a helyszínen vagy más Azure-régióban) az SMB 3.0-s. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Használhat `sudo mount -a` módosítása után az Azure-fájlmegosztás csatlakoztatása `/etc/fstab` újraindítása helyett.

## <a name="feedback"></a>Visszajelzés
Linux-felhasználók szeretnénk a véleményét!

Az Azure Files Linux-felhasználók csoport számára biztosít egy fórumban, hogy ossza meg velünk véleményét, kiértékelheti és elfogadják a File storage linuxon. E-mailek [Azure Files Linux-felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozni a felhasználói csoporthoz.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.
* [Bevezetés az Azure Files használatába](storage-files-introduction.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Gyakori kérdések](../storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-linux-file-connection-problems.md)
