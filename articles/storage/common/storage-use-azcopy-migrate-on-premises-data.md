---
title: "Telepítse át a helyszíni adatok Azure Storage az AzCopy |} Microsoft Docs"
description: "AzCopy segítségével át adatokat, vagy másolja az adatokat, vagy a blob, table és a fájl. Könnyen áttelepíti az adatokat a helyi tárolóból az Azure storage."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: f2c0b80248ef706394b3f84df4c3da26fb79026a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>A helyszíni adatok AzCopy a felhőben levő tárolóba való áttelepítése

AzCopy egy parancssori segédprogram, és a Microsoft Azure Blob, a fájl és a tábla tárolási, adat másolása az optimális teljesítményének készült egyszerű parancsok használatával. Másolhat adatokat a fájlrendszer és a storage-fiók, vagy tárfiókok között.  

AzCopy, letöltheti a két verziója van:

* [AzCopy Linux](storage-use-azcopy.md) ajánlat POSIX-stílusú parancssori kapcsolók Linux platformon célozza .NET Core keretrendszerrel épül. 
* [A Windows AzCopy](../storage-use-azcopy.md) a .NET-keretrendszer épül, és ez biztosítja a Windows stílus parancssori kapcsolókat. 
 
Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Create a storage account 
> * Az adatok feltöltése az AzCopy segítségével
> * Módosíthatja az adatokat tesztelési célra
> * Hozzon létre egy ütemezett tevékenység vagy cron feladatot, új fájlok feltöltése azonosítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez: 

* Töltse le az AzCopy legújabb verzióját a [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) vagy [Windows](http://aka.ms/downloadazcopy). 

## <a name="log-in-to-the-azure-portalhttpsportalazurecom"></a>Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Ha szeretne egy másodlagos régióban blobok letöltése a helyi tároló, és ez fordítva is igaz, állítsa be **replikációs** való **olvasás-access-georedundáns tárolás**. E beállítás megadásával létrehoz egy [georedundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) fiók. 
>
>

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. Tárolók csoportokba rendezheti a blobok például rendezheti a fájlokat a számítógépre, a mappákban teszi lehetővé. 

Kövesse az alábbi lépéseket egy tároló létrehozásához:

1. Válassza ki a **tárfiókok** gombra kattint, a fő lapján, és válassza ki a létrehozott tárfiókot.
2. Válassza ki **Blobok** alatt **szolgáltatások**, majd jelölje be **tároló**. 

![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tároló nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

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

Cserélje le  **\<kulcs\>**  a fiókkulcs. Az Azure-portálon kérheti le a fiókkulcs kiválasztásával **hívóbetűk** a tárfiók beállítások. Válasszon ki egy kulcsot, és illessze be az AzCopy parancs. Ha a megadott célhely tároló nem létezik, az AzCopy létrehozása, és a fájlt tölt be. A forrás elérési útja frissítése az adatok könyvtárba, és cserélje le **myaccount** a tárfiók nevére az cél URL-címben.

Adja meg a `--recursive` és `/S` beállítások, a Linux és a Windows, a megadott könyvtár tartalmának feltöltése a Blob storage rekurzív módon. AzCopy az ezen beállítások valamelyikét kell futtatnia, ha az almappákban és a fájlok feltöltése is.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob storage
Használhatja az AzCopy segítségével [fájlok feltöltése](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) az utolsó módosításának ideje alapján. Próbálkozzon a következővel, illetve módosíthatja vagy új fájlok létrehozása a tesztelési célú forráskönyvtárában található. Csak a frissített vagy új fájlok feltöltéséhez hozzáadása `--exclude-older`vagy `/XO` paraméter az AzCopy Linux és Windows rendre parancsot.

Ha szeretné, amelyek nem szerepelnek a célként megadott forrás-erőforrások másolása, adja meg, mindkettőt `--exclude-older` és `--exclude-newer` vagy `/XO` és `/XN` paraméterek az AzCopy Linux és Windows rendre parancsot. AzCopy fájlfeltöltések csak a frissített adatokat időbélyegzőik alapján.
 
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
Létrehozhat egy ütemezett feladat/cron feladat, amely futtatja az AzCopy parancssori parancsprogram, amely azonosítja, és feltölti a új helyszíni adatok felhőben levő tárolóba való adott időközönként. 

Másolja az AzCopy parancs egy szövegszerkesztőben. Frissítse a paraméterek értékeit AzCopy parancs a megfelelő értékeket. Mentse a fájlt `script.sh` vagy `script.bat` az AzCopy a Linux és Windows kulcsattribútumokkal. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy fut, részletes `--verbose` (Linux) és `/V` (Windows) lehetőséget, és a kimeneti fájlba van átirányítva. 

Ebben az oktatóanyagban [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) használatával hozzon létre egy ütemezett feladatot a Windows, és [Crontab](http://crontab.org/) parancs segítségével hozzon létre egy beütemezett feladatot Linux rendszeren. 
 **Schtasks** lehetővé teszi a rendszergazdának létrehozása, törlése, lekérdezése, módosítása, futtatására és a helyi vagy távoli számítógép leállítása. **Cron** lehetővé teszi, hogy parancsokkal vagy parancsfájlok futtatásához megadott időpontban, és az idő használata a Linux és UNIX rendszerű felhasználók [cron-kifejezés](https://en.wikipedia.org/wiki/Cron#CRON_expression)


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
**Cron feladat létrehozása Linux**, a következő parancsot írja be a Terminálszolgáltatások. 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

A cron-kifejezés megadásával `*/5 * * * * ` a parancs azt mutatja, a parancsfájl `script.sh` kell végrehajtani, ötpercenként. A parancsfájl futtatásához egy adott időpontban naponta, havi vagy éves ütemezhető. Lásd: [cron-kifejezés](https://en.wikipedia.org/wiki/Cron#CRON_expression) tudhat meg többet a dátum és a feladat végrehajtásához szükséges idő beállítása. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
**A Windows ütemezett feladat létrehozásához**, adja meg a következő parancsot a parancssorból vagy a PowerShell.

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

A parancs a `/SC` paraméterrel adja meg percben ütemezés és a `/MO` paraméterrel adhatja meg az időköz 5 perc. A `/TN` paraméter használatával adja meg a tevékenység neve és `/TR` paramétert adja meg az elérési útját `script.bat` fájlt. Látogasson el [schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes) Windows az ütemezett feladat létrehozásával kapcsolatos további.

---
 
Az ütemezett feladat cron érvényesítéséhez feladat végrehajtja a megfelelő, új fájlok létrehozása a könyvtárban `myfolder`. Öt perc várakozás az új fájlok lettek feltöltve a tárfiók megerősítéséhez. Nyissa meg az ütemezett feladat/cron-feladat kimeneti naplók megtekintéséhez a napló könyvtárba. 

Látogasson el [áthelyezése a helyszíni adatok](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) további módjai helyezze át a helyszíni adatok Azure Storage és fordítva.  

## <a name="next-steps"></a>További lépések
Azure Storage és AzCopy kapcsolatos további információkért lásd a következőket:

### <a name="azure-storage-documentation"></a>Az Azure Storage-dokumentáció:
* [Az Azure Storage bemutatása](../storage-introduction.md)
* [Adatátvitel az AzCopy Windows rendszeren](storage-use-azcopy.md) 
* [Adatátvitel az AzCopy Linux rendszeren](storage-use-azcopy-linux.md) 
* [Tárfiók létrehozása](../storage-create-storage-account.md)
* [A Tártallózó alkalmazással blobok kezelése](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

