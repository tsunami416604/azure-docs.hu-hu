---
title: Azure-fájlok használata Linuxalatt | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathat egy Azure-fájlmegosztást SMB-n keresztül Linuxon.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72264755d5f0379f0ffb07852f48885126a36898
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411607"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure fájlmegosztások linuxos disztribúciókban csatlakoztathatók az [SMB kernelügyfél](https://wiki.samba.org/index.php/LinuxCIFS)használatával. Ez a cikk az Azure-fájlmegosztás csatlakoztatásának `mount` két módját mutatja be: igény `/etc/fstab`szerinti a paranccsal és az indításkor egy bejegyzés létrehozásával a rendszerben.

Az Azure-fájlmegosztás Linuxon történő csatlakoztatásának ajánlott módja az SMB 3.0 használata. Alapértelmezés szerint az Azure Files titkosítást igényel az átvitel során, amelyet csak az SMB 3.0 támogat. Az Azure Files támogatja az SMB 2.1-es, amely nem támogatja a titkosítást az átvitel során, de biztonsági okokból nem csatlakoztathatja az Azure-fájlmegosztásokat SMB 2.1-es smb 2.1-es sel egy másik Azure-régióból vagy a helyszínen. Hacsak az alkalmazás kifejezetten nem igényel SMB 2.1, kevés ok a használatára, mivel a legnépszerűbb, nemrég megjelent Linux disztribúciók támogatják az SMB 3.0-t:  

| | SMB 2.1 <br>(Csatlakoztatások virtuális gépeken ugyanazon Az Azure-régión belül) | SMB 3.0 <br>(A telephelyről és a régióközi helyekről származó csatolók) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13,2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Ha olyan Linux disztribúciót használ, amely nem szerepel a fenti táblázatban, ellenőrizheti, hogy a Linux disztribúció támogatja-e az SMB 3.0 titkosítást a Linux kernel verziójának ellenőrzésével. A titkosítással ellátott SMB 3.0 a Linux kernel 4.11-es verziójához lett hozzáadva. A `uname` parancs visszaadja a használatban lévő Linux kernel verzióját:

```bash
uname -r
```

