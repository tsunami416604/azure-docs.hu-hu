---
title: Az Azure CLI használata fájlokhoz & ACL-ekhez az Azure Data Lake Storage Gen2 szolgáltatásban (előzetes verzió)
description: Az Azure CLI használatával kezelheti a könyvtárakat és a fájl- és címtárhozzáférés-vezérlési listákat (ACL) a hierarchikus névtérrel rendelkező tárfiókokban.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486134"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Az Azure CLI használatával kezelheti a könyvtárakat, a fájlokat és az ACL-eket az Azure Data Lake Storage Gen2 szolgáltatásban (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja az [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) létrehozása és kezelése könyvtárak, fájlok és engedélyek tárfiókok, amelyek hierarchikus névtérrel. 

> [!IMPORTANT]
> A `storage-preview` cikkben szereplő bővítmény jelenleg nyilvános előzetes verzióban érhető el.

[Minta](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 a Gen2 feltérképezése](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.
> * Azure CLI-verzió `2.0.67` vagy újabb verzió.

## <a name="install-the-storage-cli-extension"></a>A tároló CLI-bővítmény telepítése

1. Nyissa meg az [Azure Cloud Shellt,](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI-t, nyisson meg egy parancskonzolalkalmazást, például a Windows PowerShellt.

2. Ellenőrizze, hogy az Azure CLI `2.0.67` telepített verziója a következő paranccsal vagy annál magasabb.

   ```azurecli
    az --version
   ```
   Ha az Azure CLI verziója `2.0.67`alacsonyabb, mint a , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

3. A(z) `storage-preview` bővítmény telepítése.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz

1. Ha az Azure CLI helyileg használja, futtassa a bejelentkezési parancsot.

   ```azurecli
   az login
   ```

   Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt megteszi, és betölt egy Azure-bejelentkezési lapot.

   Ellenkező esetben nyisson [https://aka.ms/devicelogin](https://aka.ms/devicelogin) meg egy böngészőlapot a terminálon, és adja meg az engedélyezési kódot. Ezután jelentkezzen be a fiók hitelesítő adataival a böngészőben.

   További információ a különböző hitelesítési módszerekről: Bejelentkezés az Azure CLI használatával.

2. Ha az identitás egynél több előfizetéshez van társítva, állítsa be az aktív előfizetést a statikus webhelyet tároló tárfiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje `<subscription-id>` le a helyőrző értékét az előfizetés azonosítójára.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. A `az storage container create` parancs segítségével létrehozhat egyet. 

Ez a példa létrehoz `my-file-system`egy fájlrendszert.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtárhivatkozás létrehozása a `az storage blob directory create` paranccsal. 

Ez a példa `my-directory` egy névvel `my-file-system` ellátott könyvtárat ad `mystorageaccount`hozzá egy fájlrendszerhez, amely egy fiókban található.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Könyvtártulajdonságok megjelenítése

A könyvtár tulajdonságait a parancs segítségével nyomtathatja `az storage blob show` ki a konzolra.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a `az storage blob directory move` paranccsal.

Ez a példa átnevez egy `my-directory` könyvtárat `my-new-directory`a névről a névre.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Könyvtár törlése a `az storage blob directory delete` paranccsal.

Ez a példa törli `my-directory`a . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Annak ellenőrzése, hogy létezik-e könyvtár

A `az storage blob directory exist` parancs segítségével állapítsa meg, hogy létezik-e adott könyvtár a fájlrendszerben.

Ez a példa azt `my-directory` mutatja be, hogy létezik-e a fájlrendszerben megnevezett `my-file-system` könyvtár. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Letöltés könyvtárból

Töltse le a fájlt egy `az storage blob directory download` könyvtárból a parancs segítségével.

Ez a példa a `upload.txt` nevű könyvtárból letölti a nevű `my-directory`fájlt. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Ez a példa egy teljes könyvtárat tölt le.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázása a `az storage blob directory list` paranccsal.

Ez a példa egy könyvtár `my-directory` tartalmát sorolja `my-file-system` fel, amely egy `mystorageaccount`tárolófiók fájlrendszerében található. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltsön fel egy fájlt `az storage blob directory upload` egy könyvtárba a paranccsal.

Ez a példa feltölt `upload.txt` egy nevű `my-directory`fájlt a nevesített könyvtárba. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Ez a példa egy teljes könyvtárat tölt fel.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Fájltulajdonságok megjelenítése

A `az storage blob show` parancs segítségével kinyomtathatja a fájl tulajdonságait a konzolra.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Fájl átnevezése vagy áthelyezése

Fájl átnevezése vagy áthelyezése a `az storage blob move` paranccsal.

Ez a példa átnevez egy `my-file.txt` fájlt `my-file-renamed.txt`a névről a névre.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Fájl törlése

Fájl törlése a `az storage blob delete` paranccsal.

Ez a példa töröl egy nevű fájlt`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Engedélyek kezelése

Beszerezheti, beállíthatja és frissítheti a könyvtárak és fájlok hozzáférési engedélyeit.

> [!NOTE]
> Ha az Azure Active Directory (Azure AD) parancsok engedélyezéséhez használja, győződjön meg arról, hogy a rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-directory-and-file-permissions"></a>Könyvtár- és fájlengedélyek beszereznie

A **címtár** ACL-jének `az storage blob directory access show` beszereznie a parancs segítségével.

Ez a példa leveszi egy könyvtár ACL-ét, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

A `az storage blob access show` parancs segítségével kaphatja meg egy **fájl** hozzáférési engedélyeit. 

Ez a példa leveszi egy fájl ACL-ét, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Az alábbi képen látható a kimenet egy könyvtár ACL-jének beszerzése után.

![ACL kimenet beszereznie](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonában lévő csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listákról további információt az [Azure Data Lake Storage Gen2 hozzáférés-vezérlése című témakörben](data-lake-storage-access-control.md)talál.

### <a name="set-directory-and-file-permissions"></a>Könyvtár- és fájlengedélyek beállítása

A `az storage blob directory access set` parancs segítségével állítsa be a könyvtár ACL-t . **directory** 

Ez a példa beállítja az ACL-t a tulajdonában lévő felhasználó, a tulajdonában lévő csoport vagy más felhasználók könyvtárában, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa beállítja az *alapértelmezett* ACL-t a tulajdonában lévő felhasználó, a tulajdonában lévő csoport vagy más felhasználók könyvtárában, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

A `az storage blob access set` parancs segítségével állítsa be a **fájl**acl. 

Ez a példa beállítja az ACL-t egy fájlon a tulajdonában lévő felhasználó, a tulajdonában lévő csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Az alábbi képen a kimenet látható a fájl ACL-jének beállítása után.

![ACL kimenet beszereznie](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Ebben a példában a tulajdonában lévő felhasználó és a tulajdonában lévő csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó rendelkezik írási és végrehajtási engedéllyel. A hozzáférés-vezérlési listákról további információt az [Azure Data Lake Storage Gen2 hozzáférés-vezérlése című témakörben](data-lake-storage-access-control.md)talál.

### <a name="update-directory-and-file-permissions"></a>Könyvtár- és fájlengedélyek frissítése

Az engedély beállításának másik módja `az storage blob directory access update` `az storage blob access update` a vagy a parancs használata. 

A címtár vagy fájl ACL-jének frissítése az `-permissions` ACL rövid formájára állításával.

Ez a példa frissíti egy **könyvtár**ACL-ét .

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa frissíti egy **fájl**ACL-ét .

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

A címtár vagy fájl tulajdonában lévő felhasználót és `--owner` `group` csoportot úgy is frissítheti, hogy a felhasználó entitásazonosítójára vagy egyszerű felhasználónevére (UPN) állítja be a paramétereket. 

Ez a példa megváltoztatja a könyvtár tulajdonosát. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Ez a példa megváltoztatja a fájl tulajdonosát. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Felhasználó által definiált metaadatok kezelése

A felhasználó által definiált metaadatokat hozzáadhatja egy `az storage blob directory metadata update` fájlhoz vagy könyvtárhoz, ha a parancsot egy vagy több név-érték párral használja.

Ez a példa egy könyvtár nevű `my-directory` könyvtár felhasználó által definiált metaadatait adja hozzá.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Ez a példa a felhasználó által definiált `my-directory`könyvtár összes felhasználó által definiált metaadatát mutatja.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Lásd még

* [Minta](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 –Gen2 leképezés](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Forráskód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

