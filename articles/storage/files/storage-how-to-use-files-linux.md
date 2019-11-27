---
title: Azure Files használata Linux rendszeren | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathat egy Azure-fájlmegosztást az SMB protokollon keresztül a Linuxon.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3d8d7c6d3c4e752480310c122bcb7db237b3022b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209413"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztás az [SMB kernel-ügyféllel](https://wiki.samba.org/index.php/LinuxCIFS)is csatlakoztatható Linux-disztribúciókban. Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy Azure-fájlmegosztást: igény szerint a `mount` paranccsal és a rendszerindítással, ha `/etc/fstab`-bejegyzést hoz létre.

Az Azure-fájlmegosztás Linux rendszeren való csatlakoztatásának ajánlott módja az SMB 3,0 használata. Alapértelmezés szerint a Azure Files az átvitel során titkosítást igényel, amelyet csak az SMB 3,0 támogat. A Azure Files támogatja az SMB 2,1-et is, amely nem támogatja az átvitel közbeni titkosítást, de biztonsági okokból nem csatlakoztathatja az Azure-fájlmegosztást egy másik Azure-régióból vagy helyszíni SMB 2,1-ből. Ha az alkalmazása kifejezetten az SMB 2,1-t igényli, kevés az oka annak, hogy a legnépszerűbb, legutóbb kiadott Linux-disztribúciók az SMB 3,0 támogatását használják:  

| | SMB 2.1 <br>(Ugyanazon az Azure-régióban lévő virtuális gépekre csatlakoztatások) | SMB 3.0 <br>(A helyszíni és a régiók közötti) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8+ | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12+ | 12 SP3 + |

Ha a fenti táblázatban nem szereplő Linux-disztribúciót használ, ellenőrizheti, hogy a Linux-disztribúció támogatja-e az SMB 3,0-et a titkosítással a Linux kernel verziójának ellenőrzésével. Az SMB 3,0 titkosítással lett hozzáadva a Linux kernel 4,11-es verziójához. A `uname` parancs a használatban lévő Linux kernel verzióját fogja visszaadni:

```bash
uname -r
```

