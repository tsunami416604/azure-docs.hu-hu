---
title: Az Azure Files használata Linuxszal |} A Microsoft Docs
description: 'Útmutató: Azure-fájlmegosztás csatlakoztatása SMB-n keresztül Linux rendszeren.'
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e9363f88db4fa44879eb8f6a6a04e23563c5ba44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125731"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel

Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Azure-fájlmegosztások használatával Linux-disztribúciók csatlakoztathatók a [SMB-kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). Ez a cikk bemutatja az Azure-fájlmegosztások csatlakoztatására kétféleképpen: az igény a `mount` paranccsal, és a rendszerindítási bejegyzés létrehozásával `/etc/fstab`.

> [!NOTE]  
> Annak érdekében, hogy csatlakoztassa Azure-fájlmegosztás, például a helyszínen vagy egy másik Azure-régióban található Azure-régión kívül, az operációs rendszer támogatnia kell az SMB 3.0 titkosítás működésének.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Azure-fájlmegosztások a Linux és a cifs-utils csomag történő csatlakoztatásának előfeltételei
<a id="smb-client-reqs"></a>

* **Meglévő Azure storage-fiók és a fájl megosztást**: Végezze el az ebben a cikkben, egy tárfiókot és egy fájlmegosztás rendelkeznie kell. Ha még nem már létrehozott egyet, ismertető útmutatókkal a Tárgy: [Hozzon létre fájlmegosztást – parancssori felület](storage-how-to-use-files-cli.md).

* **A tárfiók nevét és a kulcs** szüksége lesz a tárfiók neve és kulcsa annak érdekében, hogy ez a cikk befejezéséhez. Ha létre kell hoznia őket a CLI a rövid útmutató egy a, ellenkező esetben tekintse át a parancssori felület a rövid útmutató, amely korábban lett csatolva annak érdekében, hogy ismerje meg, hogyan kérheti le a tárfiók kulcsára.

* **Válasszon egy Linux-disztribúció, a csatlakoztatási igényeinek.**  
      Az Azure Files csatlakoztathatók, vagy az SMB 2.1 és az SMB 3.0-n keresztül. Az ügyfelek számára a helyszíni vagy más Azure-régióban érkező kapcsolatokat az SMB 3.0-s; kell használni Az Azure Files SMB 2.1-es (vagy SMB 3.0 titkosítás nélkül) elutasítja. Ugyanazon Azure-régióban egy virtuális gépről éri el az Azure-fájlmegosztást, akkor előfordulhat, hogy hozzáférhet, ha a fájlmegosztást SMB 2.1, ha és csak akkor, ha, *biztonságos átvitelre van szükség* üzemeltetése az Azure-fájlmegosztást a storage-fiók le van tiltva. Javasoljuk, hogy mindig biztonságos átvitel megkövetelése és használatával csak az SMB 3.0-s titkosítással.

    SMB 3.0 titkosítás támogatása a Linux kernel verziója 4.11 jelent meg, és a népszerű Linux-disztribúciókra vonatkozó régebbi kernel-verzióknál backported lett. Ez a dokumentum közzétételének időpontjában a következő disztribúciók az Azure-katalógusból támogatja a csatlakoztatáshoz szükséges beállítás van megadva a tábla fejléceit az. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Minimális ajánlott verzió a megfelelő csatlakozási képességeit (2.1 vagy SMB 3.0-s verziójú SMB-verzió)

|   | SMB 2.1 <br>(Csatlakoztatása a virtuális gépek ugyanazon Azure-régióban) | SMB 3.0 <br>(Csatlakoztatása a helyszíni, a régiók közötti) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
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

