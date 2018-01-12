---
title: "Az Azure Files használata Linux |} Microsoft Docs"
description: "Ismerje meg, egy Azure fájlmegosztás csatlakoztatásáról SMB-n keresztül Linux rendszeren."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 0a87f8572af2620420faa0e3c2e575aa8add42ab
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure fájlmegosztások a Linux terjesztéseket segítségével lehet csatlakoztatni az [CIFS kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). Ez a cikk bemutatja egy Azure fájlmegosztás csatlakoztatásához kétféleképpen: az igény a `mount` parancs és a rendszerindítási bejegyzés létrehozásával `/etc/fstab`.

> [!NOTE]  
> Ahhoz, hogy csatlakoztassa egy Azure fájlmegosztás az Azure-on kívüli helyezkedik el, például a helyszínen vagy egy másik Azure régióban, az operációs rendszer régió támogatnia kell a titkosítás funkciót, az SMB 3.0. Az SMB 3.0 Linux titkosítási szolgáltatás 4.11 kernel jelent meg. Ez a funkció lehetővé teszi, hogy az Azure fájlmegosztás regisztrációját a helyi vagy egy másik Azure-régiót csatlakoztatását. Közzététel alkalmával Ez a funkció lett backported az Ubuntu 16.04 és fent.

## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Az Azure File csatlakoztatására Prerequisities megosztása a Linux és a cifs-utils csomag
* **Válassza ki, amelyeken a cifs-utils csomag telepítése Linux-disztribúció**: a Microsoft azt javasolja, hogy a kép: Azure-katalógus a következő Linux terjesztéseket:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**A cifs-utils csomag telepítve**: A cifs-utils a package manager segítségével az Ön által választott Linux terjesztési kell telepíteni. 

    A **Ubuntu** és **Debian-alapú** azokat a terjesztéseket, használja a `apt-get` Csomagkezelő:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    A **RHEL** és **CentOS**, használja a `yum` Csomagkezelő:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    A **openSUSE**, használja a `zypper` Csomagkezelő:

    ```
    sudo zypper install samba*
    ```

    A más azokat a terjesztéseket, használja a megfelelő Csomagkezelő vagy [fordítási forrás](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Adja meg a csatlakoztatott megosztást a könyvtár-/ fájlengedélyek**: a következő példa 0777 használjuk, adjon olvasási, írási és végrehajtási engedélyek minden felhasználó számára. Más lecserélheti [chmod engedélyek](https://en.wikipedia.org/wiki/Chmod) igény szerint. 

* **Tárfiók neve**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz a tárfiók nevére.

* **Tárfiók kulcsa**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárkulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, 445-ös port meg nyitva**: SMB - 445-ös TCP-porton keresztül kommunikál ellenőrizze megjelenítéséhez, ha a tűzfal nem blokkolja-e a TCP portokat a 445-ös, az ügyfélszámítógépen.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Az Azure File megosztást az igény a csatlakoztatása`mount`
1. **[A cifs-utils telepítéséhez a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: ehhez bárhol a fájlrendszerben.

    ```
    mkdir mymountpoint
    ```

3. **A csatlakoztatási parancs használata az Azure fájlmegosztás csatlakoztatásához**: ne felejtse el lecserélni `<storage-account-name>`, `<share-name>`, és `<storage-account-key>` a megfelelő adatokkal.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Ha végzett az Azure-fájlmegosztást használja, használhatja a `sudo umount ./mymountpoint` leválasztása a megosztáshoz.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Az Azure fájlmegosztás állandó csatlakoztatási pont létrehozása`/etc/fstab`
1. **[A cifs-utils telepítéséhez a Linux-disztribúció](#install-cifs-utils)**.

2. **Hozzon létre egy mappát a csatlakoztatási pont**: ehhez bárhol a fájlrendszerben, de vegye figyelembe a abszolút mappa elérési útját kell. Az alábbi példa létrehoz egy legfelső szintű mappát.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Az alábbi parancs segítségével az alábbi sor hozzáfűzése `/etc/fstab`** : ne felejtse el lecserélni `<storage-account-name>`, `<share-name>`, és `<storage-account-key>` a megfelelő adatokkal.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs nofail,vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Győződjön meg arról, hogy hozzáadta a `nofail` beállítást a `/etc/fstab` bejegyzést, egyébként pedig a virtuális gép lefagyhat egy helytelen beállítás vagy egyéb hiba esetén a rendszerindítás során az Azure fájlmegosztás csatlakoztatása.

> [!Note]  
> Használhat `sudo mount -a` módosítása után az Azure fájlmegosztás csatlakoztatásához `/etc/fstab` újraindítása helyett.

## <a name="feedback"></a>Visszajelzés
Linux-felhasználók szeretnénk a véleményét!

A Linux-felhasználók csoport Azure fájlok fórumon visszajelzést közös értékelje ki és elfogadják a File storage Linux biztosít. E-mailek [Azure Linux felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozni a felhasználói csoporthoz.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.
* [Referencia a fájlszolgáltatás REST API-jához](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [AzCopy használata a Microsoft Azure storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Az Azure storage az Azure parancssori felület használatával](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [GYIK](../storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-linux-file-connection-problems.md)
