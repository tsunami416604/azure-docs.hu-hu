---
title: 'Oktatóanyag: Helyszíni adatok migrálása az Azure Storage-ba az AzCopyval | Microsoft Docs'
description: Ebben az oktatóanyagban az AzCopyval fog adatokat migrálni vagy másolni blob-, tábla- és fájltartalmakból és -tartalmakba. Könnyedén migrálhatja adatait egy helyszíni tárolóból az Azure Storage-ba.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 5f09ae7dc625ad579e31fd49d70331f30e6a708a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844907"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval

Az AzCopy egy olyan parancssori eszköz, amely lehetővé teszi, hogy egyszerű parancsokkal másoljon adatokat az Azure Blob Storage-ba, az Azure Files-ba és az Azure Table Storage-ba, illetve ezekből a tárolókból máshová. A parancsok úgy lettek kialakítva, hogy optimális teljesítményt nyújtsanak. Az AzCopyval az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja. Az AzCopyval helyi (helyszíni) adattárolóból tárfiókba másolhat adatokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók létrehozása. 
> * Minden adat feltöltése az AzCopy segítségével.
> * Az adatok módosítása tesztelési célból.
> * Ütemezett vagy cron feladat létrehozása az új feltölthető fájlok azonosításához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez töltse le a AzCopy legújabb verzióját. Lásd: Ismerkedés [a AzCopy szolgáltatással](storage-use-azcopy-v10.md).

Ha Windows rendszert használ, szüksége lesz a [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) programra, mert az oktatóanyag azt használja a feladatok ütemezéséhez. A Linux-felhasználók e célra a crontab parancsot használják.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az első lépés a tároló létrehozása, mert a blobokat mindig egy tárolóba kell feltölteni. A tárolók segítségével úgy történik a blobcsoportok rendszerezése, ahogy a fájljait rendezi mappákba a számítógépén.

Kövesse az alábbi lépéseket a tároló létrehozásához:

1. A főoldalon kattintson a **Tárfiókok** gombra, majd válassza ki a létrehozott tárfiókot.
2. A **Szolgáltatások** területen kattintson a **Blobok** elemre, majd válassza a **Tároló** lehetőséget.

   ![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolók nevének betűvel vagy számmal kell kezdődnie. A név csak betűkből, számokból és kötőjel (-) karakterből állhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="download-azcopy"></a>AzCopy letöltése

Töltse le az AzCopy v10 végrehajtható fájlt.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) zip
- [Linuxos](https://aka.ms/downloadazcopy-v10-linux) Tar
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) zip

Helyezze a AzCopy-fájlt bárhol a számítógépre. Adja hozzá a fájl helyét a rendszerútvonal-változóhoz, hogy ezt a végrehajtható fájlt a számítógép bármelyik mappájából megtekintheti.

## <a name="authenticate-with-azure-ad"></a>Hitelesítés az Azure AD-vel