* **A csatlakoztatott megosztást könyvtár engedélyeinek vonatkozó**: A példákban az engedélyt az alábbi `0777` van segítségével adjon olvasási, írási és végrehajtási engedélyek minden felhasználó számára. Más lecserélheti [chmod engedélyek](https://en.wikipedia.org/wiki/Chmod) igény szerint, azonban ez azt jelenti, hogy vélhetően a hozzáférés korlátozása. Ha más engedélyeket használ, érdemes is használ a felhasználó- és csoportazonosító, hogy továbbra is a helyi felhasználók és csoportok tetszőleges.

> [!NOTE]
> Ha explicit módon nem rendel dir_mode és file_mode könyvtárat és fájlnevet engedéllyel, akkor alapértelmezés szerint 0755.

* **Győződjön meg, hogy a 445-ös port nyitva**: Az SMB a 445-ös TCP-porton keresztül kommunikál – ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portot az ügyfél gépéről.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Az Azure-beli fájlmegosztás igény szerinti az csatlakoztatása `mount`

1. **[Telepítse a cifs-utils csomagot a Linux-disztribúció](#install-cifs-utils)** .

1. **Hozzon létre egy mappát a csatlakoztatási pont**: Egy mappát a csatlakoztatási pont hozható létre tetszőleges a fájlrendszerben, de általános egyezmény a alatt egy új mappa létrehozásához. Ha például a következő parancs létrehoz egy új könyvtárat, cserélje le **< tárfiók_neve >** és **< file_share_name >** a környezetének megfelelő információkkal:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Az Azure-fájlmegosztás csatlakoztatása a csatlakoztatási paranccsal**: Ne felejtse el kicserélni **< tárfiók_neve >** , **< megosztás_neve >** , **< smb_version >** , **< storage_account_key >** , és **< mount_point >** a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja SMB 3.0-s titkosítással (lásd: [ismertetése SMB ügyfélkövetelmények](#smb-client-reqs) további információ), használja **3.0** a **< smb_version >** . Linux-disztribúció, amelyek nem támogatják az SMB 3.0-s titkosítással, használja a **2.1** a **< smb_version >** . Azure-fájlmegosztások csak csatlakoztathatók kívül egy Azure-régiót (például a helyszínen vagy más Azure-régióban) az SMB 3.0-s. Ha szeretné módosíthatja a használt csatlakoztatott megosztási engedélyeit könyvtárat és fájlnevet, de ez azt jelentené a hozzáférés korlátozása.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Ha elkészült az Azure-fájlmegosztást használja, előfordulhat, hogy használjon `sudo umount <mount_point>` a fájlmegosztás leválasztásához.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Az Azure-fájlmegosztást az állandó csatlakoztatási pont létrehozása `/etc/fstab`

1. **[Telepítse a cifs-utils csomagot a Linux-disztribúció](#install-cifs-utils)** .

1. **Hozzon létre egy mappát a csatlakoztatási pont**: Egy mappát a csatlakoztatási pont hozható létre tetszőleges a fájlrendszerben, de általános egyezmény a alatt egy új mappa létrehozásához. Bárhol is hoz létre, vegye figyelembe a mappa az abszolút elérési út. Ha például a következő parancs létrehoz egy új könyvtárat, cserélje le **< tárfiók_neve >** és **< file_share_name >** a környezetének megfelelő információkkal.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Hozzon létre egy hitelesítő adatait tartalmazó fájlt a felhasználónevet (a tárfiók neve) és a jelszót (a tárfiók-kulcsot), a fájlmegosztás tárolásához.** Cserélje le **< tárfiók_neve >** és **< storage_account_key >** a környezetének megfelelő információkkal.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **A hitelesítő adatait tartalmazó fájlt engedélyeinek módosítása, így csak a legfelső szintű olvassa el vagy módosítsa a jelszót fájlt is.** Mivel a tárfiók kulcsát lényegében a storage-fiókhoz tartozó felettes rendszergazdai jelszót, az engedélyek beállítása a fájlt úgy, hogy csak a legfelső szintű hozzáférhetnek a fontos, hogy az alacsonyabb jogosultságú felhasználók nem sikerült beolvasni a tárfiókkulcsot.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Fűzze hozzá a következő sort a következő paranccsal `/etc/fstab`** : Ne felejtse el kicserélni **< tárfiók_neve >** , **< megosztás_neve >** , **< smb_version >** , és **< mount_point >** a környezetének megfelelő információkkal. Ha a Linux-disztribúció támogatja SMB 3.0-s titkosítással (lásd: [ismertetése SMB ügyfélkövetelmények](#smb-client-reqs) további információ), használja **3.0** a **< smb_version >** . Linux-disztribúció, amelyek nem támogatják az SMB 3.0-s titkosítással, használja a **2.1** a **< smb_version >** . Azure-fájlmegosztások csak csatlakoztathatók kívül egy Azure-régiót (például a helyszínen vagy más Azure-régióban) az SMB 3.0-s.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Használhat `sudo mount -a` módosítása után az Azure-fájlmegosztás csatlakoztatása `/etc/fstab` újraindítása helyett.

## <a name="feedback"></a>Visszajelzés

Linux-felhasználók szeretnénk a véleményét!

Az Azure Files Linux-felhasználók csoport számára biztosít egy fórumban, hogy ossza meg velünk véleményét, kiértékelheti és elfogadják a File storage linuxon. E-mailek [Azure Files Linux-felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozni a felhasználói csoporthoz.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás](storage-troubleshoot-linux-file-connection-problems.md)
