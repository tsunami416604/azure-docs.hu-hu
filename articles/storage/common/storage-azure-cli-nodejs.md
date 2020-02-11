---
title: A klasszikus Azure CLI használata az Azure Storage szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet Storage-fiókokat a klasszikus Azure parancssori felületen (CLI) az Azure Storage használatával, és hogyan dolgozhat az Azure-Blobokkal és-fájlokkal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120857"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>A klasszikus Azure CLI használata az Azure Storage-ban

## <a name="overview"></a>Áttekintés

A klasszikus Azure CLI nyílt forráskódú, platformfüggetlen parancsokat biztosít az Azure platformmal való munkavégzéshez. Ez a funkció számos funkciót tartalmaz a [Azure Portalban](https://portal.azure.com) , valamint a gazdag adathozzáférési funkciókat is.

Ebben az útmutatóban megismerheti, hogyan használható az [Azure klasszikus parancssori](../../cli-install-nodejs.md) felülete különböző fejlesztési és adminisztrációs feladatok végrehajtására az Azure Storage-ban. Javasoljuk, hogy az útmutató használata előtt töltse le és telepítse vagy frissítse a legújabb klasszikus CLI-t.

Ez az útmutató feltételezi, hogy ismeri az Azure Storage alapfogalmait. Az útmutató számos olyan szkriptet tartalmaz, amelyek bemutatják a klasszikus CLI és az Azure Storage használatát. Az egyes parancsfájlok futtatása előtt mindenképpen frissítse a parancsfájl-változókat a konfiguráció alapján.

> [!NOTE]
> Az útmutató a klasszikus Azure CLI-parancsot és parancsfájl-példákat biztosít a klasszikus Storage-fiókokhoz. Lásd: [a Mac, Linux és Windows rendszerhez készült Azure CLI használata](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) az Azure erőforrás-kezelési szolgáltatással a klasszikus Azure CLI-parancsokhoz Resource Manager-alapú tárolási fiókokhoz.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Ismerkedjen meg az Azure Storage szolgáltatással és a klasszikus Azure CLI-vel 5 perc alatt
Ez az útmutató példákon keresztül Ubuntut használ, de más operációsrendszer-platformoknak hasonlóképpen kell elvégezniük.

**Új az Azure-hoz:** Szerezze be az előfizetéshez tartozó Microsoft Azure-előfizetést és egy Microsoft-fiók. Az Azure vásárlási lehetőségeivel kapcsolatos további információkért lásd az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/), a [vásárlási lehetőségeket](https://azure.microsoft.com/pricing/purchase-options/)és a tagok [ajánlatait](https://azure.microsoft.com/pricing/member-offers/) (az MSDN, a Microsoft Partner Network és a BizSpark, valamint más Microsoft-programok tagjai számára).

Az Azure-előfizetésekkel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban (Azure ad)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) .

**Microsoft Azure előfizetés és fiók létrehozása után:**

1. Töltse le és telepítse a klasszikus Azure CLI-t a [klasszikus Azure CLI telepítése](../../cli-install-nodejs.md)című részben ismertetett utasítások alapján.
2. Miután telepítette a klasszikus CLI-t, használhatja az Azure-parancsot a parancssori felületről (bash, Terminal, parancssor) a klasszikus CLI-parancsok eléréséhez. Írja be az _Azure_ -parancsot, és a következő kimenetnek kell megjelennie.

    ![Azure-parancs kimenete](./media/storage-azure-cli/azure_command.png)   
3. A parancssori felületen írja be a `azure storage` parancsot az összes Azure Storage-parancs kilistázásához, és ismerkedjen meg a klasszikus CLI funkcióinak első benyomásával. Megadhatja a parancs nevét a **-h** paraméterrel (például `azure storage share create -h`), hogy megtekintse a parancs szintaxisának részleteit.
4. Most egy egyszerű szkriptet adunk meg, amely az alapszintű klasszikus CLI-parancsokat mutatja be az Azure Storage eléréséhez. A szkript először megkéri, hogy állítson be két változót a Storage-fiókjához és a kulcshoz. Ezután a szkript létrehoz egy új tárolót az új Storage-fiókban, és feltölt egy meglévő képfájlt (blobot) a tárolóba. Miután a parancsfájl felsorolja az adott tárolóban lévő összes blobot, letölti a lemezképfájlt a helyi számítógépen található célkiszolgálóra.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. A helyi számítógépen nyissa meg a kívánt szövegszerkesztőt (például: Vim). Írja be a fenti szkriptet a szövegszerkesztőbe.
6. Most frissítenie kell a parancsfájl-változókat a konfigurációs beállítások alapján.

   * **< storage_account_name >** Használja a megadott nevet a parancsfájlban, vagy adjon meg egy új nevet a Storage-fiókhoz. **Fontos:** A Storage-fiók nevének egyedinek kell lennie az Azure-ban. Kisbetűnek kell lennie.
   * **< storage_account_key >** A Storage-fiók hozzáférési kulcsa.
   * **< container_name >** Használja a megadott nevet a parancsfájlban, vagy adjon meg egy új nevet a tárolónak.
   * **< image_to_upload >** Adja meg a helyi számítógépen található kép elérési útját, például: "~/images/HelloWorld.png".
   * **< destination_folder >** Adja meg az Azure Storage-ból letöltött fájlok tárolására szolgáló helyi könyvtár elérési útját, például: "~/downloadImages".
