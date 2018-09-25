---
title: A klasszikus Azure CLI használatával az Azure Storage |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure klasszikus parancssori felület (CLI) az Azure Storage létrehozása és a storage-fiókok kezelése és használata Azure-blobok és fájlok.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: e563c7000b600bed917f42d8ffb87df883564ef8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979328"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Az Azure Storage az Azure klasszikus parancssori felület használatával

## <a name="overview"></a>Áttekintés

Az Azure klasszikus parancssori felület felülete nyílt forráskódú, platformfüggetlen parancsokat biztosít az Azure Platform használatához. Nagy része megtalálható ugyanazokat a funkciókat biztosít a [az Azure portal](https://portal.azure.com) , valamint gazdag adat-hozzáférési funkcióit.

Ez az útmutató azt vizsgáljuk használata [Azure klasszikus parancssori felület](../../cli-install-nodejs.md) fejlesztési és felügyeleti feladatokat az Azure Storage számos végrehajtásához. Azt javasoljuk, hogy letöltése és telepítése vagy frissítése a legújabb klasszikus parancssori felület az útmutató használata előtt.

Ez az útmutató feltételezi, hogy ismeri az Azure Storage az alapvető fogalmait. Az útmutató számos olyan parancsprogramok használatával a klasszikus parancssori felület az Azure Storage használatát mutatja be. Ügyeljen arra, hogy frissítse a parancsfájl-változókat minden parancsprogram futtatása előtt a konfiguráció alapján.

> [!NOTE]
> Útmutató a példákat az Azure klasszikus parancssori felület parancs és a parancsfájl a klasszikus tárfiókokkal. Lásd: [Mac, Linux és Windows, az Azure Felhőerőforrás-kezelés az Azure CLI használatával](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) az Azure klasszikus parancssori felület parancsai Resource Manager-tárfiókok esetében.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Ismerkedés az Azure Storage és az Azure klasszikus parancssori felület kipróbálása
Ez az útmutató Ubuntu használja példák, de más platformokon, az operációs rendszer hasonlóképpen végre kell hajtania.

**Új Azure-bA:** egy Microsoft Azure-előfizetés és a egy adott előfizetéshez hozzárendelt Microsoft-fiókot. Az Azure vásárlási lehetőségeket ismertető további információkért lásd: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/), [beszerzési lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), és [ajánlatok tagoknak](https://azure.microsoft.com/pricing/member-offers/) (MSDN, Microsoft Partner Network és BizSpark,-tagok számára, és egyéb Microsoft programok).

Lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) további információ az Azure-előfizetések.

**Miután létrehozta a Microsoft Azure-előfizetés és a fiók:**

1. Töltse le és telepítse az Azure klasszikus parancssori leírt utasítások [a klasszikus Azure CLI telepítése](../../cli-install-nodejs.md).
2. A klasszikus parancssori felület telepítése után fogja tudni használni az azure-parancs a parancssori felületről (Bash, terminál, parancssor használatával) a klasszikus parancssori felület parancsai eléréséhez. Írja be a _azure_ parancsot, és a következő kimenetet kell látnia.

    ![Az Azure parancs kimenete](./media/storage-azure-cli/azure_command.png)   
3. Írja be a parancssori felület `azure storage` ki az azure storage-parancsok listázása és a egy első benyomást az funkciók lekérése a klasszikus parancssori felület biztosít. Írja be a parancsnév **-h** paraméter (például `azure storage share create -h`) parancs szintaxisát részleteinek megtekintéséhez.
4. Most adjunk meg egy egyszerű szkript, amely az Azure Storage eléréséhez alapszintű klasszikus parancssori felület parancsait tartalmazza. A szkript először rákérdez, hogy a storage-fiók és a kulcs két változókat. Ezt követően a parancsfájl hozzon létre egy új tárolót az új storage-fiókban, és meglévő képfájl (blob) feltöltése a tárolóba. Miután a parancsfájl listázza a tárolóban lévő összes BLOB, a cél könyvtárba, amely létezik a helyi számítógépen le fogja tölteni a képfájlt.

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