## <a name="prerequisites"></a>Előfeltételek
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Győződjön meg arról, hogy a CIFs-utils csomag telepítve van.**  
    A CIFs-utils csomag telepíthető a csomagkezelő vel az Ön által választott Linux disztribúción. 

    **Ubuntu** és **Debian alapú** disztribúciók esetén használja a `apt` csomagkezelőt:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    A **Fedorán**, **a Red Hat Enterprise Linux 8+** és a **CentOS 8 +** készüléken használja a `dnf` csomagkezelőt:

    ```bash
    sudo dnf install cifs-utils
    ```

    A Red **Hat Enterprise Linux** és **centOS**régebbi verzióiban használja a `yum` csomagkezelőt:

    ```bash
    sudo yum install cifs-utils 
    ```

    **OpenSUSE**esetén használja `zypper` a csomagkezelőt:

    ```bash
    sudo zypper install cifs-utils
    ```

    Más disztribúciókesetén használja a megfelelő csomagkezelőt vagy [a forrásból történő fordítást](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Az Azure parancssori felület (CLI) legújabb verziója.** Az Azure CLI telepítéséről az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az operációs rendszer kiválasztása című témakörben talál további információt. Ha az Azure PowerShell-modult szeretné használni a PowerShell 6+-ban, azonban az alábbi utasítások az Azure CLI-hez kerülnek bemutatásra.

* **Győződjön meg arról, hogy a 445-ös port nyitva van:** Az SMB a 445-ös TCP-porton keresztül kommunikál – ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portokat az ügyfélgépről.  Az **erőforráscsoport><** lecserélése és a **tárfiók<>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Ha a kapcsolat sikeres volt, a következő kimenethez hasonló tetszőnek kell lennie:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Ha nem tudja megnyitni a 445-ös portot a vállalati hálózaton, vagy egy isp le van tiltva, a 445-ös port megkerüléséhez VPN-kapcsolatot vagy ExpressRoute-kapcsolatot használhat. További információ: [Hálózati szempontok a közvetlen Azure-fájlmegosztási hozzáférés ..](storage-files-networking-overview.md)

## <a name="mounting-azure-file-share"></a>Azure-fájlmegosztás felszerelése
Az Azure-fájlmegosztás a Linux-disztribúció használatával, létre kell hoznia egy könyvtárat, amely az Azure-fájlmegosztás csatlakoztatási pontjaként szolgál. A csatlakoztatási pont bárhol létrehozható a Linux rendszeren, de gyakori konvenció, hogy ezt az /mnt alatt hozza létre. A csatlakoztatási pont `mount` után a paranccsal érheti el az Azure-fájlmegosztást.

Szükség esetén csatlakoztassa ugyanazt az Azure-fájlmegosztást több csatlakoztatási ponthoz.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Az Azure-fájlmegosztás igény szerinti csatlakoztatása`mount`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz:** Cserélje le `<your-resource-group>`a , `<your-storage-account>`és `<your-file-share>` a környezetének megfelelő információkkal:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Az Azure-fájlmegosztás csatlakoztatása a csatlakoztatási paranccsal csatlakoztathatja.** Az alábbi példában a helyi Linux fájl- és mappaengedélyek alapértelmezett 0755- ös értékével rendelkeznek, ami azt jelenti, hogy a tulajdonos (a fájl/könyvtár Linux tulajdonosa alapján) olvasása és végrehajtása a tulajdonoscsoport felhasználói számára olvasása és végrehajtása, valamint a rendszer többi tagja számára olvasása és végrehajtása. A `uid` csatlakoztatási `gid` beállításokkal beállíthatja a csatlakoztatás felhasználói azonosítóját és csoportazonosítóját. Egyéni engedélyeket `dir_mode` `file_mode` is használhat és igény szerint beállíthat. Az engedélyek beállításáról a Wikipédia [UNIX numerikus jelölése](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) című témakörben talál további információt. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > A fenti csatlakoztatási parancs SMB 3.0-s csatlakoztatása. Ha a Linux-disztribúció nem támogatja az SMB 3.0 titkosítással, vagy ha csak az SMB 2.1-es, csak csatlakoztathatja egy Azure virtuális gép ugyanabban a régióban, mint a tárfiók. Az Azure-fájlmegosztás titkosítással nem támogatott SMB 3.0-s szolgáltatásra való csatlakoztatásához le kell [tiltania a titkosítást a tárfiók átvitele során.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Ha befejezte az Azure-fájlmegosztás használatát, `sudo umount $mntPath` leveheti a megosztást.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Állandó csatlakoztatási pont létrehozása az Azure-fájlmegosztáshoz`/etc/fstab`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: A csatlakoztatási pont mappája bárhol létrehozható a fájlrendszeren, de az /mnt alatt gyakori a létrehozás. A következő parancs például létrehoz egy `<your-resource-group>` `<your-storage-account>`új `<your-file-share>` könyvtárat, a , a , és a környezetének megfelelő információkkal:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Hozzon létre egy hitelesítő adatfájlt a fájlmegosztás felhasználónevének (a tárfiók nevének) és jelszavának (a tárfiók kulcsának) tárolására.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Módosítsa a hitelesítő adatok fájljára vonatkozó engedélyeket, hogy csak a gyökér tudja olvasni vagy módosítani a jelszófájlt.** Mivel a tárfiók kulcsa lényegében egy rendszergazdai jelszó a tárfiókhoz, fontos, hogy a fájl engedélyeit úgy állítsa be, hogy csak a gyökér férhet hozzá, így az alacsonyabb szintű felhasználók nem tudják letölteni a tárfiók kulcsát.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **A következő parancs segítségével fűzi `/etc/fstab`hozzá a következő sort: **Az alábbi példában a helyi Linux fájl- és mappaengedélyek alapértelmezett 0755, ami azt jelenti, olvasás, írás és végrehajtás a tulajdonos számára (a fájl / könyvtár Linux tulajdonosa alapján), olvassa el és hajtsa végre a felhasználók számára a tulajdonoscsoportban, és olvassa el és hajtsa végre mások számára a rendszeren. A `uid` csatlakoztatási `gid` beállításokkal beállíthatja a csatlakoztatás felhasználói azonosítóját és csoportazonosítóját. Egyéni engedélyeket `dir_mode` `file_mode` is használhat és igény szerint beállíthat. Az engedélyek beállításáról a Wikipédia [UNIX numerikus jelölése](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) című témakörben talál további információt.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > A fenti csatlakoztatási parancs SMB 3.0-s csatlakoztatása. Ha a Linux-disztribúció nem támogatja az SMB 3.0 titkosítással, vagy ha csak az SMB 2.1-es, csak csatlakoztathatja egy Azure virtuális gép ugyanabban a régióban, mint a tárfiók. Az Azure-fájlmegosztás titkosítással nem támogatott SMB 3.0-s szolgáltatásra való csatlakoztatásához le kell [tiltania a titkosítást a tárfiók átvitele során.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Az Autofs használata az Azure-fájlmegosztás(ok) automatikus csatlakoztatásához

1. **Győződjön meg arról, hogy az autofs csomag telepítve van.**  

    Az autofs csomag telepíthető a csomagkezelő vel az Ön által választott Linux disztribúción. 

    **Ubuntu** és **Debian alapú** disztribúciók esetén használja a `apt` csomagkezelőt:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    A **Fedorán**, **a Red Hat Enterprise Linux 8+** és a **CentOS 8 +** készüléken használja a `dnf` csomagkezelőt:
    ```bash
    sudo dnf install autofs
    ```
    A Red **Hat Enterprise Linux** és **centOS**régebbi verzióiban használja a `yum` csomagkezelőt:
    ```bash
    sudo yum install autofs 
    ```
    **OpenSUSE**esetén használja `zypper` a csomagkezelőt:
    ```bash
    sudo zypper install autofs
    ```
2. **Csatlakoztatási pont létrehozása a megosztás(ok)hoz:**
   ```bash
    sudo mkdir /fileshares
    ```
3. **Kréta egy új egyéni autofs konfigurációs fájl**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Adja hozzá a következő bejegyzéseket az /etc/auto.fileshares fájlmegosztáshoz**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Adja hozzá a következő bejegyzést az /etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Autofs újraindítása**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **A megosztáshoz kijelölt mappa elérése**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Linux biztonságossá tétele
Az Azure-fájlmegosztás Linuxon való csatlakoztatásához a 445-ös portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB 1, más néven CIFS (Common Internet File System) egy örökölt fájlrendszerprotokoll, amely számos Linux disztribúcióban része. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy az Azure Files nem támogatja az SMB 1-et, és a Linux kernel 4.18-as verziójától kezdve a Linux lehetővé teszi az SMB 1 letiltását. Mindig [javasoljuk, hogy](https://aka.ms/stopusingsmb1) tiltsa le az SMB 1 a Linux-ügyfelek használata előtt SMB fájlmegosztások éles környezetben.

A Linux kernel 4.18-tól kezdve `cifs` az SMB kernel modul, amelyet örökölt okok miatt hívtak meg, `disable_legacy_dialects`egy új modulparamétert (amelyet különböző külső dokumentumok *parm-nak* neveznek), a . Bár a Linux kernel 4.18-ban vezették be, egyes gyártók ezt a változást az általuk támogatott régebbi kernelekre módosították. A kényelem érdekében az alábbi táblázat részletezi a modulparaméter elérhetőségét a gyakori Linux-disztribúciókon.

| Disztribúció | Letilthatja az SMB 1-et |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nem |
| Ubuntu 18.04 | Igen |
| Ubuntu 19.04+ | Igen |
| Debian 8-9 | Nem |
| Debian 10+ | Igen |
| Fedora 29+ | Igen |
| CentOS 7 | Nem | 
| CentOS 8+ | Igen |
| Red Hat Enterprise Linux 6.x-7.x | Nem |
| Red Hat Enterprise Linux 8+ | Igen |
| openSUSE Leap 15.0 | Nem |
| openSUSE Leap 15.1+ | Igen |
| openSUSE Tumbleweed | Igen |
| SUSE Linux Enterprise 11.x-12.x | Nem |
| SUSE Linux Enterprise 15 | Nem |
| SUSE Linux Enterprise 15.1 | Nem |

Ellenőrizheti, hogy a Linux disztribúció támogatja-e a `disable_legacy_dialects` modulparamétert a következő parancs segítségével.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Ennek a parancsnak a következő üzenetet kell kiadnia:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Az SMB 1 letiltása előtt ellenőriznie kell, hogy az SMB modul jelenleg nincs-e betöltve a rendszerbe (ez automatikusan megtörténik, ha SMB-megosztást csatlakoztat). Ezt a következő paranccsal teheti meg, amely semmit sem ad ki, ha az SMB nincs betöltve:

```bash
lsmod | grep cifs
```

A modul kiürítéséhez először távolítsa el `umount` az összes SMB-megosztást (a fent leírt parancs használatával). A rendszer összes csatlakoztatott SMB-megosztását a következő paranccsal azonosíthatja:

```bash
mount | grep cifs
```

Miután leszerelte az összes SMB fájlmegosztást, biztonságosan eltávolíthatja a modult. Ezt a `modprobe` paranccsal teheti meg:

```bash
sudo modprobe -r cifs
```

A modult manuálisan is betöltheti `modprobe` az SMB 1 eltávolítása kora segítségével:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Végül ellenőrizheti, hogy az SMB modul be van-e töltve `/sys/module/cifs/parameters`a paraméterrel, ha megnézi a betöltött paramétereket:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Az SMB 1 állandó letiltásához ubuntu- és Debian-alapú disztribúciókon, létre kell hoznod `/etc/modprobe.d/local.conf` egy új fájlt (ha még nincs enek egyéni lehetőséged más modulokhoz) a beállítással. Ezt a következő paranccsal teheti meg:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Az SMB modul betöltésével ellenőrizheti, hogy ez működött-e:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Visszajelzés
Linux felhasználók, szeretnénk hallani rólad!

Az Azure Files for Linux felhasználói csoport fórumot biztosít a visszajelzések megosztásához a Linuxon lévő fájltárolás kiértékelése és elfogadása során. E-mail [Azure Files Linux-felhasználók,](mailto:azurefiles@microsoft.com) hogy csatlakozzon a felhasználók csoportja.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [GYIK](../storage-files-faq.md)
* [Hibaelhárítás](storage-troubleshoot-linux-file-connection-problems.md)
