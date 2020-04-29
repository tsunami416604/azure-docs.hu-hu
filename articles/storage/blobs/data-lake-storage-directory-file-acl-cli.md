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
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486134"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Könyvtárak, fájlok és ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2 (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet olyan könyvtárakat, fájlokat és engedélyeket a Storage-fiókokban, amelyek hierarchikus névteret használnak az [Azure parancssori felületének (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) használatával. 

> [!IMPORTANT]
> A `storage-preview` cikkben Kiemelt bővítmény jelenleg nyilvános előzetes verzióban érhető el.

[Sample](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [A Gen2-megfeleltetés](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | Gen1 mintája[visszajelzést ad](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Az Azure CLI `2.0.67` verziója vagy újabb.

## <a name="install-the-storage-cli-extension"></a>A Storage CLI bővítmény telepítése

1. Nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI `2.0.67` -verzió vagy magasabb-e.

   ```azurecli
    az --version
   ```
   Ha az Azure CLI verziója alacsonyabb, mint `2.0.67`a, telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

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

   Ellenkező esetben nyisson meg egy böngészőt [https://aka.ms/devicelogin](https://aka.ms/devicelogin) , és adja meg a terminálon megjelenő engedélyezési kódot. Ezután jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

   További információ a különböző hitelesítési módszerekről: Bejelentkezés az Azure CLI használatával.

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetés azonosítójával.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. A `az storage container create` parancs használatával létrehozhat egyet. 

Ez a példa egy nevű `my-file-system`fájlrendszert hoz létre.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a `az storage blob directory create` parancs használatával. 

Ez a példa egy nevű könyvtárat `my-directory` vesz fel egy nevű fájlrendszerbe, `my-file-system` amely egy nevű fiókban `mystorageaccount`található.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Könyvtár tulajdonságainak megjelenítése

A `az storage blob show` parancs használatával kinyomtathatja egy könyvtár tulajdonságait a konzolra.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `az storage blob directory move` parancs használatával.

Ez a példa átnevez egy könyvtárat a név `my-directory` alapján. `my-new-directory`

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a `az storage blob directory delete` parancs használatával.

Ez a példa törli a nevű `my-directory`könyvtárat. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Annak ellenőrzése, hogy létezik-e könyvtár

Annak megállapítása, hogy egy adott könyvtár létezik-e a fájlrendszerben a `az storage blob directory exist` parancs használatával.

Ez a példa azt mutatja, hogy `my-directory` létezik-e `my-file-system` egy nevű könyvtár a fájlrendszerben. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `az storage blob directory download` parancs használatával.

Ez a példa egy nevű könyvtárból tölt le `upload.txt` egy `my-directory`nevű fájlt. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Ez a példa egy teljes könyvtárat tölt le.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a `az storage blob directory list` parancs használatával.

Ez a példa egy nevű könyvtár tartalmát sorolja fel `my-directory` , amely egy nevű `my-file-system` `mystorageaccount`Storage-fiók fájlrendszerében található. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a `az storage blob directory upload` parancs használatával.

Ez a példa egy nevű fájlt tölt `upload.txt` fel egy nevű könyvtárba `my-directory`. 

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

Nevezze át vagy helyezze át a fájlt a `az storage blob move` parancs használatával.

Ez a példa átnevez egy fájlt a név `my-file.txt` alapján. `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Fájl törlése

Törölje a fájlt a `az storage blob delete` parancs használatával.

Ez a példa töröl egy nevű fájlt.`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Engedélyek kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit.

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a parancsokat, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört.

### <a name="get-directory-and-file-permissions"></a>Könyvtár-és fájlengedélyek beolvasása

A `az storage blob directory access show` parancs használatával szerezze be a **címtár** ACL-listáját.

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

A `az storage blob directory access set` parancs használatával állítsa be a **címtár**ACL-listáját. 

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók könyvtárának ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók könyvtárának *alapértelmezett* ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
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

Ezt az engedélyt úgy is beállíthatja, hogy a `az storage blob directory access update` vagy `az storage blob access update` a parancsot használja. 

Frissítse a címtár vagy fájl ACL-listáját úgy, hogy `-permissions` a paramétert egy ACL rövid formájára állítja be.

Ez a példa egy **könyvtár**ACL-listáját frissíti.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa egy **fájl**ACL-listáját frissíti.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Egy könyvtár vagy fájl tulajdonos felhasználóját és csoportját úgy is frissítheti, ha az `--owner` vagy `group` a paramétereket egy felhasználó entitás-azonosító vagy egyszerű felhasználónév (UPN) értékére állítja be. 

Ez a példa egy könyvtár tulajdonosát módosítja. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa egy fájl tulajdonosát módosítja. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Felhasználó által definiált metaadatok kezelése

Felhasználó által definiált metaadatokat adhat hozzá egy fájlhoz vagy könyvtárhoz a `az storage blob directory metadata update` parancs használatával egy vagy több név-érték párokkal.

Ez a példa egy címtár nevű `my-directory` könyvtár felhasználó által definiált metaadatait adja meg.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Ebben a példában a nevű `my-directory`könyvtár összes felhasználó által definiált metaadata látható.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Lásd még

* [Minta](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Forráskód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