5. A helyi számítógépen nyissa meg az előnyben részesített szövegszerkesztőben (például vim). Írja be a fenti szkript a szövegszerkesztőbe.
6. Most akkor frissítenie kell a parancsfájl-változókat a konfigurációs beállítások alapján.

   * **< Tárfiók_neve >** használata a parancsfájl a megadott névvel, vagy adjon meg egy új nevet a tárfiók számára. **Fontos:** a tárfiók nevét az Azure-ban egyedinek kell lennie. Ez csak kisbetűket, túl!
   * **< Storage_account_key >** a tárfiók hozzáférési kulcsára.
   * **< Container_name >** használata a parancsfájl a megadott névvel, vagy adjon meg egy új nevet a tároló számára.
   * **< Image_to_upload >** egy elérési útját adja meg egy képet a helyi számítógépen, mint például: "~ / images/HelloWorld.png".
   * **< Destination_folder >** adjon meg útvonalat az Azure Storage, például a letöltött fájlok tárolásához egy helyi könyvtárba: "~/downloadImages".
7. A szükséges változók vim frissítése után nyomja le az billentyűkombinációk `ESC`, `:`, `wq!` a parancsfájl mentéséhez.
8. Ez a szkript futtatásához, egyszerűen írja be a parancsfájl nevét a bash-konzolon. Ez a szkript futtatása után a rendelkeznie kell egy helyi célmappát, amely a letöltött bináris fájl tartalmaz. Az alábbi képernyőképen látható egy példa a kimenetre:

A szkript futtatása után a rendelkeznie kell egy helyi célmappát, amely a letöltött bináris fájl tartalmaz.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>A klasszikus Azure CLI-vel a storage-fiókok kezelése
### <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez
A storage-parancsok a legtöbb Azure-előfizetés nélkül fog működni, amíg azt javasoljuk, hogy csatlakozzon az előfizetéséhez, a klasszikus parancssori felület.

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure storage használatához szüksége lesz egy storage-fiókot. Új Azure storage-fiókot hozhat létre, a számítógép csatlakozni az előfizetés konfigurálása után.

```azurecli
azure storage account create <account_name>
```

A tárfiók neve lehet 3 – 24 karakter közötti hosszúságúnak, és csak számokat és kisbetűket tartalmazhat.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Környezeti változók egy alapértelmezett Azure storage-fiók beállítása
Több tárfiókot is rendelkezik az előfizetésében. Válasszon egyet ezek közül, és beállíthatja a környezeti változókat a storage-parancsok a ugyanabban a munkamenetben. Ez lehetővé teszi, hogy a CLI-parancsokkal a klasszikus tárolása a storage-fiók megadása nélkül, és explicit módon kulcsát.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Kapcsolati karakterlánc egy másik módja egy alapértelmezett tárfiókot használja. Először kérje le a kapcsolati karakterláncot parancs által:

```azurecli
azure storage account connectionstring show <account_name>
```

