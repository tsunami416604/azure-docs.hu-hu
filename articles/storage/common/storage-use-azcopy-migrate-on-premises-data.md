---
title: 'Oktatóanyag: Helyszíni adatok migrálása az Azure Storage-ba az AzCopyval | Microsoft Docs'
description: Ebben az oktatóanyagban az AzCopyval fog adatokat migrálni vagy másolni blob-, tábla- és fájltartalmakból és -tartalmakba. Könnyedén migrálhatja adatait egy helyszíni tárolóból az Azure Storage-ba.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 64d79abd1e142a231c08e02e7d62e8bfbab7b90e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244738"
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

Az oktatóanyag elvégzéséhez töltse le az AzCopy legújabb verzióját. Lásd: [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md).

Ha Windows rendszert használ, szüksége lesz a [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) programra, mert az oktatóanyag azt használja a feladatok ütemezéséhez. A Linux-felhasználók e célra a crontab parancsot használják.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az első lépés a tároló létrehozása, mert a blobokat mindig egy tárolóba kell feltölteni. A tárolók segítségével úgy történik a blobcsoportok rendszerezése, ahogy a fájljait rendezi mappákba a számítógépén.

Kövesse az alábbi lépéseket a tároló létrehozásához:

1. A főoldalon kattintson a **Tárfiókok** gombra, majd válassza ki a létrehozott tárfiókot.
2. A **Szolgáltatások** területen kattintson a **Blobok** elemre, majd válassza a **Tároló** lehetőséget.

   ![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolók nevének betűvel vagy számmal kell kezdődnie. A név csak betűkből, számokból és kötőjel (-) karakterből állhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="download-azcopy"></a>Töltse le az AzCopy

Töltse le az AzCopy V10 végrehajtható fájlt.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Helyezze az AzCopy fájlt a számítógépen található. Adja hozzá a fájl helyét a rendszer path változóban, hogy olvassa el ezt a végrehajtható fájlt minden olyan mappából a számítógépen.

## <a name="authenticate-with-azure-ad"></a>Hitelesítés az Azure AD-vel

Első lépésként hozzárendelése a [Storage-Blobadatok Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) identitásához szerepkör. Lásd: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Ezután nyisson meg egy parancssort, írja be a következő parancsot, és nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ez a parancs visszaadja a hitelesítési kódot és a egy webhely URL-CÍMÉT. Nyissa meg a webhelyet, írja be a kódot, és válassza a **tovább** gombra.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

A bejelentkezési ablak jelenik meg. Ezt az ablakot jelentkezzen be Azure-fiókjába az Azure-fiók hitelesítő adataival. Miután sikeresen bejelentkezett, zárja be a böngészőablakot, és megkezdheti az AzCopy használatával.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Egy mappa tartalmának feltöltése a Blob Storage-ba

Az AzCopyval feltöltheti egy mappa összes fájlját a Blob Storage-ba [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le a `<local-folder-path>` helyőrzőt a fájlokat tartalmazó mappa elérési útja (Példa: `C:\myFolder` vagy `/mnt/myFolder`).

* Cserélje le a `<storage-account-name>` helyőrzőt a tárfiók nevére.

* Cserélje le a `<container-name>` helyőrzőt az Ön által létrehozott tároló neve.

A megadott könyvtár tartalmának a Blob storage rekurzív feltöltéséhez adja meg a `--recursive` lehetőséget. AzCopy futtatja ezt a beállítást, ha minden almappa és a fájlok is feltöltődnek.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob Storage-ba

Az AzCopy segítségével töltse fel a fájlokat a legutóbbi módosításuk ideje alapján. 

Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ezután használja az AzCopy `sync` parancsot.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le a `<local-folder-path>` helyőrzőt a fájlokat tartalmazó mappa elérési útja (Példa: `C:\myFolder` vagy `/mnt/myFolder`.

* Cserélje le a `<storage-account-name>` helyőrzőt a tárfiók nevére.

* Cserélje le a `<container-name>` helyőrzőt az Ön által létrehozott tároló neve.

További információkat talál a `sync` paranccsal [szinkronizálhatja a fájljaikat](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Ütemezett feladat létrehozása

Létrehozhat olyan ütemezett vagy cron feladatot, amely egy AzCopy parancssori szkriptet futtat. A szkript adott időközönként azonosítja és feltölti az új helyszíni adatokat a felhőtárolóba.

Másolja az AzCopy-parancsot egy szövegszerkesztőbe. Frissítse az AzCopy-parancs paraméterértékeit a megfelelő értékekre. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) néven az AzCopyhoz. 

Ezek a példák feltételezik, hogy a mappa neve `myFolder`, a tárfiók neve `mystorageaccount` és a tároló neve `mycontainer`.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

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

Ez a példa feltételezi, hogy a parancsfájl a meghajtó gyökérkönyvtárára a számítógép található, de a parancsfájl bárhol lehet, hogy szeretné.

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

Az AzCopy kapcsolatos további információkért tekintse meg az alábbi cikkek valamelyikére:

* [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

* [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)

* [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)

* [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)
 
* [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)