7. Miután frissítette a szükséges változókat a Vim-ben, nyomja le a billentyűkombinációkat `ESC`, `:``wq!` a parancsfájl mentéséhez.
8. A szkript futtatásához egyszerűen írja be a parancsfájl nevét a bash konzolon. A szkript futtatása után olyan helyi célmappával kell rendelkeznie, amely tartalmazza a letöltött képfájl fájlját. Az alábbi képernyőképen egy példa kimenet látható:

A szkript futtatása után olyan helyi célmappával kell rendelkeznie, amely tartalmazza a letöltött képfájl fájlját.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Storage-fiókok kezelése a klasszikus Azure CLI-vel
### <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez
Habár a tárolási parancsok többsége Azure-előfizetés nélkül is működik, javasoljuk, hogy a klasszikus CLI-vel kapcsolódjon az előfizetéséhez.

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure Storage használatához szüksége lesz egy Storage-fiókra. Új Azure Storage-fiókot is létrehozhat, miután konfigurálta a számítógépet az előfizetéshez való kapcsolódáshoz.

```azurecli
azure storage account create <account_name>
```

A Storage-fiók nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Alapértelmezett Azure Storage-fiók beállítása környezeti változókban
Az előfizetéshez több Storage-fiók is tartozhat. Kiválaszthatja az egyiket, és beállíthatja az adott munkamenetben lévő összes tárolási parancs környezeti változóit. Ez lehetővé teszi a klasszikus CLI tárolási parancsok futtatását anélkül, hogy explicit módon megadta a Storage-fiókot és a kulcsot.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Az alapértelmezett Storage-fiók beállításának másik módja a kapcsolódási karakterlánc használata. Először kérje le a (z) paranccsal a kapcsolatok karakterláncát:

```azurecli
azure storage account connectionstring show <account_name>
```