Először rendelje hozzá a [Storage blob adatközreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) szerepkört az identitásához. Lásd: [hozzáférés engedélyezése az Azure blobhoz és az üzenetsor-kezelés a RBAC a Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Ezután nyisson meg egy parancssort, írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ez a parancs egy hitelesítő kódot és egy webhely URL-címét adja vissza. Nyissa meg a webhelyet, adja meg a kódot, majd válassza a **tovább** gombot.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

Ekkor megjelenik egy bejelentkezési ablak. Ebben az ablakban jelentkezzen be az Azure-fiókjába az Azure-fiókja hitelesítő adataival. Miután sikeresen bejelentkezett, lezárhatja a böngészőablakot, és megkezdheti a AzCopy használatát.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Egy mappa tartalmának feltöltése a Blob Storage-ba

Az AzCopyval feltöltheti egy mappa összes fájlját a Blob Storage-ba [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le `<local-folder-path>` a helyőrzőt a fájlokat tartalmazó mappa elérési útjára (például: `C:\myFolder` vagy `/mnt/myFolder`).

* Cserélje le `<storage-account-name>` a helyőrzőt a Storage-fiók nevére.

* Cserélje le `<container-name>` a helyőrzőt a létrehozott tároló nevére.

Ha a megadott könyvtár tartalmát rekurzív módon szeretné feltölteni a blob Storage-ba, adja `--recursive` meg a beállítást. Ha ezzel a kapcsolóval futtatja az AzCopy-t, a rendszer az összes almappát és azok fájljait is feltölti.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob Storage-ba

A AzCopy használatával fájlokat tölthet fel a legutóbbi módosításuk időpontja alapján. 

Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ezután használja a AzCopy `sync` parancsot.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le `<local-folder-path>` a helyőrzőt a fájlokat tartalmazó mappa elérési útjára (például: `C:\myFolder` vagy `/mnt/myFolder`).

* Cserélje le `<storage-account-name>` a helyőrzőt a Storage-fiók nevére.

* Cserélje le `<container-name>` a helyőrzőt a létrehozott tároló nevére.

További információ a `sync` parancsról: [fájlok szinkronizálása](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Ütemezett feladat létrehozása

Létrehozhat olyan ütemezett vagy cron feladatot, amely egy AzCopy parancssori szkriptet futtat. A szkript adott időközönként azonosítja és feltölti az új helyszíni adatokat a felhőtárolóba.

Másolja az AzCopy-parancsot egy szövegszerkesztőbe. Frissítse az AzCopy-parancs paraméterértékeit a megfelelő értékekre. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) néven az AzCopyhoz. 

Ezek a példák feltételezik `myFolder`, hogy a mappa neve, a Storage `mystorageaccount` `mycontainer`-fiók neve, a tároló neve pedig.

> [!NOTE]
> A Linux-példa egy SAS-tokent fűz hozzá. Meg kell adnia egyet a parancsban. Az AzCopy v10 jelenlegi verziója nem támogatja az Azure AD-hitelesítést a cron-feladatokban.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

Ebben az oktatóanyagban a [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) eszközt használjuk egy ütemezett feladat létrehozásához Windows rendszeren, és a [Crontab](http://crontab.org/) parancsot egy cron feladat létrehozásához Linux rendszeren.

 A **Schtasks** lehetővé teszi a rendszergazdák számára ütemezett feladatok létrehozását, törlését, lekérdezését, módosítását, futtatását és befejezését helyi vagy távoli számítógépeken. A **Cron** lehetővé teszi a Linux- és Unix-felhasználók számára parancsok vagy szkriptek futtatását adott dátumon és időpontban [cron-kifejezések](https://en.wikipedia.org/wiki/Cron#CRON_expression) használatával.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Cron feladat létrehozásához Linux rendszeren írja be az alábbi parancsot egy terminálon:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Ha a parancsban megadja az `*/5 * * * *` cron-kifejezést, az azt jelenti, hogy a `script.sh` héjszkriptnek öt percenként kell futnia. A szkriptet ütemezheti úgy, hogy adott időben fusson naponta, havonta vagy évente. Ha többet szeretne megtudni a feladatok végrehajtási dátumának és idejének megadásáról, tekintse át a [cron-kifejezéseket](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Ha ütemezett feladatot szeretne létrehozni Windows rendszeren, írja be a következő parancsot a parancssorba vagy a PowerShellbe:

Ez a példa feltételezi, hogy a parancsfájl a számítógép legfelső szintű meghajtóján található, de a szkript bárhol lehet.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

A parancs a következő paramétereket használja:
- Az `/SC` paramétert a percenkénti ütemezés megadásához.
- Az `/MO` paramétert öt perces intervallumok meghatározásához.
- A `/TN` paramétert a feladat nevének megadásához.
- A `/TR` paramétert a `script.bat` fájl elérési útjának megadásához.

További információk az ütemezett feladatok létrehozásáról Windows rendszeren: [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Ha ellenőrizni szeretné, hogy az ütemezett/cron feladat megfelelően fut-e, hozzon létre új fájlokat a `myFolder` könyvtárban. Várjon öt percet, és győződjön meg arról, hogy az új fájlok feltöltődtek a tárfiókjába. Navigáljon a naplókönyvtárhoz, és tekintse meg az ütemezett vagy a cron feladat kimeneti naplóit.

## <a name="next-steps"></a>További lépések

A helyszíni adatok Azure Storage-ba, vagy a Storage-adatok helyszíni tárolóba történő áthelyezésével kapcsolatos további információkért kövesse az alábbi hivatkozást:

* [Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

A AzCopy kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

* [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

* [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

* [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)
 
* [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
