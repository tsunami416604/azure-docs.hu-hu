---
title: "Telepítse át a helyszíni adatok Azure Storage AzCopy segítségével |} Microsoft Docs"
description: "AzCopy segítségével át adatokat, vagy másolja az adatokat, vagy a blob, table és a fájl. Könnyen áttelepíti az adatokat a helyi tárolóból Azure Storage."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: 3dbfb935ac0b134e127a5dccb7bc76716c688e8a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>A felhőbeli tárhelyén AzCopy használatával a helyszíni adatok áttelepítése

AzCopy parancssori segédprogram másolására adatok számára, vagy az Azure Blob Storage tárolóban, Azure fájlok és az Azure Table storage egyszerű parancsok segítségével. A parancsok az optimális teljesítmény készültek. Másolhat adatokat a fájlrendszer és a storage-fiók, vagy tárfiókok között.  

Az AzCopy két verziója tölthető le:

* [AzCopy Linux](storage-use-azcopy.md) .NET Core-keretrendszerben készült épül. Linux-platformokhoz tett POSIX-stílusú parancssori kapcsolók célozza azt. 
* [A Windows AzCopy](../storage-use-azcopy.md) a .NET-keretrendszer épül. Ez lehetővé teszi a Windows-stílusú parancssori kapcsolókat. 
 
Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy tárfiókot. 
> * AzCopy segítségével töltse fel az adatokat.
> * Módosíthatja az adatokat tesztelési célokra.
> * Hozzon létre egy ütemezett tevékenység vagy cron feladatot töltse fel az új fájlok azonosításához.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez le kell töltenie az AzCopy legújabb verzióját a [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) vagy [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Ha szeretne egy másodlagos régióban blobok letöltése a helyi tároló, és ez fordítva is igaz, állítsa be **replikációs** való **olvasás-access-georedundáns tárolás**. E beállítás megadásával létrehoz egy [georedundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) fiók. 
>
>

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. Tárolók segítségével csoportokba rendezheti a blobok például rendezheti a fájlokat a számítógépre, a mappákban. 

Kövesse az alábbi lépéseket egy tároló létrehozásához:

1. Válassza ki a **tárfiókok** gombra kattint, a fő lapján, és válassza ki a létrehozott tárfiókot.
2. Válassza ki **Blobok** alatt **szolgáltatások**, majd válassza ki **tároló**. 

   ![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolónévnek betűvel vagy számmal kell kezdődnie. Csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>A mappában lévő összes fájl feltöltése a Blob storage

AzCopy segítségével feltöltése a Blob storage egy mappában lévő összes fájlt a [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). A mappában található összes BLOB feltöltése, adja meg a következő AzCopy parancs:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Cserélje le `<key>` és `key` a fiókkulcs. Az Azure-portálon kérheti le a fiókkulcs kiválasztásával **hívóbetűk** alatt **beállítások** tárfiókba. Válasszon ki egy kulcsot, és illessze be az AzCopy parancs. Ha a megadott célhely tároló nem létezik, az AzCopy létrehozása, és a fájlt tölt be. A forrás elérési útja frissítése az adatok könyvtárba, és cserélje le **myaccount** az az URL-cím a tárfiók nevére.

A megadott könyvtár tartalmának feltöltése a Blob storage rekurzív módon, adja meg a `--recursive` (Linux) vagy `/S` (Windows) lehetőséget. AzCopy az ezen beállítások valamelyikét kell futtatnia, ha az almappákban és a fájlok feltöltése is.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob storage
Használhatja az AzCopy segítségével [fájlok feltöltése](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) az utolsó módosításának ideje alapján. Próbálkozzon a következővel, illetve módosíthatja vagy új fájlok létrehozása a forráskönyvtárban tesztelési célokra. Csak a frissített vagy új fájlok feltöltése, vegye fel a `--exclude-older` (Linux) vagy `/XO` az AzCopy parancs paramétert (Windows).

Ha szeretné, amelyek nem szerepelnek a célként megadott forrás-erőforrások másolása, adja meg, mindkettőt `--exclude-older` és `--exclude-newer` (Linux) vagy `/XO` és `/XN` az AzCopy parancs paramétereinek (Windows). AzCopy fájlfeltöltések csak a frissített adatokat, a hozzá tartozó időbélyeg alapján.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Hozzon létre egy ütemezett tevékenység vagy cron feladatot 
Egy ütemezett feladat vagy az AzCopy parancssori parancsprogram futó feladat cron is létrehozhat. A parancsfájl azonosítja, és feltölti a új helyszíni adatok felhőben levő tárolóba való adott időközönként. 

Másolja az AzCopy parancs egy szövegszerkesztőben. Frissítse az AzCopy parancs paraméter értékének a megfelelő értékeket. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) az AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy fut, a részletes `--verbose` (Linux) vagy `/V` (Windows) lehetőséget. A kimenet átirányítása fájlba. 

Ebben az oktatóanyagban [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) Windows az ütemezett feladat létrehozásához használt. A [Crontab](http://crontab.org/) parancs segítségével hozzon létre egy beütemezett feladatot Linux rendszeren. 
 **Schtasks** lehetővé teszi a rendszergazdának létrehozása, törlése, lekérdezése, módosítása, futtatására és leállítása helyi vagy távoli számítógépen. **Cron** lehetővé teszi a Linux és UNIX rendszerű felhasználók parancsokkal és parancsprogramokkal a futtatását, hogy a megadott napon és időpontban [cron-kifejezés](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Cron feladat létrehozása Linux rendszeren, adja meg a terminálon a következő parancsot: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

A cron-kifejezés megadásával `*/5 * * * * ` a parancs azt jelzi, hogy a parancsfájl `script.sh` kell futtatnia, ötpercenként. A parancsfájl futtatásához egy adott időpontban, naponta, havonta vagy évente is ütemezheti. A dátum és idő, a feladat-végrehajtás beállításával kapcsolatos további tudnivalókért lásd: [cron-kifejezés](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
A Windows ütemezett feladat létrehozásához adja meg a következő parancsot a parancssorba vagy a PowerShellben:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

A parancs használja:
- A `/SC` paraméter segítségével adjon meg egy perc ütemezést.
- A `/MO` paraméterrel adhatja meg az időköz 5 perc.
- A `/TN` paraméterrel adhatja meg a feladat nevét.
- A `/TR` paramétert adja meg az elérési útját a `script.bat` fájlt. 

A Windows az ütemezett feladat létrehozásával kapcsolatos további információkért lásd: [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Ellenőrizze, hogy az ütemezett feladat/cron feladat megfelelően fut-e, hozzon létre új fájlokat a `myfolder` könyvtár. Várjon, amíg a öt perc annak ellenőrzéséhez, hogy az új fájlok a tárfiók lettek feltöltve. Nyissa meg az ütemezett feladat vagy cron-feladat kimeneti naplók megtekintéséhez a napló könyvtárba. 

Helyezze át a helyszíni adatok Azure Storage és ez fordítva is igaz módjai kapcsolatos további információkért lásd: [helyezze át az adatokat, és az Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>További lépések
Azure Storage és AzCopy kapcsolatos további információkért lásd a következőket:

* [Az Azure Storage bemutatása](../storage-introduction.md)
* [Adatátvitel az AzCopy Windows rendszeren](storage-use-azcopy.md) 
* [Adatátvitel az AzCopy Linux rendszeren](storage-use-azcopy-linux.md) 
* [Tárfiók létrehozása](../storage-create-storage-account.md)
* [A Tártallózó alkalmazással blobok kezelése](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

