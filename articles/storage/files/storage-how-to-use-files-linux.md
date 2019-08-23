---
title: Azure Files használata Linux rendszeren | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathat egy Azure-fájlmegosztást az SMB protokollon keresztül a Linuxon.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9674df346ff0b5c046df26fe85bf7ba0bbb802e6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900309"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel

Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztás az [SMB kernel-ügyféllel](https://wiki.samba.org/index.php/LinuxCIFS)is csatlakoztatható Linux-disztribúciókban. Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy Azure-fájlmegosztást: igény `mount` szerint a paranccsal és a rendszerindítással, ha `/etc/fstab`létrehoz egy bejegyzést a alkalmazásban.

> [!NOTE]  
> Az Azure-fájlmegosztás azon Azure-régión kívüli csatlakoztatásához, amely a (z) rendszerben található, például a helyszínen vagy egy másik Azure-régióban, az operációs rendszernek támogatnia kell az SMB 3,0 titkosítási funkcióit.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Azure-fájlmegosztás Linux és a CIFS-utils csomaggal való csatlakoztatásának előfeltételei
<a id="smb-client-reqs"></a>

* **Egy meglévő Azure Storage-fiók és-fájlmegosztás**: A cikk végrehajtásához rendelkeznie kell egy Storage-fiókkal és egy fájlmegosztással. Ha még nem hozott létre egyet, tekintse meg a témával kapcsolatos rövid útmutatók egyikét: [Fájlmegosztás létrehozása –](storage-how-to-use-files-cli.md)parancssori felület.

* **A Storage-fiók neve és kulcsa** A cikk elvégzéséhez szüksége lesz a Storage-fiók nevére és kulcsára. Ha a parancssori felület rövid útmutatójának használatával hozta létre az egyiket, akkor ha nem, akkor a Storage-fiók kulcsainak lekéréséhez tekintse meg a korábban csatolt parancssori felületet.

* **Válasszon egy Linux-disztribúciót a csatlakoztatási igényeknek megfelelően.**  
      A Azure Files az SMB 2,1 és az SMB 3,0 használatával csatlakoztatható. A helyszíni vagy más Azure-régiókban lévő ügyfelektől érkező kapcsolatok esetén az SMB 3,0-et kell használnia; Azure Files elutasítja az SMB 2,1 (vagy az SMB 3,0 titkosítás nélkül). Ha ugyanazon az Azure-régióban található virtuális gépről fér hozzá az Azure-fájlmegosztáshoz, akkor az SMB 2,1 használatával érheti el a fájlmegosztást, ha és csak akkor, ha az Azure-fájlmegosztás üzemeltetéséhez *szükséges biztonságos átvitel* le van tiltva. A biztonságos átvitel megkövetelését és csak az SMB 3,0 titkosítással való használatát javasoljuk.

    Az SMB 3,0 titkosítási támogatása a Linux kernel 4,11-es verziójában lett bevezetve, és a népszerű Linux-disztribúciók régebbi kernel-verzióira backported. A dokumentum közzétételének időpontjában az Azure-katalógus következő eloszlásai támogatják a táblázat fejlécében megadott csatlakoztatási lehetőséget. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>A megfelelő csatlakoztatási képességekkel rendelkező minimális ajánlott verziók (SMB-verzió 2,1 vs SMB-verzió 3,0)

|   | SMB 2.1 <br>(Ugyanazon az Azure-régióban lévő virtuális gépekre csatlakoztatások) | SMB 3.0 <br>(A helyszíni és a régiók közötti) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8+ | 9 +<sup>*</sup> |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3 + |

<sup>*</sup>Szükség lehet egy backported-kernel használatára. A telepítést az `sudo apt-get install linux-image-cloud-amd64` paranccsal tudja végrehajtani.

Ha a Linux-disztribúció nem szerepel a listán, akkor a következő paranccsal tekintheti meg a Linux kernel verzióját:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**A CIFS-utils csomag telepítve van.**  
    A CIFS-utils csomag a kiválasztott Linux-disztribúcióban telepíthető a Package Manager használatával. 

    **Ubuntu** -és **Debian-alapú** disztribúciók esetén használja a `apt-get` Package Managert:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    A **RHEL** és a **CentOS**platformon `yum` használja a Package Managert:

    ```bash
    sudo yum install cifs-utils
    ```

    Az **openSUSE**-ben használja `zypper` a csomagkezelő eszközt:

    ```bash
    sudo zypper install cifs-utils
    ```

    Más disztribúciók esetén használja a megfelelő csomagkezelő vagy [fordítás forrásból elemet](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Döntse el a csatlakoztatott megosztás könyvtárának/fájljának engedélyeit**: Az alábbi példákban az engedély `0777` használatával olvasási, írási és végrehajtási engedélyeket adhat az összes felhasználónak. Szükség szerint lecserélheti más [chmod](https://en.wikipedia.org/wiki/Chmod) -engedélyekkel, de ez potenciálisan korlátozza a hozzáférést. Ha más engedélyeket használ, érdemes megfontolnia a UID és a GID használatát is, hogy megőrizze a hozzáférést az Ön által választott helyi felhasználók és csoportok számára.

> [!NOTE]
> Ha nem ad explicit módon könyvtárat és fájlt a dir_mode és a file_mode, az alapértelmezett érték a 0755.

* **Győződjön meg arról, hogy a 445-es port nyitva van**: Az SMB a 445-ös TCP-porton keresztül kommunikál – ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portot az ügyfél gépéről.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Igény szerinti Azure-fájlmegosztás csatlakoztatása a következővel:`mount`

1. **[Telepítse a CIFS-utils csomagot a Linux](#install-cifs-utils)** -disztribúcióhoz.

1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: A csatlakoztatási ponthoz tartozó mappa bárhol létrehozható a fájlrendszerben, de az általános konvenció egy új mappa létrehozásakor. Például a következő parancs létrehoz egy új könyvtárat, és lecseréli **< storage_account_name >** és **< file_share_name >** a környezetének megfelelő információkkal:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. Az **Azure-fájlmegosztás csatlakoztatásához használja a mount parancsot**: Ne felejtse el helyettesíteni **< storage_account_name >** , **< share_name >** , **< smb_version >** , **< storage_account_key >** és **< mount_point >** a megfelelő információkkal környezet. Ha a Linux-disztribúció támogatja a titkosítást az SMB 3,0-ben (további információért lásd: az [SMB-ügyfélre vonatkozó követelmények ismertetése](#smb-client-reqs) ), használja a **3,0** -es **< smb_version >** . Az SMB 3,0 titkosítást nem támogató Linux-disztribúciók esetén használja a **2,1** **< smb_version >** . Az Azure-fájlmegosztás csak olyan Azure-régión kívül csatlakoztatható (beleértve a helyszíni vagy egy másik Azure-régiót), amely az SMB 3,0-et használja. Ha szeretné, módosíthatja a csatlakoztatott megosztás könyvtár-és fájl-engedélyeit, de ez a hozzáférés korlátozását jelentené.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Ha elkészült az Azure-fájlmegosztás használatával, `sudo umount <mount_point>` a használatával leválaszthatja a megosztást.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Állandó csatlakozási pont létrehozása az Azure-fájlmegosztás számára a következővel:`/etc/fstab`

1. **[Telepítse a CIFS-utils csomagot a Linux](#install-cifs-utils)** -disztribúcióhoz.

1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: A csatlakoztatási ponthoz tartozó mappa bárhol létrehozható a fájlrendszerben, de az általános konvenció egy új mappa létrehozásakor. A létrehozás helyétől függetlenül jegyezze fel a mappa abszolút elérési útját. Például a következő parancs létrehoz egy új könyvtárat, és lecseréli **< storage_account_name >** és **< file_share_name >** a környezetének megfelelő információkkal.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Hozzon létre egy hitelesítőadat-fájlt a fájlmegosztás felhasználónevének (a Storage-fiók neve) és a jelszó (a Storage-fiók kulcsa) tárolásához.** Cserélje le **< storage_account_name >** és **< storage_account_key >** a környezetének megfelelő információkkal.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Módosítsa a hitelesítő fájl engedélyeit úgy, hogy csak a root tudja olvasni vagy módosítani a jelszót tartalmazó fájlt.** Mivel a Storage-fiók kulcsa lényegében egy felügyelői jelszó a Storage-fiókhoz, a fájl engedélyeinek megadásával, hogy csak a gyökér férhet hozzá, fontos, hogy az alacsonyabb jogosultságú felhasználók ne tudják beolvasni a Storage-fiók kulcsát.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. A következő **parancs használatával fűzze hozzá `/etc/fstab`a következő sort** : Ne felejtse el helyettesíteni **< storage_account_name >** , **< share_name >** , **< smb_version >** , és **< mount_point >** a megfelelő információkkal a környezetéhez. Ha a Linux-disztribúció támogatja a titkosítást az SMB 3,0-ben (további információért lásd: az [SMB-ügyfélre vonatkozó követelmények ismertetése](#smb-client-reqs) ), használja a **3,0** -es **< smb_version >** . Az SMB 3,0 titkosítást nem támogató Linux-disztribúciók esetén használja a **2,1** **< smb_version >** . Az Azure-fájlmegosztás csak olyan Azure-régión kívül csatlakoztatható (beleértve a helyszíni vagy egy másik Azure-régiót), amely az SMB 3,0-et használja.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Az újraindítást követően a paranccsal `sudo mount -a` csatlakoztathatja az Azure-fájlmegosztást a Szerkesztés `/etc/fstab` helyett.

## <a name="feedback"></a>Visszajelzés

Linux-felhasználók, szeretnénk hallani!

A Linux-felhasználók csoportjának Azure Files egy olyan fórumot biztosít, amellyel megoszthatja a visszajelzéseket, és elfogadhatja a Linuxon a fájlok tárolását. E-mail [Azure Files Linux-felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozhatnak a felhasználók csoportjához.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás](storage-troubleshoot-linux-file-connection-problems.md)
