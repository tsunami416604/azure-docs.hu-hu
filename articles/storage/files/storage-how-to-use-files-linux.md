---
title: Az Azure Files használata Linux |} Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatása az Azure fájlmegosztások SMB-n keresztül Linux rendszeren.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.openlocfilehash: 667f385e4f157a5e1b9fcaf47b25619eafa8e9e3
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738244"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure fájlmegosztások a Linux terjesztéseket segítségével lehet csatlakoztatni az [SMB kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). Ez a cikk bemutatja az Azure fájlmegosztások csatlakoztatása kétféleképpen: az igény a `mount` parancs és a rendszerindítási bejegyzés létrehozásával `/etc/fstab`.

> [!NOTE]  
> Ahhoz, hogy csatlakoztatása az Azure fájlmegosztások kívül az Azure-régió, például a helyszínen vagy a különböző Azure-régiót helyezkedik el, az operációs rendszer támogatnia kell a titkosítás funkciót, az SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>A Linux és a cifs-utils csomagot az Azure fájlmegosztások csatlakoztatása előfeltételei
* **Válasszon ki egy Linux-disztribúció, amelyeken a cifs-utils csomag telepítve.**  
    A következő Linux terjesztésekről állnak rendelkezésre az Azure katalógusában:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8 +
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**A cifs-utils csomag telepítve van.**  
    A cifs-utils csomag a package manager segítségével az Ön által választott Linux terjesztési is telepíthetők. 

    A **Ubuntu** és **Debian-alapú** azokat a terjesztéseket, használja a `apt-get` Csomagkezelő:

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

    A más azokat a terjesztéseket, használja a megfelelő Csomagkezelő vagy [fordítási forrás](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* <a id="smb-client-reqs"></a>**SMB-ügyfél követelmények megértése érdekében.**  
    Az Azure Files SMB 2.1 és az SMB 3.0 keresztül lehet csatlakoztatni. Az ügyfelek a helyszíni vagy más Azure-régiókban érkező kapcsolatokat Azure fájlok elutasítják az SMB 2.1-es (vagy SMB 3.0-titkosítás nélkül). Ha *szükséges átviteli biztonságos* engedélyezve van a tárfiókon, Azure fájlokat fogja kapcsolatok engedélyezése csak SMB 3.0 protokollal a titkosítás.
    
    Az SMB 3.0-titkosítás támogatása Linux kernel verzió 4.11 bemutatott és backported a népszerű Linux terjesztésekről régebbi kernel verziókra. Ez a dokumentum közzétételének időpontjában az Azure katalógusában a következő terjesztéseket támogatja ezt a szolgáltatást:

    - Ubuntu Server 16.04 +
    - openSUSE 42.3 +
    - SUSE Linux Enterprise Server 12 SP3 +
    
    A Linux-disztribúció itt nem látható, ha azt is ellenőrizze, hogy a Linux kernel verzió a következő paranccsal:

    ```bash
    uname -r
    ```

* **Adja meg a csatlakoztatott megosztást a könyvtár-/ fájlengedélyek**: az engedély az alábbi példákban `0777` van használ a rekordhoz olvasási, írási és végrehajtási engedélyek minden felhasználó számára. Más lecserélheti [chmod engedélyek](https://en.wikipedia.org/wiki/Chmod) igény szerint. 

* **A tárfiók neve**: az Azure fájlmegosztások csatlakoztatni kell a tárfiók nevét.

* **Tárfiók kulcsa**: az Azure fájlmegosztások csatlakoztatni kell az elsődleges (vagy a másodlagos) kulcsot. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, 445-ös port meg nyitva**: SMB - 445-ös TCP-porton keresztül kommunikál ellenőrizze megjelenítéséhez, ha a tűzfal nem blokkolja-e a TCP portokat a 445-ös, az ügyfélszámítógépen.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>A fájl Azure fájlmegosztás az igény a csatlakoztatása `mount`
1. **[A cifs-utils telepítéséhez a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: egy mappát egy csatlakoztatási pontot bárhol is létrehozható a fájlrendszerben, de a létrehozás menetét az általános egyezmény a `/mnt` mappát. Példa:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **A csatlakoztatási parancs használata Azure fájlmegosztás csatlakoztatásához**: ne felejtse el lecserélni `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, és `<mount-point>` a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja az SMB 3.0 titkosítással (lásd: [megértéséhez SMB ügyfélkövetelmények](#smb-client-reqs) további információt), használja `3.0` a `<smb-version>`. A Linux terjesztéseket, amelyek nem támogatják az SMB 3.0 a titkosítás, használjon `2.1` a `<smb-version>`. Vegye figyelembe, hogy az Azure fájlmegosztások csak lehet csatlakoztatni az Azure-régiót kívül (beleértve a helyi vagy egy másik Azure-régió) az SMB 3.0-s. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Ha végzett Azure fájlmegosztás használja, használhatja a `sudo umount <mount-point>` leválasztása a megosztáshoz.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Az Azure fájlmegosztás állandó csatlakoztatási pont létrehozása `/etc/fstab`
1. **[A cifs-utils telepítéséhez a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: egy mappát egy csatlakoztatási pontot bárhol is létrehozható a fájlrendszerben, de a létrehozás menetét az általános egyezmény a `/mnt` mappát. Bárhol hoz létre, vegye figyelembe a abszolút mappa elérési útját. Például a következő parancs létrehoz egy új mappát `/mnt` (az elérési út abszolút elérési utat).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Hozzon létre egy hitelesítő adatait tartalmazó fájlt (a tárfiók neve) felhasználónevét és jelszavát (a tárfiók hívóbetűjét) a fájlmegosztás tárolásához.** Ne felejtse el lecserélni `<storage-account-name>` és `<storage-account-key>` a környezetének megfelelő információkkal. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **A hitelesítő adatok fájlját engedélyeinek módosítása, így csak legfelső szintű olvashassák el vagy módosítsa a jelszót fájlt.** Mivel a tárfiók hívóbetűjét alapvetően a tárfiók felettes rendszergazdai jelszót, az engedélyek beállítása a férhetnek hozzá, hogy csak a legfelső szintű fájl fontos, hogy alacsonyabb jogosultsággal rendelkező felhasználók nem lehet beolvasni a tárfiók kulcsára.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Az alábbi parancs segítségével az alábbi sor hozzáfűzése `/etc/fstab`** : ne felejtse el lecserélni `<storage-account-name>`, `<share-name>`, `<smb-version>`, és `<mount-point>` a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja az SMB 3.0 titkosítással (lásd: [megértéséhez SMB ügyfélkövetelmények](#smb-client-reqs) további információt), használja `3.0` a `<smb-version>`. A Linux terjesztéseket, amelyek nem támogatják az SMB 3.0 a titkosítás, használjon `2.1` a `<smb-version>`. Vegye figyelembe, hogy az Azure fájlmegosztások csak lehet csatlakoztatni az Azure-régiót kívül (beleértve a helyi vagy egy másik Azure-régió) az SMB 3.0-s. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-key>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Használhat `sudo mount -a` szerkesztését követően Azure fájlmegosztás csatlakoztatásához `/etc/fstab` újraindítása helyett.

## <a name="feedback"></a>Visszajelzés
Linux-felhasználók szeretnénk a véleményét!

A Linux-felhasználók csoport Azure fájlok fórumon visszajelzést közös értékelje ki és elfogadják a File storage Linux biztosít. E-mailek [Azure Linux felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozni a felhasználói csoporthoz.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.
* [Az Azure Files bemutatása](storage-files-introduction.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [GYIK](../storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-linux-file-connection-problems.md)