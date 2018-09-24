---
title: Helyszíni adatok migrálása az Azure Storage-ba az AzCopyval | Microsoft Docs
description: Az AzCopyval adatokat migrálhat vagy másolhat blob-, tábla és fájltartalmakból és -tartalmakba. Könnyedén migrálhatja adatait egy helyszíni tárolóból az Azure Storage-ba.
services: storage
author: roygara
ms.service: storage
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: bb3df82e66024b49e399ab2f7795a27932f7559e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953973"
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Helyszíni adatok migrálása felhőtárhelybe az AzCopyval

Az AzCopy egy olyan parancssori eszköz, amely lehetővé teszi, hogy egyszerű parancsokkal másoljon adatokat az Azure Blob Storage-ba, az Azure Files-ba és az Azure Table Storage-ba, illetve ezekből a tárolókból máshová. A parancsok úgy lettek kialakítva, hogy optimális teljesítményt nyújtsanak. Az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja.  

Az AzCopynak két verziója tölthető le:

* Az [AzCopy Linuxon](storage-use-azcopy-linux.md) futó verziója a .NET Core-keretrendszerrel lett felépítve. POSIX stílusú parancssori lehetőségeket kínál Linux platformokra. 
* Az [AzCopy Windowson](storage-use-azcopy.md) futó verziója a .NET-keretrendszerrel készült. Windows stílusú parancssori lehetőségeket kínál. 
 
Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók létrehozása. 
> * Minden adat feltöltése az AzCopy segítségével.
> * Az adatok módosítása tesztelési célból.
> * Ütemezett vagy cron feladat létrehozása az új feltölthető fájlok azonosításához.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez töltse le az AzCopy legújabb verzióját [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) vagy [Windows](http://aka.ms/downloadazcopy) rendszerhez. 

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

>[!NOTE]
>Ha szeretne blobokat letölteni egy másodlagos régióból a helyi tárolóba vagy fordítva, a **Replikáció** értéke legyen **Georedundáns írásvédett tárolás**. Ha ez a lehetőség ki van választva, egy [georedundáns tárfiók](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) jön létre. 
>
>

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. Tárolók használatával a blobok csoportjait ugyanúgy rendszerezheti, ahogy a fájlokat szokta a számítógép mappáiban. 

Kövesse az alábbi lépéseket a tároló létrehozásához:

1. A főoldalon kattintson a **Tárfiókok** gombra, majd válassza ki a létrehozott tárfiókot.
2. A **Szolgáltatások** területen kattintson a **Blobok** elemre, majd válassza a **Tároló** lehetőséget. 

   ![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolók nevének betűvel vagy számmal kell kezdődnie. A név csak betűkből, számokból és kötőjel (-) karakterből állhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Egy mappa összes fájljának feltöltése a Blob Storage-ba

Az AzCopyval feltöltheti egy mappa összes fájlját a Blob Storage-ba [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Cserélje le a `<key>` és `key` értékeket a fiók kulcsára. Az Azure Portalon a fiókkulcs lekéréséhez válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Válasszon ki egy kulcsot, majd illessze be az AzCopy-parancsba. Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba. Frissítse az adatkönyvtár forrásútvonalát, és cserélje le a **myaccount** kifejezést a tárfiókja nevére a cél URL-címében.

A megadott könyvtár tartalmának a Blob Storage-ba való rekurzív feltöltéséhez adja meg a `--recursive` (Linux) vagy az `/S` (Windows) beállítást. Ha az AzCopyt ezen beállítások egyikével futtatja, minden almappa és a bennük tárolt fájlok is feltöltődnek.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob Storage-ba
Az AzCopyval a legutóbbi módosításuk ideje alapján is [feltölthet fájlokat](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features). Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ha csak a frissített vagy új fájlokat szeretné feltölteni, adja hozzá az `--exclude-older` (Linux) vagy az `/XO` (Windows) paramétert az AzCopy-parancshoz.

Ha csak azokat az erőforrásokat szeretné átmásolni a forrásból, amelyek nem léteznek a célhelyen, adja meg az `--exclude-older` és az `--exclude-newer` (Linux), vagy az `/XO` és az `/XN` (Windows) paramétereket az AzCopy-parancsban. Az AzCopy az időbélyegek alapján csak a frissített adatokat tölti fel.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Ütemezett vagy cron feladat létrehozása 
Létrehozhat olyan ütemezett vagy cron feladatot, amely egy AzCopy parancssori szkriptet futtat. A szkript adott időközönként azonosítja és feltölti az új helyszíni adatokat a felhőtárolóba. 

Másolja az AzCopy-parancsot egy szövegszerkesztőbe. Frissítse az AzCopy-parancs paraméterértékeit a megfelelő értékekre. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) néven az AzCopyhoz. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

Az AzCopy a részletes `--verbose` (Linux) vagy `/V` (Windows) beállítással fut. A kimenet egy naplófájlba lesz átirányítva. 

Ebben az oktatóanyagban a [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) eszközt használjuk egy ütemezett feladat létrehozásához Windows rendszeren, és a [Crontab](http://crontab.org/) parancsot egy cron feladat létrehozásához Linux rendszeren. 
 A **Schtasks** lehetővé teszi a rendszergazdák számára ütemezett feladatok létrehozását, törlését, lekérdezését, módosítását, futtatását és befejezését helyi vagy távoli számítógépeken. A **Cron** lehetővé teszi a Linux- és Unix-felhasználók számára parancsok vagy szkriptek futtatását adott dátumon és időpontban [cron-kifejezések](https://en.wikipedia.org/wiki/Cron#CRON_expression) használatával.


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Cron feladat létrehozásához Linux rendszeren írja be az alábbi parancsot egy terminálon: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Ha a parancsban megadja az `*/5 * * * * ` cron-kifejezést, az azt jelenti, hogy a `script.sh` héjszkriptnek öt percenként kell futnia. A szkriptet ütemezheti úgy, hogy adott időben fusson naponta, havonta vagy évente. Ha többet szeretne megtudni a feladatok végrehajtási dátumának és idejének megadásáról, tekintse át a [cron-kifejezéseket](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Ha ütemezett feladatot szeretne létrehozni Windows rendszeren, írja be a következő parancsot a parancssorba vagy a PowerShellbe:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

A parancs a következő paramétereket használja:
- Az `/SC` paramétert a percenkénti ütemezés megadásához.
- Az `/MO` paramétert öt perces intervallumok meghatározásához.
- A `/TN` paramétert a feladat nevének megadásához.
- A `/TR` paramétert a `script.bat` fájl elérési útjának megadásához. 

További információk az ütemezett feladatok létrehozásáról Windows rendszeren: [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Ha ellenőrizni szeretné, hogy az ütemezett/cron feladat megfelelően fut-e, hozzon létre új fájlokat a `myfolder` könyvtárban. Várjon öt percet, és győződjön meg arról, hogy az új fájlok feltöltődtek a tárfiókjába. Navigáljon a naplókönyvtárhoz, és tekintse meg az ütemezett vagy a cron feladat kimeneti naplóit. 

A helyszíni adatok Azure Storage-ba, vagy a Storage-adatok helyszínre történő áthelyezéséről további információkat abban a cikkben talál, amely [az adatok áthelyezésével foglalkozik az Azure Storage-ba vagy onnan máshová](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>További lépések
További, az Azure Storage szolgáltatással és az AzCopyval kapcsolatos adatokat a következő erőforrások nyújtanak:

* [A Microsoft Azure Storage bemutatása](../storage-introduction.md)
* [Adatok áthelyezése az AzCopyval Windows rendszeren](storage-use-azcopy.md) 
* [Adatok áthelyezése az AzCopyval Linux rendszeren](storage-use-azcopy-linux.md) 
* [Tárfiók létrehozása](../storage-create-storage-account.md)
* [Blobok kezelése a Storage Explorerrel](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

