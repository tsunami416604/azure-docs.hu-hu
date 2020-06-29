---
title: A Azure Files üzembe helyezése | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a Azure Files az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b9df9375dee59df987cea01a4142a22a78eb533e
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85510794"
---
# <a name="how-to-deploy-azure-files"></a>Az Azure Files üzembe helyezése
A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő SMB protokollon keresztül érhető el. Ez a cikk bemutatja, hogyan lehet gyakorlatilag üzembe helyezni Azure Files a szervezeten belül.

Javasoljuk, hogy az ebben a cikkben ismertetett lépések végrehajtása előtt olvassa el a [Azure Files központi telepítésének megtervezését](storage-files-planning.md) .

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már végrehajtotta a következő lépéseket:

- Létrehozott egy Azure Storage-fiókot a kívánt rugalmassági és titkosítási beállításokkal a kívánt régióban. A Storage-fiók létrehozásával kapcsolatos részletes útmutatásért tekintse meg a [Storage-fiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) című témakört.
- Létrehozott egy Azure-fájlmegosztást a kívánt kvótával a Storage-fiókban. A fájlmegosztás létrehozásával kapcsolatos részletes útmutatásért tekintse meg a [fájlmegosztás létrehozása](storage-how-to-create-file-share.md) című témakört.

## <a name="transfer-data-into-azure-files"></a>Adatok átvitele a Azure Filesba
Előfordulhat, hogy át kívánja telepíteni a meglévő fájlmegosztást, például a helyszínen tárolt fájlokat az új Azure-fájlmegosztás számára. Ez a szakasz bemutatja, hogyan helyezhetők át adatok egy Azure-fájlmegosztás számára a [tervezési útmutatóban](storage-files-planning.md#migration) részletesen ismertetett népszerű módszerek használatával.

### <a name="azure-file-sync"></a>Azure File Sync
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ez a Windows-kiszolgálók Azure-fájlmegosztás gyors gyorsítótárba alakításával végezhető el. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A Azure File Sync akkor is használható, ha egy Azure-fájlmegosztás számára kívánja áttelepíteni az adatátvitelt, még akkor is, ha a szinkronizálási mechanizmus hosszú távú használatra nem szükséges. Az adatok Azure-ba való átvitelének Azure File Sync használatáról további információt talál a [Azure file Sync központi telepítésének megtervezése](storage-sync-files-planning.md) és [a Azure file Sync üzembe helyezése című](storage-sync-files-deployment-guide.md)témakörben.

### <a name="azure-importexport"></a>Azure Import/Export
Az Azure import/export szolgáltatás lehetővé teszi nagy mennyiségű adatok biztonságos átvitelét egy Azure-fájlmegosztásba egy Azure-adatközpontba a merevlemez-meghajtók használatával. A szolgáltatás részletesebb áttekintését lásd: [a Microsoft Azure import/export szolgáltatás használata az adatok Azure Storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) -ba történő átviteléhez.

> [!Note]  
> Az Azure import/export szolgáltatás jelenleg nem támogatja az Azure-fájlmegosztás fájljainak exportálását.

A következő lépések a helyszíni helyről az Azure-fájlmegosztás adatait fogják importálni.

1. Az Azure-ba való levelezéshez szükséges merevlemezek számának beszerzése. A merevlemezek bármilyen méretűek lehetnek, de a következőnek kell lennie: 2,5 "vagy 3,5" SSD vagy HDD, amely támogatja a SATA II vagy a SATA III szabványt. 

2. Csatlakoztassa és csatlakoztassa az összes lemezt a kiszolgálón, illetve a számítógépen az adatátvitel során. Az optimális teljesítmény érdekében javasoljuk, hogy helyileg futtassa a helyszíni exportálási feladatot azon a kiszolgálón, amelyen az adatok szerepelnek. Bizonyos esetekben, például ha az adatkiszolgáló, amely egy NAS-eszköz, előfordulhat, hogy ez nem lehetséges. Ebben az esetben tökéletesen elfogadható az egyes lemezek csatlakoztatása a SZÁMÍTÓGÉPeken.

3. Győződjön meg arról, hogy minden meghajtó online állapotú, inicializálva van, és meghajtóbetűjelet kap. A meghajtó online állapotba helyezéséhez, inicializálásához és meghajtóbetűjel hozzárendeléséhez nyissa meg a Lemezkezelés MMC beépülő modult (diskmgmt. msc).

    - Ha online állapotba szeretne hozni egy lemezt (ha még nincs online állapotban), kattintson a jobb gombbal a lemezre a Lemezkezelés MMC alsó paneljén, és válassza az "online" lehetőséget.
    - Egy lemez inicializálásához kattintson a jobb gombbal a lemezre az alsó ablaktáblán (miután a lemez online állapotban van), és válassza az "inicializálás" lehetőséget. Ha a rendszer kéri, válassza a "GPT" lehetőséget.

        ![A Lemezkezelés beépülő modul lemezének inicializálási menüjének képernyőképe](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Ha meghajtóbetűjelet szeretne hozzárendelni a lemezhez, kattintson a jobb gombbal az online és a inicializált lemez "le nem foglalt" területére, majd kattintson az "új egyszerű kötet" elemre. Ez lehetővé teszi a meghajtóbetűjelek hozzárendelését. Vegye figyelembe, hogy a kötet formázása nem szükséges, mert később ezt fogja elvégezni.

        ![Képernyőkép a Lemezkezelés beépülő modul új egyszerű kötet varázslójával](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Hozza létre az adatkészlet CSV-fájlját. Az adatkészlet CSV-fájlja a helyszíni adat elérési útja és a kívánt Azure-fájlmegosztás közötti leképezés, amelyet az adatokat át kell másolni. Például a következő adatkészlet CSV-fájlja egy helyszíni fájlmegosztást ("F:\shares\scratch") rendel egy Azure-fájlmegosztás ("MyAzureFileShare") számára:
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    A Storage-fiókkal több megosztás is megadható. További információ: [az ADATKÉSZLET CSV-fájljának előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

5. Hozza létre a driveset CSV-fájlt. A driveset CSV-fájl felsorolja a helyszíni exportálási ügynök számára elérhető lemezeket. Például a következő driveset CSV-fájlok listája `X:` , és a helyszíni `Y:` `Z:` exportálási feladatokban használandó meghajtók:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    További információ: [a DRIVESET CSV-fájl előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

6. Az [WAImportExport eszköz](https://www.microsoft.com/download/details.aspx?id=55280) használatával másolja az adatait egy vagy több merevlemezre.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > A lemez előkészítésének befejezése után ne módosítsa a merevlemez-meghajtókon vagy a naplófájlban lévő adatfájlokat.

7. [Importálási feladatok létrehozása](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
A Robocopy egy jól ismert másolási eszköz, amely a Windows és a Windows Server rendszert is tartalmazza. A Robocopy felhasználható az adatok Azure Filesba történő átvitelére a fájlmegosztás helyi csatlakoztatásával, majd a csatlakoztatott hely célként való használatával a Robocopy parancsban. A Robocopy használata meglehetősen egyszerű:

1. [Csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md). Az optimális teljesítmény érdekében javasoljuk, hogy helyileg csatlakoztassa az Azure-fájlmegosztást az azokat tartalmazó kiszolgálón. Bizonyos esetekben, például ha az adatkiszolgáló, amely egy NAS-eszköz, előfordulhat, hogy ez nem lehetséges. Ebben az esetben tökéletesen elfogadható az Azure-fájlmegosztás csatlakoztatása egy számítógépen. Ebben a példában `net use` a parancssorban a fájlmegosztás csatlakoztatására használjuk a következőt:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Az `robocopy` Azure-fájlmegosztás eléréséhez használja a parancsot a parancssorban:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    A Robocopy jelentős számú lehetőséget kínál a másolási viselkedés igény szerinti módosítására. További információkért tekintse meg a [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) manuális oldalát.

### <a name="azcopy"></a>AzCopy
A AzCopy egy parancssori segédprogram, amely az adatok Azure Filesba, valamint az Azure Blob Storage-ba történő másolására szolgál, és az optimális teljesítményű egyszerű parancsokat használja. A AzCopy használata egyszerű:

1. Töltse le a [AzCopy legújabb verzióját Windows](https://aka.ms/downloadazcopy) vagy [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy)rendszeren.
2. Az `azcopy` Azure-fájlmegosztás eléréséhez használja a parancsot a parancssorban. A Windows szintaxisa a következő: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linux rendszeren a parancs szintaxisa némileg eltér:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    A AzCopy jelentős számú lehetőséggel módosíthatja a másolási viselkedést a kívánt módon. További információkért tekintse meg a Windows és a AzCopy [AzCopy a](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) [Linuxon](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)című témakört.

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatikus csatlakoztatás szükséges számítógépeken/kiszolgálókon
Egy helyszíni fájlmegosztás cseréjéhez hasznos a megosztások előzetes csatlakoztatása azon gépeken, amelyeket használni fog. Ezt automatikusan megteheti a számítógépek listáján.

> [!Note]  
> Az Azure-fájlmegosztás csatlakoztatásához jelszóként a Storage-fiók kulcsát kell használnia, ezért csak megbízható környezetekben javasolt a csatlakoztatás. 

### <a name="windows"></a>Windows
A PowerShell több számítógépen is használható a csatlakoztatási parancs futtatására. A következő példában `$computers` manuálisan van feltöltve, de létrehozhatja az automatikusan csatlakoztatni kívánt számítógépek listáját. Feltöltheti például ezt a változót a Active Directory eredményeiből.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Az SSH-val kombinálva egy egyszerű bash-szkript ugyanazt az eredményt eredményezheti a következő példában. A `$computer` változó a felhasználótól hasonlóan marad:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Következő lépések
- [Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
- [Azure-fájlok hibaelhárítása Windows rendszeren](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok hibaelhárítása Linux rendszeren](storage-troubleshoot-linux-file-connection-problems.md)
