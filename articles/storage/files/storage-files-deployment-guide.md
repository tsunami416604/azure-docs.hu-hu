---
title: Azure-fájlok telepítése |} Microsoft Docs
description: 'Útmutató: Azure fájlok szolgáltatásra történő telepítését.'
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.openlocfilehash: 38a0e64bf958ef6aebac41136ffcb9c213cc7ec0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029781"
---
# <a name="how-to-deploy-azure-files"></a>Az Azure Files üzembe helyezése
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők az iparági szabványos SMB protokollon keresztül. Ez a cikk bemutatja, hogyan gyakorlatilag központi telepítése az Azure-fájlokat a szervezeten belül.

Erősen ajánlott olvasási [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md) ebben a cikkben ismertetett lépések előtt.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már végrehajtotta a következőket:

- Egy Azure Storage-fiók hozza létre a kívánt rugalmassági és titkosítási beállításokat, felügyelni a régióban. Lásd: [hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) vonatkozó részletes utasításokat a Storage-fiók létrehozásához.
- Az Azure fájlmegosztások létrehozása a Tárfiókban lévő a kívánt kvótát. Lásd: [fájlmegosztás létrehozása](storage-how-to-create-file-share.md) vonatkozó részletes utasításokat a fájlmegosztás létrehozásához.

