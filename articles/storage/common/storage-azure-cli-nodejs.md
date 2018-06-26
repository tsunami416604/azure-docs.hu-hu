---
title: Az Azure parancssori felület 1.0 használata az Azure Storage |} Microsoft Docs
description: Megtudhatja, hogyan használható az Azure parancssori felület (CLI) 1.0 az Azure Storage létrehozása és a storage-fiókok kezelése és a munkahelyi Azure-blobokat és fájlokat. Az Azure parancssori felület egy olyan többplatformos eszköz
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: 1e2c2dc45f2cb91ca225bf183ddde5fa2d1123c4
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936989"
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Az Azure parancssori felület 1.0 használata az Azure Storage szolgáltatással

## <a name="overview"></a>Áttekintés

Az Azure parancssori felület számos nyílt forráskódú, platformok közötti parancsok végzett munka az Azure platformra. Nagy része megtalálható ugyanezeket a funkciókat biztosít a [Azure-portálon](https://portal.azure.com) , valamint a gazdag adat-hozzáférési funkciókat.

Ez az útmutató azt fogja feltárja használata [Azure parancssori felület (CLI)](../../cli-install-nodejs.md) különböző az Azure Storage fejlesztői és felügyeleti feladatok elvégzéséhez. Azt javasoljuk, hogy letölteni és telepíteni vagy frissíteni az Azure CLI legújabb az útmutató használata előtt.

Ez az útmutató feltételezi, hogy tudomásul veszi az Azure Storage alapvető fogalmait. Az útmutató számos parancsfájlt az használatát, az Azure parancssori felület az Azure Storage bemutatása. Ne felejtse el frissíteni a parancsfájl-változókat, minden parancsprogram futtatása előtt a konfiguráció alapján.

> [!NOTE]
> Az útmutatóban példákat az Azure CLI parancs és a parancsfájl klasszikus tárfiókokat. Lásd: [használata az Azure parancssori felület Mac, Linux és Windows Azure Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) az erőforrás-kezelő storage-fiókok Azure parancssori felület parancsait.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Ismerkedés az Azure Storage és az Azure parancssori felület 5 perc
Ez az útmutató Ubuntu használja példák, de más operációs Rendszeri platformokon hasonló módon végre kell hajtania.

**Új Azure-bA:** a Microsoft Azure-előfizetés és az adott előfizetéshez tartozó Microsoft-fiókkal. Az Azure megvásárlási lehetőségeinek információkért lásd: [ingyenes](https://azure.microsoft.com/pricing/free-trial/), [beszerzési lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), és [ajánlatok](https://azure.microsoft.com/pricing/member-offers/) (MSDN, a Microsoft Partner Network, és a BizSpark, tagok és más Microsoft-programokat).

Lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) Azure-előfizetések további információt.

**Miután létrehozta a Microsoft Azure-előfizetésre és -fiókra:**

1. Töltse le és telepítse az Azure parancssori felület ismertetett lépéseket követve [az Azure parancssori felület telepítése](../../cli-install-nodejs.md).
2. Az Azure parancssori felület telepítése után tudnak, az azure parancsot a parancssori felület (Bash, terminál, parancssor) az Azure parancssori felület parancsait elérésére használhat. Típus a _azure_ parancsot, és a következő kimenetnek kell megjelennie.

    ![Az Azure parancs kimenete](./media/storage-azure-cli/azure_command.png)   
3. Írja be a parancssori felület `azure storage` biztosít az azure storage-parancsok listában, és a funkciók első benyomást az Azure parancssori felület. Beírhatja a parancsnév **-h** paraméter (például `azure storage share create -h`) parancs szintaxisa a részletek megtekintéséhez.
4. Most lesz ad egy egyszerű parancsprogram, amely az Azure Storage eléréséhez alapszintű Azure parancssori felület parancsait tartalmazza. A parancsfájl először ekkor megkérdezi, hogy a tárfiók és a kulcs két változók megadása. Ezt követően a parancsfájl hozzon létre egy új tárolót az új tárfiók, és töltse fel a meglévő képfájl (blob) a tárolóban. Miután a parancsfájl az összes BLOB a tárolóban sorolja fel, akkor a célkönyvtárat, amely létezik a helyi számítógépen a képfájl letölti.

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

5. A helyi számítógépen nyissa meg az előnyben részesített szövegszerkesztőben (például vim). A fenti szkript írja be a szövegszerkesztőben.
6. Most módosítania a parancsfájl-változókat a konfigurációs beállítások alapján.

   * **< Storage_account_name >** használja a parancsfájl a megadott névvel, vagy adjon meg egy új nevet a tárfiók. **Fontos:** a tárfiók nevét az Azure-ban egyedinek kell lennie. Az kisbetűnek kell lennie, túl!
   * **< Storage_account_key >** a tárfiók hozzáférési kulcsának.
   * **< Container_name >** használja a parancsfájl a megadott névvel, vagy adjon meg egy új nevet a tároló.
   * **< Image_to_upload >** adja meg egy elérési utat kép a helyi számítógépen, például: "~ / images/HelloWorld.png".
   * **< Destination_folder >** meg elérési útját a helyi könyvtárat, többek között az Azure Storage-ból letöltött fájlok tárolására szolgáló: "~/downloadImages".
7. A szükséges változók vim frissítése után nyomja le az billentyűkombinációk `ESC`, `:`, `wq!` a parancsfájl mentése.
8. Futtassa ezt a parancsfájlt, egyszerűen csak írja be a parancsfájl nevét a bash konzolon. Ez a parancsfájl futtatása után rendelkeznie kell egy helyi célmappát, amely tartalmazza a letöltött lemezképfájlt. Az alábbi képernyőfelvételen látható egy példa a kimenetre:

A parancsfájl futtatása után rendelkeznie kell egy helyi célmappát, amely tartalmazza a letöltött lemezképfájlt.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Az Azure parancssori felülettel storage-fiókok kezelése
### <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez
A legtöbb a storage parancsok Azure-előfizetés nélkül fog működni, amíg azt javasoljuk, hogy az előfizetés csatlakozni az Azure parancssori felületen. Az előfizetés használható az Azure parancssori felület konfigurálásához kövesse a lépéseket [csatlakozás Azure-előfizetéshez az Azure parancssori felületen](/cli/azure/authenticate-azure-cli).

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure storage használatához szüksége lesz egy tárfiókot. Egy új Azure-tárfiókot is létrehozhat, a számítógép csatlakozni az előfizetés konfigurálása után.

```azurecli
azure storage account create <account_name>
```

A tárfiók nevét kell 3 és 24 karakter hosszúságúnak és kell használnia csak számokat és kisbetűket tartalmazhatnak.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Környezeti változók alapértelmezett Azure storage-fiók beállítása
Az előfizetés több tárfiókot is lehet. Válasszon egyet közülük, és állítsa be a környezeti változók a tárolási parancs összes ugyanabban a munkamenetben. Ez lehetővé teszi a tárolási Azure parancssori felület parancsait a storage-fiók megadása nélkül futtatja, és a kulcsok explicit módon.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Egy másik módja egy alapértelmezett tárfiókot használja a kapcsolati karakterlánc. Először hozza ki a kapcsolati karakterlánc parancsot:

```azurecli
azure storage account connectionstring show <account_name>
```

Ezután másolja a kimeneti kapcsolati karakterláncot, és állítsa az értékét a környezeti változó:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Hozzon létre és blobok kezelése
Az Azure Blob storage egy olyan szolgáltatás nagy mennyiségű strukturálatlan adatok, például szövegek vagy bináris adatok, hozzáfér a bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához. Ez a szakasz feltételezi, hogy Ön már ismeri az Azure Blob storage fogalmakat. Részletes információkért lásd: [az Azure Blob storage .NET használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md) és [Blob szolgáltatással kapcsolatos fogalmak](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure storage összes blobjának egy tárolóban kell lennie. Létrehozhat egy személyes tárolót használja a `azure storage container create` parancs:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> A névtelen olvasási hozzáférés három szintje van: **ki**, **Blob**, és **tároló**. Blobok névtelen hozzáférés érdekében a engedély paraméter értéke **ki**. Alapértelmezés szerint az új tároló privát, és csak a fiók tulajdonosa férhet. A névtelen felhasználók engedélyezése nyilvános olvasási hozzáférés a blob-erőforrásokhoz, de nem csomagtároló metaadatai vagy a tárolóban lévő blobok listájának értékre az engedély paraméter **Blob**. A blob-erőforrások, tároló metaadatait és a tárolóban lévő blobok listájának teljes nyilvános olvasási hozzáférés engedélyezéséhez a engedély paraméter értéke **tároló**. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.
>
>

### <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat. További információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).

A blobok feltöltése tárolót, használhatja a `azure storage blob upload`. Alapértelmezés szerint ez a parancs a helyi fájlok feltöltése blokkblobba. Adja meg a BLOB, használhatja a `--blobtype` paraméter.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Blobok letöltése a tárolóból
A következő példa bemutatja, hogyan töltheti le blobok egy tárolóba.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blobok másolása
Tárfiókokon és régiókon belül vagy azok között aszinkron módon másolhatja át a blobokat.

Az alábbi példa azt mutatja be, hogyan másolhat át blobokat egyik tárfiókból a másikba. Ez a példa azt létrehozni egy tárolót, amelyben blobokat nyilvánosan, is névtelenül érhető el.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Ez a minta egy aszinkron másolatot hajt végre. Minden egyes másolási művelet állapotának futtassa a figyelheti a `azure storage blob copy show` műveletet.

Vegye figyelembe, hogy a forrás URL-címe a másolási művelet előírt kell nyilvánosan hozzáférhető vagy egy (közös hozzáférésű jogosultságkód) SAS-jogkivonatot tartalmazza.

### <a name="delete-a-blob"></a>Blob törlése
Egy blob törléséhez használja az alábbi parancsot:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Hozzon létre és fájlmegosztások kezelése
Az Azure Files a szabványos SMB-protokollt használó alkalmazások esetében közös tárterületet biztosít. A Microsoft Azure virtuális gépek és felhőszolgáltatások, valamint a helyszíni alkalmazások megosztott csatlakoztatott megosztásokon keresztül. A fájlmegosztások és a fájladatok az Azure parancssori felület használatával kezelheti. Azure fájlokon további információkért lásd: [Bevezetés az Azure Fileshoz](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az Azure fájlmegosztások SMB-fájlmegosztás az Azure-ban. Minden könyvtárak és fájlok fájlmegosztást kell létrehozni. Egy fiók korlátlan számú megosztást tartalmazhat, és a megosztás tud tárolni a fájlokat, a tárfiók a kapacitás határértékekig korlátlan számú. Az alábbi példa létrehoz egy nevű fájlmegosztás **megosztás**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár egy választható hierarchikus struktúra biztosít az Azure fájlmegosztások. Az alábbi példa létrehoz egy könyvtárat nevű **könyvtárnév** a fájlmegosztásban.

```azurecli
azure storage directory create myshare myDir
```

Vegye figyelembe, hogy a könyvtár elérési útja tartalmazhatnak több szintjéről *pl.*, **a / b**. Azonban úgy kell beállítania, hogy létezik-e az összes fölérendelt könyvtárak. Például a következő elérési út **a / b**, létre kell hoznia könyvtárat **egy** először, majd hozza létre a könyvtár **b**.

### <a name="upload-a-local-file-to-directory"></a>Helyi fájl feltöltése a könyvtárba
Az alábbi példa feltölt egy fájlt a **~/temp/samplefile.txt** számára a **könyvtárnév** könyvtár. Szerkessze a fájl elérési útját, hogy a helyi számítógépen érvényes fájlba:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Vegye figyelembe, hogy egy, a megosztásban található fájl akár 1 TB méretű lehet.

### <a name="list-the-files-in-the-share-root-or-directory"></a>A megosztás legfelső szintű vagy a könyvtár található fájlok listázása
A fájlok és alkönyvtárak megosztás legfelső szintű vagy egy könyvtárat az alábbi parancs segítségével jeleníthetők meg:

```azurecli
azure storage file list myshare myDir
```

Ne feledje, hogy a könyvtár nevét kötelező megadni a listázási művelet. Ha nincs megadva, a parancs a gyökérkönyvtár a megosztás tartalmát jeleníti meg.

### <a name="copy-files"></a>Fájlok másolása
Azure CLI 0.9.8-as verzióját kezdve, hogy másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Az alábbiakban bemutatjuk, hogyan hajthat végre ezeket a másolási műveleteket CLI-parancsok használatával. Fájl másolása az új mappába:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

A blob másolása fájl könyvtár:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>További lépések

Azure CLI 1.0 parancsdokumentációja találhat itt tároló-erőforrások használata:

* [Az Azure parancssori felület parancsait erőforrás-kezelő módban](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Az Azure parancssori felület parancsait Azure szolgáltatásfelügyelet módban](../../cli-install-nodejs.md)

Előfordulhat, hogy is szeretné, hogy próbálja meg a [Azure CLI 2.0](../storage-azure-cli.md), a következő generációs CLI a Resource Manager üzembe helyezési modellben való használatra, pythonban írt.
