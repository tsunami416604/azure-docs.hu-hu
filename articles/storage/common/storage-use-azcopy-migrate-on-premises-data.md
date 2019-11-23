---
title: 'Tutorial: Migrate on-premises data to Azure Storage with AzCopy| Microsoft Docs'
description: Ebben az oktatóanyagban az AzCopyval fog adatokat migrálni vagy másolni blob-, tábla- és fájltartalmakból és -tartalmakba. Könnyedén migrálhatja adatait egy helyszíni tárolóból az Azure Storage-ba.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327517"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrate on-premises data to cloud storage with AzCopy

Az AzCopy egy olyan parancssori eszköz, amely lehetővé teszi, hogy egyszerű parancsokkal másoljon adatokat az Azure Blob Storage-ba, az Azure Files-ba és az Azure Table Storage-ba, illetve ezekből a tárolókból máshová. A parancsok úgy lettek kialakítva, hogy optimális teljesítményt nyújtsanak. Az AzCopyval az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja. Az AzCopyval helyi (helyszíni) adattárolóból tárfiókba másolhat adatokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók létrehozása. 
> * Minden adat feltöltése az AzCopy segítségével.
> * Az adatok módosítása tesztelési célból.
> * Ütemezett vagy cron feladat létrehozása az új feltölthető fájlok azonosításához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

To complete this tutorial, download the latest version of AzCopy. See [Get started with AzCopy](storage-use-azcopy-v10.md).

Ha Windows rendszert használ, szüksége lesz a [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) programra, mert az oktatóanyag azt használja a feladatok ütemezéséhez. A Linux-felhasználók e célra a crontab parancsot használják.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az első lépés a tároló létrehozása, mert a blobokat mindig egy tárolóba kell feltölteni. A tárolók segítségével úgy történik a blobcsoportok rendszerezése, ahogy a fájljait rendezi mappákba a számítógépén.

Kövesse az alábbi lépéseket a tároló létrehozásához:

1. A főoldalon kattintson a **Tárfiókok** gombra, majd válassza ki a létrehozott tárfiókot.
2. A **Szolgáltatások** területen kattintson a **Blobok** elemre, majd válassza a **Tároló** lehetőséget.

   ![Tároló létrehozása](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolók nevének betűvel vagy számmal kell kezdődnie. A név csak betűkből, számokból és kötőjel (-) karakterből állhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="download-azcopy"></a>Download AzCopy

Download the AzCopy V10 executable file.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Place the AzCopy file anywhere on your computer. Add the location of the file to your system path variable so that you can refer to this executable file from any folder on your computer.

## <a name="authenticate-with-azure-ad"></a>Hitelesítés az Azure AD-vel

First, assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) role to your identity. See [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Then, open a command prompt, type the following command, and press the ENTER key.

```azcopy
azcopy login
```

This command returns an authentication code and the URL of a website. Open the website, provide the code, and then choose the **Next** button.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

A sign-in window will appear. In that window, sign into your Azure account by using your Azure account credentials. After you've successfully signed in, you can close the browser window and begin using AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Egy mappa tartalmának feltöltése a Blob Storage-ba

Az AzCopyval feltöltheti egy mappa összes fájlját a Blob Storage-ba [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`).

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To upload the contents of the specified directory to Blob storage recursively, specify the `--recursive` option. When you run AzCopy with this option, all subfolders and their files are uploaded as well.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob Storage-ba

You can use AzCopy to upload files based on their last-modified time. 

Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Then, use the AzCopy `sync` command.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`.

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To learn more about the `sync` command, see [Synchronize files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Ütemezett feladat létrehozása

Létrehozhat olyan ütemezett vagy cron feladatot, amely egy AzCopy parancssori szkriptet futtat. A szkript adott időközönként azonosítja és feltölti az új helyszíni adatokat a felhőtárolóba.

Másolja az AzCopy-parancsot egy szövegszerkesztőbe. Frissítse az AzCopy-parancs paraméterértékeit a megfelelő értékekre. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) néven az AzCopyhoz. 

These examples assume that your folder is named `myFolder`, your storage account name is `mystorageaccount` and your container name is `mycontainer`.

> [!NOTE]
> The Linux example appends a SAS token. You'll need to provide one in your command. The current version of AzCopy V10 doesn't support Azure AD authorization in cron jobs.

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

This example assumes that your script is located in the root drive of your computer, but your script can be anywhere that you want.

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

## <a name="next-steps"></a>Következő lépések

A helyszíni adatok Azure Storage-ba, vagy a Storage-adatok helyszíni tárolóba történő áthelyezésével kapcsolatos további információkért kövesse az alábbi hivatkozást:

* [Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

For more information about AzCopy, see any of these articles:

* [Get started with AzCopy](storage-use-azcopy-v10.md)

* [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)

* [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

* [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)
