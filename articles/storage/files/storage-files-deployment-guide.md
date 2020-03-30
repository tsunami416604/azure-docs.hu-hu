---
title: Az Azure Files telepítése | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure Files-t az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598782"
---
# <a name="how-to-deploy-azure-files"></a>Az Azure Files üzembe helyezése
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztásokat kínál a felhőben, amelyek az iparági szabványnak megfelelő SMB protokollon keresztül érhetők el. Ez a cikk bemutatja, hogyan gyakorlatilag az Azure Files a szervezeten belül.

Javasoljuk, hogy olvassa el [az Azure Files üzembe helyezésének tervezése](storage-files-planning.md) című cikkben ismertetett lépések beolvasását.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már végrehajtotta a következő lépéseket:

- Létrehozott egy Azure Storage-fiókot a kívánt rugalmassági és titkosítási beállításokkal, a kívánt régióban. Lásd: [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) című témakört a tárfiók létrehozásának részletes útmutatója.
- Létrehozott egy Azure-fájlmegosztást a kívánt kvótával a storage-fiókban. A fájlmegosztás létrehozásának lépésről lépésre történő létrehozásáról a [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) című témakörben nyújt részt.

## <a name="transfer-data-into-azure-files"></a>Adatok átvitele az Azure-fájlokba
Előfordulhat, hogy szeretné áttelepíteni a meglévő fájlmegosztások, például a helyszíni tárolt, az új Azure-fájlmegosztás. Ez a szakasz bemutatja, hogyan helyezhetát át az adatokat egy Azure-fájlmegosztásba a [tervezési útmutatóban](storage-files-planning.md#migration) részletezett számos népszerű módszerrel.

### <a name="azure-file-sync"></a>Azure File Sync
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótárává alakítja. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Az Azure File Sync segítségével áttelepítheti az adatokat egy Azure-fájlmegosztásba, még akkor is, ha a szinkronizálási mechanizmus nem kívánatos a hosszú távú használatra. Az Azure File Sync használatával az Azure-fájlmegosztásba való átvitelével kapcsolatos további információk az [Azure-fájlszinkronizálás tervezése és](storage-sync-files-planning.md) [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)részben találhatók.

### <a name="azure-importexport"></a>Azure Import/Export
Az Azure importálási/exportálási szolgáltatás lehetővé teszi, hogy biztonságosan átnagy mennyiségű adatot egy Azure-fájlmegosztás szállításával merevlemez-meghajtók egy Azure-adatközpontba. A szolgáltatás részletesebb áttekintéséhez [olvassa el A Microsoft Azure importálási/exportálási szolgáltatásának használata az Azure storage-ba való átviteléhez](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) című témakört.

> [!Note]  
> Az Azure importálási/exportálási szolgáltatás jelenleg nem támogatja a fájlok exportálását egy Azure-fájlmegosztásból.

A következő lépések adatokimportálása egy helyszíni helyről az Azure-fájlmegosztásba.

1. Szerezze be a szükséges számú merevlemezt az Azure-ba való levelezéshez. A merevlemezek bármilyen méretűek lehetnek, de a SATA II vagy sATA III szabványt támogató 2,5" vagy 3,5" SSD vagy HDD lehet. 

2. Csatlakoztassa és csatlakoztassa az adatátvitelt lefolytató kiszolgálón/számítógépen lévő lemezeket. Az optimális teljesítmény érdekében azt javasoljuk, hogy futathatja a helyszíni exportálási feladatot helyileg az adatokat tartalmazó kiszolgálón. Bizonyos esetekben, például ha az adatokat kiszolgáló fájlkiszolgáló NAS-eszköz, ez nem biztos, hogy lehetséges. Ebben az esetben teljesen elfogadható, hogy minden lemezt a számítógépre szereljen fel.

3. Győződjön meg arról, hogy minden meghajtó online állapotban van, inicializálva van, és meghajtóbetűjellel van hozzárendelve. A meghajtó online állapotba hozásához inicializálhatja és hozzárendelni a meghajtóbetűjelet, nyissa meg a Lemezkezelés MMC beépülő modult (diskmgmt.msc).

    - Ha egy lemezt online szeretne állapotba hozni (ha még nincs online), kattintson a jobb gombbal a lemezre a Lemezkezelés MMC alsó ablaktáblájában, és válassza az "Online" lehetőséget.
    - A lemez inicializálásához kattintson a jobb gombbal az alsó ablaktáblában lévő lemezre (miután a lemez online állapotban van), és válassza az "Inicializálás" parancsot. Ügyeljen arra, hogy válassza ki a "GPT", amikor megkérdezte.

        ![Képernyőkép a Lemezkezelés MMC Lemez inicializálása menüjéről](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Ha meghajtóbetűjelet szeretne hozzárendelni a lemezhez, kattintson a jobb gombbal az online és az inicializált lemez "szabad" területére, majd kattintson az "Új egyszerű kötet" parancsra. Ez lehetővé teszi a meghajtóbetűjel hozzárendelését. Ne feledje, hogy nem kell formáznia a kötetet, mivel ez később történik.

        ![Képernyőkép az Új egyszerű kötet varázslóról a Lemezkezelés MMC-ben](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Hozza létre az adatkészlet CSV-fájlját. Az adatkészlet CSV-fájl egy leképezés az elérési utat a helyszíni adatok és a kívánt Azure-fájlmegosztás az adatokat kell másolni. Például a következő dataset CSV-fájl leképez egy helyszíni fájlmegosztást ("F:\shares\scratch") egy Azure-fájlmegosztásra ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Tárfiókkal több megosztás is megadható. További információt [az adatkészlet CSV-fájljának előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) című témakörben talál.

5. Hozza létre a meghajtó csv-fájlt. A meghajtóCSV-fájl felsorolja a helyszíni exportügynök számára elérhető lemezeket. Például a következő meghajtócsv-fájllisták `X:`, `Y:`és `Z:` a helyszíni exportálási feladatban használandó meghajtók:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    További információt [a meghajtócsv-fájl előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) című témakörben talál.

6. A [WAImportExport eszközzel](https://www.microsoft.com/download/details.aspx?id=55280) az adatokat egy vagy több merevlemezre másolhatja.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ne módosítsa a merevlemez-meghajtókon vagy a naplófájlban lévő adatokat a lemez előkészítésének befejezése után.

7. [Hozzon létre egy importálási feladatot](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy egy jól ismert másolási eszköz, hogy a hajók a Windows és a Windows Server. A Robocopy segítségével adatokat vihet át az Azure Files-ba a fájlmegosztás helyi csatlakoztatásával, majd a robocopy parancs ban a csatlakoztatott hely használatával. A Robocopy használata meglehetősen egyszerű:

1. [Csatlakoztassa az Azure-fájlmegosztást.](storage-how-to-use-files-windows.md) Az optimális teljesítmény érdekében azt javasoljuk, hogy az Azure-fájlmegosztást helyileg szerelje fel az adatokat tartalmazó kiszolgálóra. Bizonyos esetekben, például ha az adatokat kiszolgáló fájlkiszolgáló NAS-eszköz, ez nem biztos, hogy lehetséges. Ebben az esetben teljesen elfogadható az Azure-fájlmegosztás csatlakoztatása a számítógépen. Ebben `net use` a példában a fájlmegosztás csatlakoztatása a parancssorban használatos:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Használja `robocopy` a parancssorból az adatok áthelyezését az Azure-fájlmegosztásba:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy jelentős számú lehetőséget, hogy módosítsa a másolás viselkedését, mint kívánatos. További információkért tekintse meg a [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) kézikönyv oldalát.

### <a name="azcopy"></a>AzCopy
Az AzCopy egy parancssori segédprogram, amelyet az Azure Files-ba és az Azure Blob storage-ba történő másolásra terveztek, egyszerű parancsokat használva optimális teljesítménnyel. Az AzCopy használata egyszerű:

1. Töltse le az [AzCopy legújabb verzióját Windows vagy](https://aka.ms/downloadazcopy) [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy)rendszeren.
2. Használja `azcopy` a parancssorból adatok áthelyezése az Azure-fájlmegosztásra. A Windows szintaxisa a következő: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linuxon a parancs szintaxisa egy kicsit más:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    Az AzCopy jelentős számú lehetőséggel rendelkezik a másolási viselkedés igény szerint történő módosítására. További információért tekintse meg [az AzCopy on Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) és az [AzCopy on Linux című fájlokat.](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatikus csatlakoztatás a szükséges számítógépekhez/kiszolgálókhoz
A helyszíni fájlmegosztás lecseréléséhez hasznos, ha előre csatlakoztatja a megosztásokat azokon a gépeken, amelyeken használni fogja. Ez automatikusan elvégezhető a gépek listáján.

> [!Note]  
> Az Azure-fájlmegosztás csatlakoztatásához a tárfiók kulcsa kell a jelszó, ezért csak azt javasoljuk, hogy megbízható környezetben való csatlakoztatása. 

### <a name="windows"></a>Windows
A PowerShell több számítógépen is futtathatja a csatlakoztatási parancsot. A következő példában `$computers` a rendszer manuálisan tölti ki, de létrehozhatja az automatikusan csatlakoztatandó számítógépek listáját. Feltöltheti például ezt a változót az Active Directory ból származó eredményekkel.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Egy egyszerű bash script sSH-val kombinálva a következő példában ugyanazt az eredményt eredményezheti. A `$computer` változó t hasonlóképpen a felhasználó által feltöltendő:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
- [Azure-fájlok hibaelhárítása Windows rendszeren](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok hibaelhárítása Linux rendszeren](storage-troubleshoot-linux-file-connection-problems.md)