Ezután másolja a kimeneti kapcsolódási karakterláncot, és állítsa a környezeti változóra:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Blobok létrehozása és kezelése
Az Azure Blob Storage egy olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adat, például szöveges vagy bináris adatok tárolására szolgál, amelyek HTTP-vagy HTTPS-kapcsolaton keresztül bárhonnan elérhetők a világ bármely pontján. Ez a szakasz azt feltételezi, hogy már ismeri az Azure Blob Storage-fogalmakat. Részletes információkért lásd: [Az Azure Blob Storage használatának első lépései a .net](../blobs/storage-dotnet-how-to-use-blobs.md) -és [blob-szolgáltatásokkal kapcsolatos fogalmakkal](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure Storage-ban minden blobnak tárolóban kell lennie. A `azure storage container create` parancs használatával létrehozhat egy privát tárolót:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> A névtelen olvasási hozzáférésnek három szintje van: **off**, **blob**és **tároló**. Ha meg szeretné akadályozni, hogy a Blobok névtelenül hozzáférjenek, állítsa **ki**az engedélyezési paramétert. Alapértelmezés szerint az új tároló magánjellegű, és csak a fiók tulajdonosa érheti el. Ha engedélyezni szeretné a névtelen nyilvános olvasási hozzáférést a blob erőforrásaihoz, de nem a tároló metaadatainak vagy a tárolóban lévő Blobok listájához, állítsa az engedélyezési paramétert a **blob**értékre. Ha engedélyezni szeretné a teljes nyilvános olvasási hozzáférést a blob erőforrásaihoz, a tároló metaadataihoz és a tárolóban lévő Blobok listájához, állítsa az engedélyezési paramétert **tároló**értékre. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.
>
>

### <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat. További információkért lásd: a [Blobok, a blobok és a Blobok hozzáfűzésének ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx).

A Blobok tárolóba való feltöltéséhez használhatja a `azure storage blob upload`. Alapértelmezés szerint a parancs a helyi fájlokat egy blokk blobba tölti fel. A blob típusának megadásához a `--blobtype` paramétert használhatja.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Blobok letöltése tárolóból
Az alábbi példa bemutatja, hogyan tölthetők le a Blobok egy tárolóból.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blobok másolása
Tárfiókokon és régiókon belül vagy azok között aszinkron módon másolhatja át a blobokat.

Az alábbi példa azt mutatja be, hogyan másolhat át blobokat egyik tárfiókból a másikba. Ebben a példában egy olyan tárolót hozunk létre, amelyben a Blobok nyilvánosan, névtelenül elérhetők.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Ez a minta aszinkron másolatot hajt végre. Az egyes másolási műveletek állapotát a `azure storage blob copy show` művelet futtatásával figyelheti.

Vegye figyelembe, hogy a másolási művelethez megadott forrás URL-címnek nyilvánosan elérhetőnek kell lennie, vagy tartalmaznia kell a SAS (közös hozzáférésű aláírás) jogkivonatot.

### <a name="delete-a-blob"></a>Blob törlése
BLOB törléséhez használja az alábbi parancsot:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Fájlmegosztás létrehozása és kezelése
A Azure Files a szabványos SMB protokollt használó alkalmazások számára biztosít megosztott tárterületet. Microsoft Azure a virtuális gépek és a Cloud Services, valamint a helyszíni alkalmazások is megoszthatják a fájlokat a csatlakoztatott megosztásokon keresztül. A fájlmegosztást és a fájlok adatfájljait a klasszikus CLI-n keresztül kezelheti. További információ a Azure Filesről: a [Azure Files bemutatása](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az Azure-fájlmegosztás egy SMB-fájlmegosztás az Azure-ban. Az összes könyvtárat és fájlt egy fájlmegosztási fájlban kell létrehozni. Egy fiók korlátlan számú megosztást tartalmazhat, és egy megosztás korlátlan számú fájlt tárolhat, a Storage-fiók kapacitásának korlátaival együtt. A következő példa egy **MyShare**nevű fájlmegosztást hoz létre.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár opcionális hierarchikus struktúrát biztosít az Azure-fájlmegosztás számára. A következő példában létrehozunk egy **könyvtárnév** nevű könyvtárat a fájlmegosztás számára.

```azurecli
azure storage directory create myshare myDir
```

Vegye figyelembe, hogy a könyvtár elérési útja több szintet is tartalmazhat, *például* **a/b**. Azonban győződjön meg arról, hogy az összes szülő-könyvtár létezik. Az **a/b**elérési útra vonatkozóan például először létre kell hoznia egy címtárat, majd létre kell hoznia **a** **b**könyvtárat.

### <a name="upload-a-local-file-to-directory"></a>Helyi fájl feltöltése a könyvtárba
A következő példa egy fájlt tölt fel a **~/temp/samplefile.txt** -ből a **könyvtárnév** könyvtárba. Szerkessze a fájl elérési útját úgy, hogy az egy érvényes fájlra mutasson a helyi gépen:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Vegye figyelembe, hogy a megosztásban lévő fájlok mérete akár 1 TB is lehet.

### <a name="list-the-files-in-the-share-root-or-directory"></a>A megosztás gyökerében vagy könyvtárában található fájlok listázása
A megosztási gyökérben vagy a címtárban lévő fájlokat és alkönyvtárakat a következő parancs használatával listázhatja:

```azurecli
azure storage file list myshare myDir
```

Vegye figyelembe, hogy a címtár neve nem kötelező a listázási művelethez. Ha nincs megadva, a parancs felsorolja a megosztás gyökérkönyvtárának tartalmát.

### <a name="copy-files"></a>Fájlok másolása
A klasszikus CLI verziójának 0.9.8 kezdve egy fájlt másolhat egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Alább bemutatjuk, hogyan hajthatja végre ezeket a másolási műveleteket a CLI-parancsokkal. Fájl másolása az új könyvtárba:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

BLOB másolása egy fájl könyvtárába:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>További lépések

Az Azure-beli klasszikus CLI-parancsok a tárolási erőforrások használatáról itt találhatók:

* [Azure-beli klasszikus CLI-parancsok Resource Manager módban](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Azure-beli klasszikus CLI-parancsok az Azure Service Management módban](../../cli-install-nodejs.md)

Az [Azure CLI](../storage-azure-cli.md)legújabb verzióját is használhatja a Resource Manager-alapú üzemi modellel való használatra.
