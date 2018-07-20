---
title: Az Azure Files üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure Files elejétől a végéig.
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
ms.openlocfilehash: 4cced4a735aaf92f803b45fab4afe5102020d469
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144271"
---
# <a name="how-to-deploy-azure-files"></a>Az Azure Files üzembe helyezése
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Ebből a cikkből megtudhatja, hogyan gyakorlatilag Azure Files üzembe helyezése a szervezeten belül.

Javasoljuk, hogy olvasási [Azure Files üzembe helyezésének megtervezése](storage-files-planning.md) előtt ebben a cikkben leírt lépések végrehajtásával.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már végrehajtotta az alábbi lépéseket:

- Létrehozott egy Azure Storage-fiókot a kívánt rugalmassági és titkosítási beállításokat, a régióban, amennyit csak szeretne. Lásd: [hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a Storage-fiók létrehozásáról részletes utasításokat.
- Létrehozott egy Azure-fájlmegosztást a Storage-fiókban a kívánt kvótát. Lásd: [fájlmegosztás létrehozása](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásáról részletes utasításokat.

## <a name="transfer-data-into-azure-files"></a>Adatok átvitele az Azure Files
Előfordulhat, hogy szeretne áttelepíteni meglévő fájlmegosztások, például azokat a helyszínen tárolt, az új Azure-fájlmegosztást. Ez a szakasz bemutatja, hogyan adatok áthelyezése az Azure-fájlmegosztások a részletes számos népszerű módszer használatával a [tervezési útmutató](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Azure File Sync
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt nem átalakításával keletkező a Windows-kiszolgálók az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Az Azure File Sync adatok áttelepítése az Azure-fájlmegosztások használhatók, még akkor is, ha a szinkronizálási mechanizmus nem kívánt hosszú távú használatra. További információ az Azure File Sync használata az adatok átviteléhez az Azure-fájlmegosztás található [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Az Azure Import/Export
Az Azure Import/Export szolgáltatás lehetővé teszi, hogy biztonságosan továbbíthat nagy mennyiségű adatot az Azure-fájlmegosztások merevlemezes meghajtók egy Azure-adatközpontban való szállításával. Lásd: [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez az Azure storage-](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a szolgáltatás részletes áttekintése.

> [!Note]  
> Az Azure Import/Export szolgáltatás az Azure-fájlmegosztások a fájlok exportálása jelenleg nem támogatja.

Az alábbi lépéseket fogja adatokat importálhat a helyszíni helyről az Azure-fájlmegosztást.

1. Be kell szereznie a szükséges számú mail merevlemezek az Azure-bA. Merevlemezek lemez méretű lehet, de kell lennie vagy egy 2,5" vagy a 3.5-ös" SSD vagy HDD, SATA II vagy SATA III normál támogatása. 

2. Csatlakoztatás minden lemezhez a kiszolgáló/számítógépről az adatátvitel során. Az optimális teljesítmény érdekében javasoljuk a helyi exportálási feladat helyben fut a kiszolgálón, amely tartalmazza az adatokat. Például egy NAS-eszközök esetén a fájlkiszolgálóra, amelynek szolgál az adatok bizonyos esetekben ez nem lehetséges. Ebben az esetben egy tökéletesen feltételei minden lemez csatlakoztatása a számítógépen.

3. Győződjön meg róla, minden olyan meghajtó online állapotban, inicializálva, és a meghajtóbetűjel van hozzárendelve. A meghajtó online állapotba, inicializálása és társítson egy meghajtóbetűjelet, nyissa meg a lemezkezelő MMC beépülő modul (diskmgmt.msc).

    - A lemezek online (Ha még nem szerepel online), kattintson a jobb gombbal az alsó ablaktáblán, a lemezkezelő MMC a lemezen, és válassza az "Online".
    - A lemez inicializálása, kattintson a jobb gombbal a kisebb ablaktábla (miután a lemez online állapotban), és válasszon "Initialize" a lemezen. Mindenképpen jelölje be a "GPT", ha a rendszer kéri.

        ![A lemez inicializálása a lemezkezelő MMC menüjének képernyőképe](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Rendeljen hozzá meghajtóbetűjelet a lemezhez, kattintson a jobb gombbal a lemez online állapotban és inicializálva a "nem lefoglalt" terület, és kattintson az "Új egyszerű kötet". Ez lehetővé teszi, hogy a meghajtó betűjeléhez. Vegye figyelembe, hogy nem kell formázza a kötetet, mivel ezt később fogja elvégezni.

        ![Az új egyszerű kötet varázslóban a lemezkezelő MMC a képernyőképe](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Az adatkészlet CSV-fájl létrehozása. Az adatkészlet CSV-fájl elérési útját az adatokat a helyszíni közötti leképezést, amely a kívánt Azure-fájlmegosztást az adatokat másolja. A következő adatkészlet CSV-fájl például egy helyszíni fájlmegosztás ("F:\shares\scratch") leképezhető egy Azure-fájlmegosztást ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Storage-fiók több megosztást lehet megadni. Lásd: [az adatkészlet CSV-fájl előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) további információt.

5. Hozzon létre a driveset CSV-fájlt. A driveset CSV-fájlt a helyi exportálási ügynök elérhető lemezek listája. Például a következő driveset CSV fájl a listák `X:`, `Y:`, és `Z:` használhatók a helyi meghajtókat exportálási feladatot:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Lásd: [driveset CSV-fájl előkészítése](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) további információt.

6. Használja a [WAImportExport eszköz](https://www.microsoft.com/en-us/download/details.aspx?id=55280) az adatok másolása egy vagy több merevlemez-meghajtókat.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ne módosítsa a merevlemez-meghajtók vagy a naplófájl található adatokat lemezre előkészítés befejezése után.

7. [Importálási feladat létrehozása](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>A Robocopy
A Robocopy egy olyan jól ismert másolás eszköz, Windows és Windows Server részét képező. A Robocopy adatok átviteléhez az Azure Files helyileg a fájlmegosztás csatlakoztatása és a csatlakoztatott helyet használva a Robocopy parancsot a célként használható. A Robocopy használatával használata rendkívül egyszerű:

1. [Az Azure-fájlmegosztás csatlakoztatása](storage-how-to-use-files-windows.md). Az optimális teljesítmény érdekében javasoljuk, hogy az Azure-fájlmegosztás helyi csatlakoztatásával a kiszolgálón, amely tartalmazza az adatokat. Például egy NAS-eszközök esetén a fájlkiszolgálóra, amelynek szolgál az adatok bizonyos esetekben ez nem lehetséges. Ebben az esetben egy tökéletesen feltételei az Azure-fájlmegosztás csatlakoztatása a számítógépen. Ebben a példában `net use` a fájlmegosztások csatlakoztatására szolgál a parancssorban:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Használat `robocopy` adatok áthelyezése az Azure-fájlmegosztás a parancssorban:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    A Robocopy a másolási viselkedés igény szerint módosíthatja a beállítások jelentős számú rendelkezik. További információkért tekintse meg a [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) manuális lapot.

### <a name="azcopy"></a>AzCopy
Az AzCopy egy parancssori segédprogram, és az Azure Files, valamint az Azure Blob storage, az adatok másolása szolgál az optimális teljesítmény érdekében egyszerű parancs használatával. Az AzCopy használata egyszerű:

1. Töltse le a [Windows az AzCopy legújabb verzióját](http://aka.ms/downloadazcopy) vagy [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Használat `azcopy` adatok áthelyezése az Azure-fájlmegosztás a parancssorból. A Windows a szintaxisa a következő: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    A Linux a parancs szintaxisa kissé eltérő:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    Az AzCopy a másolási viselkedés igény szerint módosíthatja a beállítások jelentős számú rendelkezik. További információkért tekintse meg [az AzCopy Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) és [AzCopy linuxon](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>A szükséges számítógépek és kiszolgálók automatikusan csatlakoztatható
Cserélje le az a helyszíni fájlmegosztások, érdemes előre csatlakoztassa a megosztásokat a gépeken, a rendszer azt használja a. Ezt megteheti automatikusan a gépek listáját.

> [!Note]  
> Azure-fájlmegosztás csatlakoztatása a tárfiókkulcs használatával jelszót igényel, ezért csak javasoljuk a megbízható környezetek csatlakoztatása. 

### <a name="windows"></a>Windows
PowerShell a mount parancs futtatása több számítógépeken használható. A következő példában `$computers` manuálisan fel van töltve, azonban létrehozhat automatikusan csatlakoztatni a számítógépek listáját. Ha például töltheti fel ezt a változót, az eredmények az Active Directoryból.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Egy egyszerű bash-szkript az ssh-val kombinált eredményezhet a következő példában ugyanazt az eredményt. A `$computer` kell feltöltenie a felhasználó által ehhez hasonlóan maradt a változót:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [A Windows Azure-fájlok hibaelhárítása](storage-troubleshoot-windows-file-connection-problems.md)
- [A linuxon futó Azure-fájlok hibaelhárítása](storage-troubleshoot-linux-file-connection-problems.md)