Ezután másolja a kimeneti kapcsolati karakterláncot, és beállíthatja azt a környezeti változót:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Hozzon létre és kezelheti a blobokat
Az Azure Blob storage szolgáltatás nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatot, amely segítségével bárhonnan elérhetők HTTP- vagy HTTPS keresztül a világon tárolásához. Ez a szakasz azt feltételezi, hogy már ismeri az Azure Blob storage fogalmakat. Részletes információkért lásd: [.NET használatával az Azure Blob storage használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md) és [Blob szolgáltatással kapcsolatos fogalmak](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure BLOB storage összes blobjának egy tárolóban kell lennie. Privát tároló használatával is létrehozhat a `azure storage container create` parancsot:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> A névtelen olvasási hozzáférés három szinten: **ki**, **Blob**, és **tároló**. Hogy a névtelen hozzáférés a blobokhoz, állítsa a engedély paramétert **ki**. Alapértelmezés szerint az új tároló privát, és csak a fiók tulajdonosa úgy érhető el. A névtelen felhasználók engedélyezése nyilvános olvasási hozzáférés blob erőforrásokhoz, de nem tároló metaadatait vagy a tárolóban lévő blobok listáját az engedély paraméter értéke **Blob**. Ahhoz, hogy a teljes nyilvános olvasási hozzáférés a blob-erőforrások, a tároló metaadatait és a tárolóban lévő blobok listáját, az engedély paraméter értéke **tároló**. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.
>
>

### <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat. További információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).

A blobok feltöltése a tárolóba, használhatja a `azure storage blob upload`. Alapértelmezés szerint ez a parancs a helyi fájlokat egy blokkblobot tölt fel. Adja meg a BLOB, használhatja a `--blobtype` paraméter.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Blobok letöltése tárolóból
Az alábbi példa bemutatja, hogyan töltheti le blobok egy tárolóba.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blobok másolása
Tárfiókokon és régiókon belül vagy azok között aszinkron módon másolhatja át a blobokat.

Az alábbi példa azt mutatja be, hogyan másolhat át blobokat egyik tárfiókból a másikba. Ebben a példában létrehozunk egy tartalmazó tárolót blobokat nyilvánosan, névtelenül elérhető-e.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Ez a példa egy aszinkron példány hajt végre. Minden egyes másolási művelet állapotának futtatásával figyelheti a `azure storage blob copy show` műveletet.

Vegye figyelembe, hogy a forrás URL-címe a másolási művelet megadott kell vagy nyilvánosan hozzáférhető, vagy egy SAS (közös hozzáférésű jogosultságkód) jogkivonatot tartalmazza.

### <a name="delete-a-blob"></a>Blob törlése
A blob törléséhez használja az alábbi parancsot:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Hozzon létre, és a fájlmegosztások felügyelete
Az Azure Files a szabványos SMB protokollt használó alkalmazások közös tárhelyet kínál. A Microsoft Azure virtuális gépek és felhőszolgáltatások, valamint a helyszíni alkalmazásokat, oszthatnak meg keresztül csatlakoztatott megosztások. Fájlmegosztások és a fájladatok klasszikus parancssori felületén is kezelheti. További információ az Azure Files: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Azure-fájlmegosztások az SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak léteznie kell egy fájlmegosztásban. Egy fiók korlátlan számú megosztást tartalmazhat, és a egy megosztás korlátlan számú fájl, egészen a tárfiók kapacitásának korlátjáig tárolhatja. A következő példában létrehozunk egy nevű fájlmegosztást **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár egy nem kötelező hierarchikus struktúrát biztosít Azure-fájlmegosztások. A következő példában létrehozunk egy nevű könyvtárat **könyvtárnév** a fájlmegosztásban.

```azurecli
azure storage directory create myshare myDir
```

Vegye figyelembe, hogy a könyvtár elérési útja több szintet tartalmazhat *például*, **a / b**. Azonban biztosítania kell, hogy az összes szülőkönyvtár létezik. Elérési út esetében például **a / b**, létre kell hoznia a directory **egy** először, majd hozza létre a könyvtár **b**.

### <a name="upload-a-local-file-to-directory"></a>Helyi fájl feltöltése a könyvtárba
A következő példa feltölti a fájlt **~/temp/samplefile.txt** , a **könyvtárnév** könyvtár. Szerkessze a fájl elérési útját úgy, hogy a egy érvényes fájlt a helyi gépen mutat:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Vegye figyelembe, hogy a a megosztásban található fájl akár 1 TB méretű lehet.

### <a name="list-the-files-in-the-share-root-or-directory"></a>A megosztás legfelső szintű vagy a könyvtárban lévő fájlok listázása
A fájlok és alkönyvtárak a legfelső szintű megosztás vagy egy könyvtárat a következő paranccsal listázhatja:

```azurecli
azure storage file list myshare myDir
```

Vegye figyelembe, hogy a könyvtár neve a listázási művelet esetén nem kötelező. Ha nincs megadva, a parancs felsorolja a gyökérkönyvtár a megosztás tartalma.

### <a name="copy-files"></a>Fájlok másolása
A klasszikus CLI 0.9.8-as verzióját verziótól kezdve, másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Az alábbiakban bemutatjuk, hogyan hajthat végre ezeket a parancssori felület parancsait használva másolási műveleteket. Fájl másolása az új mappába:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Egy blob átmásolása egy fájl könyvtára:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>További lépések

Az Azure klasszikus parancssori felület parancsdokumentációja használata a tárolási erőforrások itt találja:

* [Resource Manager módban az Azure klasszikus parancssori felület parancsai](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure szolgáltatásfelügyelet módban az Azure klasszikus parancssori felület parancsai](../../cli-install-nodejs.md)

Szeretné kipróbálni a legújabb verzióját is érdekelheti a [Azure CLI-vel](../storage-azure-cli.md), a Resource Manager üzemi modellel történő használathoz.