## <a name="prerequisites"></a>Előfeltételek
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Győződjön meg arról, hogy a CIFS-utils csomag telepítve van.**  
    A CIFS-utils csomag a kiválasztott Linux-disztribúcióban telepíthető a Package Manager használatával. 

    **Ubuntu** -és **Debian-alapú** disztribúciókban használja a `apt` csomagkezelő eszközt:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**, **Red Hat Enterprise Linux 8 +** és **CentOS 8 +** esetén használja a `dnf` csomagkezelő eszközt:

    ```bash
    sudo dnf install cifs-utils
    ```

    A **Red Hat Enterprise Linux** és a **CentOS**régebbi verzióiban használja a `yum` csomagkezelő eszközt:

    ```bash
    sudo yum install cifs-utils 
    ```

    Az **openSUSE**-ben használja a `zypper` csomagkezelő eszközt:

    ```bash
    sudo zypper install cifs-utils
    ```

    Más disztribúciók esetén használja a megfelelő csomagkezelő vagy [fordítás forrásból elemet](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Az Azure parancssori felület (CLI) legújabb verziója.** Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az operációs rendszer kiválasztása. Ha inkább a Azure PowerShell modult szeretné használni a PowerShell 6 +-ban, akkor az alábbi utasításokat azonban az Azure CLI-hez mutatjuk be.

* **Győződjön meg arról**, hogy a 445-es port nyitva van: az SMB a 445-es TCP-porton keresztül kommunikál, és ellenőrizze, hogy a tűzfal nem blokkolja-e az ügyfélszámítógép TCP-445 portjait.  Cserélje le **< az-erőforráscsoport >** és **< a storage-fiókot >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Ha a kapcsolódás sikeres volt, a következő kimenethez hasonlónak kell megjelennie:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Ha nem tudja megnyitni a 445-as portot a vállalati hálózaton, vagy ha az INTERNETSZOLGÁLTATÓ ezt letiltja, akkor VPN-kapcsolattal vagy ExpressRoute kell használnia a 445-es porton való működéshez. További információ: [hálózati megfontolások az Azure-fájlmegosztás közvetlen eléréséhez](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása
Ha egy Azure-fájlmegosztást Linux-disztribúcióval szeretne használni, létre kell hoznia egy könyvtárat, amely az Azure-fájlmegosztás csatlakoztatási pontként szolgál. Egy csatlakoztatási pont bárhol létrehozható a Linux rendszeren, de ez a közös konvenció a/mnt. alatt történő létrehozásához. A csatlakoztatási pont után a `mount` parancs használatával férhet hozzá az Azure-fájlmegosztás eléréséhez.

Szükség esetén egyszerre több csatlakoztatási pontra is csatlakoztathatja az Azure-fájlmegosztást.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Igény szerinti Azure-fájlmegosztás csatlakoztatása `mount`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: cserélje le `<your-resource-group>`, `<your-storage-account>`és `<your-file-share>` a megfelelő információkkal a környezetéhez:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. Az **Azure-fájlmegosztás csatlakoztatásához használja a mount parancsot**. Az alábbi példában a helyi Linux-fájl és a mappa engedélyei alapértelmezett 0755, ami azt jelenti, hogy a tulajdonos (fájl/könyvtár Linux-tulajdonos) olvasási, írási és végrehajtási jogosultsággal rendelkezik, olvasás és végrehajtás a tulajdonos csoportban lévő felhasználók számára, valamint olvasás és végrehajtás mások számára a rendszeren. A `uid` és a `gid` csatlakoztatási lehetőségekkel beállíthatja a csatlakoztatás felhasználói AZONOSÍTÓját és a csoport AZONOSÍTÓját. `dir_mode` és `file_mode` is használhatja az egyéni engedélyek megadásához a kívánt módon. További információ az engedélyek beállításáról: [UNIX numerikus jelölés](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) a wikipedia-ben. 

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
    > A fenti csatlakoztatási parancs az SMB 3,0-vel együtt csatlakoztatható. Ha a Linux-disztribúció nem támogatja az SMB 3,0 titkosítást, vagy ha az csak az SMB 2,1-et támogatja, akkor csak a Storage-fiókkal azonos régióban lévő Azure-beli virtuális gépekről lehet csatlakoztatni. Ha az Azure-fájlmegosztást olyan Linux-disztribúción szeretné csatlakoztatni, amely nem támogatja az SMB 3,0 titkosítást, [le kell tiltania a Storage-fiók titkosítását](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Ha elkészült az Azure-fájlmegosztás használatával, `sudo umount $mntPath` használatával leválaszthatja a megosztást.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Állandó csatlakoztatási pont létrehozása az Azure-fájlmegosztás `/etc/fstab`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: egy csatlakozási ponthoz tartozó mappa bárhol létrehozható a fájlrendszerben, de ez a közös konvenció a/mnt. alatt történő létrehozásához. Például a következő parancs létrehoz egy új könyvtárat, és lecseréli a `<your-resource-group>`, `<your-storage-account>`és `<your-file-share>` helyére a környezetének megfelelő információkat:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Hozzon létre egy hitelesítőadat-fájlt a fájlmegosztás felhasználónevének (a Storage-fiók neve) és a jelszó (a Storage-fiók kulcsa) tárolásához.** 

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

1. **Módosítsa a hitelesítő fájl engedélyeit úgy, hogy csak a root tudja olvasni vagy módosítani a jelszót tartalmazó fájlt.** Mivel a Storage-fiók kulcsa lényegében egy felügyelői jelszó a Storage-fiókhoz, a fájl engedélyeinek megadásával, hogy csak a gyökér férhet hozzá, fontos, hogy az alacsonyabb jogosultságú felhasználók ne tudják beolvasni a Storage-fiók kulcsát.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. A **következő paranccsal fűzze hozzá a következő sort a `/etc/fstab`hoz** : az alábbi példában a helyi Linux-fájl és a mappa engedélyei alapértelmezett 0755, ami azt jelenti, hogy olvasás, írás és végrehajtás a tulajdonos számára (a fájl/könyvtár Linux-tulajdonos alapján), olvasás és végrehajtás a tulajdonos csoportba tartozó felhasználók számára, valamint olvasás és végrehajtás mások számára a rendszeren. A `uid` és a `gid` csatlakoztatási lehetőségekkel beállíthatja a csatlakoztatás felhasználói AZONOSÍTÓját és a csoport AZONOSÍTÓját. `dir_mode` és `file_mode` is használhatja az egyéni engedélyek megadásához a kívánt módon. További információ az engedélyek beállításáról: [UNIX numerikus jelölés](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) a wikipedia-ben.

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
    > A fenti csatlakoztatási parancs az SMB 3,0-vel együtt csatlakoztatható. Ha a Linux-disztribúció nem támogatja az SMB 3,0 titkosítást, vagy ha az csak az SMB 2,1-et támogatja, akkor csak a Storage-fiókkal azonos régióban lévő Azure-beli virtuális gépekről lehet csatlakoztatni. Ha az Azure-fájlmegosztást olyan Linux-disztribúción szeretné csatlakoztatni, amely nem támogatja az SMB 3,0 titkosítást, [le kell tiltania a Storage-fiók titkosítását](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>A Linux biztonságossá tétele
Az Azure-fájlmegosztás Linux rendszeren való csatlakoztatásához a 445-es portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB 1, más néven CIFS (Common Internet File System) egy örökölt fájlrendszeri protokoll, amely számos Linux-disztribúciót tartalmaz. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy Azure Files nem támogatja az SMB 1 használatát, és a Linux kernel 4,18-es verziójától kezdődően a Linux lehetővé teszi az SMB 1 letiltását. Mindig [erősen ajánlott](https://aka.ms/stopusingsmb1) az SMB 1 letiltása a Linux-ügyfeleken az SMB-fájlmegosztás éles környezetben való használata előtt.

A Linux kernel 4,18-es verziótól kezdődően az SMB kernel modul, amelyet örökölt okok miatt `cifs`, egy új modul-paramétert (más néven a különböző külső dokumentációban *paramétert* ) tesz elérhetővé (más néven `disable_legacy_dialects`). Bár a Linux kernel 4,18-ben jelent meg, egyes gyártók backported ezt a változást az általuk támogatott régebbi kerneleken. A kényelmes használat érdekében a következő táblázat részletezi a modul paraméterének rendelkezésre állását a gyakori Linux-disztribúciók esetében.

| Disztribúció | Az SMB 1 letiltása |
|--------------|-------------------|
| Ubuntu 14.04 – 16.04 | Nem |
| Ubuntu 18.04 | Igen |
| Ubuntu 19.04 + | Igen |
| Debian 8-9 | Nem |
| Debian 10 + | Igen |
| Fedora 29 + | Igen |
| CentOS 7 | Nem | 
| CentOS 8 + | Igen |
| Red Hat Enterprise Linux 6. x-7. x | Nem |
| Red Hat Enterprise Linux 8 + | Igen |
| openSUSE LEAP 15,0 | Nem |
| openSUSE ugrás 15.1 + | Igen |
| openSUSE Tumbleweed | Igen |
| SUSE Linux Enterprise 11. x-12. x | Nem |
| SUSE Linux Enterprise 15 | Nem |
| SUSE Linux Enterprise 15,1 | Nem |

A következő parancs használatával megtekintheti, hogy a Linux-disztribúciója támogatja-e a `disable_legacy_dialects` Module paramétert.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

A parancsnak a következő üzenetet kell kiadnia:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Az SMB 1 letiltása előtt ellenőriznie kell, hogy az SMB-modul jelenleg nincs-e betöltve a rendszeren (ez automatikusan megtörténik, ha az SMB-megosztást csatlakoztatta). Ezt a következő paranccsal teheti meg, amelynek kimenete nem történik meg, ha az SMB nincs betöltve:

```bash
lsmod | grep cifs
```

A modul eltávolításához először válassza le az összes SMB-megosztást (a fentiekben leírtak szerint a `umount` parancs használatával). A rendszeren található összes csatlakoztatott SMB-megosztást a következő paranccsal azonosíthatja:

```bash
mount | grep cifs
```

Miután leválasztotta az összes SMB-fájlmegosztást, biztonságosan eltávolíthatja a modult. Ezt a `modprobe` paranccsal teheti meg:

```bash
sudo modprobe -r cifs
```

Az `modprobe` paranccsal manuálisan is betöltheti a modult az SMB 1 memóriából:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Végezetül megtekintheti az SMB-modult a paraméterrel, ha megtekinti a betöltött paramétereket `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ha tartósan le szeretné tiltani az SMB 1-et az Ubuntu-és a Debian-alapú disztribúcióban, létre kell hoznia egy új fájlt (ha még nem rendelkezik a többi modulhoz tartozó egyéni beállításokkal), akkor a beállítással `/etc/modprobe.d/local.conf`t kell létrehoznia. Ezt a következő paranccsal teheti meg:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Az SMB-modul betöltésével ellenőrizheti, hogy ez működik-e:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Visszajelzés
Linux-felhasználók, szeretnénk hallani!

A Linux-felhasználók csoportjának Azure Files egy olyan fórumot biztosít, amellyel megoszthatja a visszajelzéseket, és elfogadhatja a Linuxon a fájlok tárolását. E-mail [Azure Files Linux-felhasználók](mailto:azurefileslinuxusers@microsoft.com) csatlakozhatnak a felhasználók csoportjához.

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Gyakori kérdések](../storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-linux-file-connection-problems.md)
