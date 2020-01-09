---
title: Az Azure CLI használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)
description: Az Azure CLI használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) a hierarchikus névtérrel rendelkező Storage-fiókokban.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 596f8334b647daf6fe3a15521f7caeecb0c0e303
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462604"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Könyvtárak, fájlok és ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2 (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet olyan könyvtárakat, fájlokat és engedélyeket a Storage-fiókokban, amelyek hierarchikus névteret használnak az [Azure parancssori felületének (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) használatával. 

> [!IMPORTANT]
> A cikkben Kiemelt `storage-preview` bővítmény jelenleg nyilvános előzetes verzióban érhető el.

[Példa](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 Gen2-leképezésre](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Az Azure CLI verziója `2.0.67` vagy újabb.

## <a name="install-the-storage-cli-extension"></a>A Storage CLI bővítmény telepítése

1. Nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI-verzió `2.0.67` vagy magasabb-e.

   ```azurecli
    az --version
   ```
   Ha az Azure CLI verziója alacsonyabb, mint `2.0.67`, telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. A(z) `storage-preview` bővítmény telepítése.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

1. Ha helyileg használja az Azure CLI-t, futtassa a login parancsot.

   ```azurecli
   az login
   ```

   Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

   Ellenkező esetben nyisson meg egy böngészőt a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) címen, és adja meg a terminálon megjelenő engedélyezési kódot. Ezután jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

   További információ a különböző hitelesítési módszerekről: bejelentkezés az Azure CLI-vel.

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. A `az storage container create` parancs használatával létrehozhat egyet. 

Ez a példa egy `my-file-system`nevű fájlrendszert hoz létre.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy könyvtárat a `az storage blob directory create` parancs használatával. 

Ez a példa egy `my-directory` nevű könyvtárat vesz fel egy `my-file-system` nevű fájlrendszerbe, amely egy `mystorageaccount`nevű fiókban található.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Könyvtár tulajdonságainak megjelenítése

A könyvtár tulajdonságait a `az storage blob show` parancs használatával kinyomtathatja a konzolra.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `az storage blob directory move` parancs használatával.

Ez a példa átnevez egy könyvtárat a név `my-directory` a név `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a `az storage blob directory delete` parancs használatával.

Ez a példa töröl egy `my-directory`nevű könyvtárat. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Annak ellenőrzése, hogy létezik-e könyvtár

Annak megállapítása, hogy egy adott könyvtár létezik-e a fájlrendszerben a `az storage blob directory exist` parancs használatával.

Ez a példa azt mutatja, hogy egy `my-directory` nevű könyvtár létezik-e a `my-file-system` fájlrendszerben. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `az storage blob directory download` parancs használatával.

Ez a példa egy `my-directory`nevű könyvtárból tölt le egy `upload.txt` nevű fájlt. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Ez a példa egy teljes könyvtárat tölt le.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a `az storage blob directory list` parancs használatával.

Ez a példa egy `my-directory` nevű könyvtár tartalmát sorolja fel, amely az `mystorageaccount`nevű Storage-fiók `my-file-system` fájlrendszerében található. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a `az storage blob directory upload` parancs használatával.

Ez a példa egy `upload.txt` nevű fájlt tölt fel egy `my-directory`nevű könyvtárba. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Ez a példa egy teljes könyvtárat tölt fel.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Fájl tulajdonságainak megjelenítése

A `az storage blob show` parancs használatával kinyomtathatja egy fájl tulajdonságait a konzolra.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Fájl átnevezése vagy áthelyezése

Átnevezheti vagy áthelyezhet egy fájlt a `az storage blob move` parancs használatával.

Ez a példa átnevez egy fájlt a név `my-file.txt` a név `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Fájl törlése

Töröljön egy fájlt a `az storage blob delete` parancs használatával.

Ez a példa töröl egy `my-file.txt` nevű fájlt.

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Engedélyek kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit.

### <a name="get-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek beolvasása

A `az storage blob directory access show` parancs használatával szerezze be a **CÍMTÁR** ACL-listáját.

Ez a példa egy könyvtár ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

A `az storage blob access show` parancs használatával szerezze be egy **fájl** hozzáférési engedélyeit. 

Ez a példa egy fájl ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Az alábbi képen egy könyvtár ACL-listájának beolvasása után a kimenet látható.

![ACL kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Ebben a példában a tulajdonos felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek megadása

A `az storage blob directory access set` parancs használatával állítsa be a **CÍMTÁR**ACL-listáját. 

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók könyvtárának ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

A `az storage blob access set` parancs használatával állítsa be egy **fájl**ACL-listáját. 

Ez a példa egy fájl ACL-fájlját állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Az alábbi képen egy fájl ACL-listájának beállítása után a kimenet látható.

![ACL kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Ebben a példában a tulajdonos felhasználó és a tulajdonos csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó írási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek frissítése

Ezt az engedélyt úgy is beállíthatja, hogy az `az storage blob directory access update` vagy `az storage blob access update` parancsot használja. 

Frissítse a címtár vagy fájl ACL-listáját úgy, hogy a `-permissions` paramétert egy ACL rövid formájára állítja be.

Ez a példa egy **könyvtár**ACL-listáját frissíti.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa egy **fájl**ACL-listáját frissíti.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Egy könyvtár vagy fájl tulajdonos felhasználóját és csoportját úgy is frissítheti, ha a `--owner` vagy `group` paramétereket a felhasználó entitás-azonosító vagy egyszerű felhasználónév (UPN) értékére állítja. 

Ez a példa egy könyvtár tulajdonosát módosítja. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa egy fájl tulajdonosát módosítja. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Felhasználó által definiált metaadatok kezelése

Felhasználó által definiált metaadatokat adhat hozzá egy fájlhoz vagy könyvtárhoz az `az storage blob directory metadata update` parancs használatával egy vagy több név-érték párokkal.

Ez a példa egy `my-directory` Directory nevű könyvtár felhasználó által definiált metaadatait adja meg.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Ebben a példában a `my-directory`nevű könyvtár összes felhasználó által definiált metaadata látható.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Lásd még:

* [Minta](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Forráskód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