## <a name="transfer-data-into-azure-files"></a>Adatok átviteléhez az Azure-fájlok
Kezdésként érdemes lehet áttelepíteni meglévő fájlmegosztások esetén ezek a helyszínen tárolt, például az új Azure fájlmegosztás. Ez a szakasz bemutatja, hogyan áthelyezni az adatokat az Azure fájlmegosztások be a részletes számos népszerű módszer használatával a [tervezési útmutató](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync-preview"></a>Az Azure File Sync (előzetes verzió)
Azure fájl szinkronizálása (előzetes verzió) lehetővé teszi a fájlmegosztások a szervezet Azure-fájlokban szereplő központosítása próbálkozik a rugalmasság, a teljesítmény és a kompatibilitási fájlt a helyi kiszolgáló nélkül. Ennek érdekében a Windows-kiszolgálók átalakítása a Azure fájlmegosztás gyors gyorsítótárába. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Azure fájlszinkronizálás is használható adatokat áttelepíteni az Azure fájlmegosztások, még akkor is, ha a szinkronizálási mechanizmus nem kívánt hosszú távú használatra. Azure fájlszinkronizálás adatok átviteléhez az Azure-fájlmegosztáshoz való használatáról további információk találhatók [Azure fájlszinkronizálás központi telepítésének tervezésében](storage-sync-files-planning.md) és [központi telepítése az Azure fájlszinkronizálás](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Az Azure Import/Export
Az Azure Import/Export szolgáltatás lehetővé teszi, hogy biztonságosan át nagy mennyiségű adatot az Azure fájlmegosztások által szállítási merevlemez-meghajtók számára egy Azure-adatközpontban. Lásd: [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez az Azure storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a szolgáltatás részletes ismertetése.

> [!Note]  
> Az Azure Import/Export szolgáltatás nem támogatja az Exportálás ekkor az Azure fájlmegosztások-fájlok.

Az alábbi lépéseket a helyszíni helyről adatokat az Azure fájlmegosztások importál.

1. Be kell szereznie a mail merevlemezek szükséges számát, az Azure. Merevlemezek lehet, hogy a lemez méretének, de lehet vagy egy 2,5" vagy 3.5-ös" SSD és HDD a SATA II vagy SATA III szabványt támogató. 

2. Csatlakozás, és csatlakoztassa az egyes lemezek számítógépen a kiszolgáló vagy az adatátvitel során. Az optimális teljesítmény érdekében javasoljuk, a helyi exportálási feladat helyben fut a kiszolgálón, amely tartalmazza az adatokat. Egyes esetekben, például egy NAS-eszköz esetén a fájlkiszolgálón, amely adatokat szolgáltat ez nem lehet. Ebben az esetben is tökéletesen elfogadható minden lemez csatlakoztatásához a számítógépen.

3. Győződjön meg arról, minden olyan meghajtó online állapotban és inicializálva, és hozzá van rendelve egy meghajtóbetűjelet. A meghajtó online állapotba, inicializálása és rendeljen meghajtóbetűjelet, nyissa meg a lemez kezelése MMC beépülő modul (Diskmgmt.msc beépülő modult).

    - A lemezek online (Ha még nincs online), kattintson a jobb gombbal az alsó ablaktáblán, a lemez Management MMC a lemezen, és válassza az "Online".
    - A lemez inicializálása, kattintson a jobb gombbal a alsó ablaktáblán (után a lemez online állapotban), és válasszon "Initialize" a lemezen. Győződjön meg arról, "GPT" kérdésnél kiválasztásához.

        ![A lemez inicializálása menü, a lemez Management MMC egy képernyőképe](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - A meghajtóbetűjelet rendelje a lemezre, kattintson a jobb gombbal a lemez online állapotban és inicializálva "szabad" lemezterület, majd kattintson az "Új egyszerű kötet". Ez lehetővé teszi, hogy a meghajtóbetűjelet rendelje. Vegye figyelembe, hogy nem kell formázza a kötetet, ezt később fogja végezni.

        ![A képernyőfelvétel az új egyszerű kötet varázslóban, a lemez felügyeleti MMC-ben](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. A dataset CSV-fájl létrehozása. A dataset CSV-fájl elérési útját az adatokat a helyszíni közötti leképezést, és másolja a kívánt Azure fájlmegosztás az adatok. Például a következő adatkészlet CSV-fájlt az Azure fájlmegosztások ("MyAzureFileShare") van leképezve egy helyszíni fájlmegosztás ("F:\shares\scratch"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Több megosztást a Storage-fiók adható meg. Lásd: [készítse elő a dataset CSV-fájl](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) további információt.

5. A driveset CSV-fájl létrehozása. A driveset CSV-fájlt a helyi exportálási ügynök számára elérhető lemezek sorolja fel. Például a következő driveset CSV fájl listák `X:`, `Y:`, és `Z:` használhatók a helyi meghajtók feladat exportálása:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Lásd: [készítse elő a driveset CSV-fájl](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) további információt.

6. Használja a [WAImportExport eszköz](https://www.microsoft.com/en-us/download/details.aspx?id=55280) másolja az adatokat legalább egy merevlemez-meghajtókat.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ne módosítsa a merevlemez-meghajtók vagy a napló fájlban lévő adatok lemezét befejezése után.

7. [Hozzon létre egy importálási feladat](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>A Robocopy
A Robocopy egy jól ismert másolási eszköz részét képező Windows és Windows Server. A Robocopy adatok átviteléhez az Azure-fájlok helyileg a fájlmegosztás csatlakoztatása és a csatlakoztatott hely majd segítségével a Robocopy parancsot a célként használható. A Robocopy használata rendkívül egyszerű:

1. [Az Azure fájlmegosztások csatlakoztatása](storage-how-to-use-files-windows.md). Az optimális teljesítmény érdekében javasoljuk, helyileg a Azure fájlmegosztás csatlakoztatása a kiszolgálón, amely tartalmazza az adatokat. Egyes esetekben, például egy NAS-eszköz esetén a fájlkiszolgálón, amely adatokat szolgáltat ez nem lehet. Ebben az esetben is tökéletesen elfogadható Azure fájlmegosztás csatlakoztatása egy számítógépen. Ebben a példában `net use` a fájlmegosztás csatlakoztatásához használatos a parancssorból:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Használjon `robocopy` adatok áthelyezése az Azure fájlmegosztások a parancssorból:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    A Robocopy számos jelentős tetszés szerint a Másolás viselkedését befolyásoló beállításokat. További információkért tekintse meg a [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) kézi.

### <a name="azcopy"></a>AzCopy
AzCopy egy parancssori segédprogram, és az Azure-fájlok, valamint az Azure Blob Storage tárolóban, az adatok másolása az optimális teljesítménnyel egyszerű parancsok használatával. AzCopy használata egyszerű:

1. Töltse le a [Windows AzCopy legújabb verzióját](http://aka.ms/downloadazcopy) vagy [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Használjon `azcopy` adatok áthelyezése az Azure fájlmegosztások a parancssorból. A Windows szintaxisa a következő: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linux a parancs szintaxisa eltérő:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy számos jelentős tetszés szerint a Másolás viselkedését befolyásoló beállításokat. További információkért tekintse meg [Windows AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) és [Linux AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatikus csatlakoztatást a szükséges számítógépek/kiszolgáló
Egy helyszíni fájlmegosztást használ, akkor az azt használni kívánt számítógépeken a megosztások előre csatlakoztatni. Ezt megteheti automatikusan a számítógépek listája.

> [!Note]  
> Az Azure fájlmegosztások csatlakoztatása van szüksége, használja a tárfiók kulcsa a jelszót, ezért csak ajánlott megbízható környezetben csatlakoztatni. 

### <a name="windows"></a>Windows
PowerShell is használható a csatlakoztatási parancs futtatása több számítógépeken. A következő példában `$computers` kézzel fel van töltve, de létrehozhat automatikusan csatlakoztatni a számítógépek listáját. Például ezt a változót, az Active Directoryból eredményekkel feltöltheti azt.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Egy egyszerű bash parancsfájl SSH együtt ugyanazt az eredményt a következő példa érvényesítését. A `$computer` változó hasonlóképpen balról tölthető fel, a felhasználó:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>További lépések
- [Egy Azure fájlszinkronizálás központi telepítésének tervezése](storage-sync-files-planning.md)
- [A Windows Azure fájlok hibaelhárítása](storage-troubleshoot-windows-file-connection-problems.md)
- [Az Azure Files Linux hibaelhárítása](storage-troubleshoot-linux-file-connection-problems.md)