---
title: Java SDK Azure Data Lake Storage Gen2 fájlokhoz & ACL-ek
description: A Javához készült Azure Storage-kódtárak segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108127"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A Java segítségével kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet a Java használatával könyvtárakat, fájlokat és engedélyeket olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referenciák](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](../common/storage-account-create.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként nyissa meg [ezt a lapot](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) , és keresse meg a Java-könyvtár legújabb verzióját. Ezután nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adjon hozzá egy függőségi elemet, amely hivatkozik erre a verzióra.

Ha az ügyfélalkalmazás Azure Active Directory (AD) használatával történő hitelesítését tervezi, akkor vegyen fel egy függőséget az Azure Secret ügyféloldali függvénytárba. Lásd:  [a titkos ügyféloldali kódtár csomag hozzáadása a projekthez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ezután adja hozzá ezeket az importálási utasításokat a programkódhoz.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz 

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [Javához készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> További példákért tekintse meg a Java-dokumentációhoz készült [Azure Identity ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .


## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A **DataLakeServiceClient. createFileSystem** metódus meghívásával létrehozhat egyet.

Ez a példa egy nevű tárolót hoz létre `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a **DataLakeFileSystemClient. createDirectory** metódus meghívásával.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz, majd hozzáadja a nevű alkönyvtárat `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DataLakeDirectoryClient. Rename** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-subdirectory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DataLakeDirectoryClient. deleteWithResponse** metódus meghívásával törölje a könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient. Append** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **DataLakeFileClient. FlushAsync** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Ha a fájl mérete nagy, a kódnak több hívást is el kell végeznie a **DataLakeFileClient. Append** metódushoz. Használja helyette a **DataLakeFileClient. uploadFromFile** metódust. Így feltöltheti a teljes fájlt egyetlen hívással. 
>
> Tekintse meg a következő szakaszt egy példához.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyméretű fájl feltöltése egy könyvtárba

A **DataLakeFileClient. uploadFromFile** metódus használatával nagyméretű fájlokat tölthet fel anélkül, hogy több hívást kellene tennie a **DataLakeFileClient. Append** metódushoz.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Először hozzon létre egy **DataLakeFileClient** -példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **DataLakeFileClient. Read** metódust. A .NET-fájlok feldolgozására szolgáló API-k használatával az adatfolyamból fájlba mentheti a bájtokat. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Ez a példa kinyomtatja a nevű könyvtárban található egyes fájlok nevét `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Hozzáférés-vezérlési listák (ACL-ek) kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit.

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](./data-lake-storage-access-control.md)ismertető témakört.

### <a name="manage-a-directory-acl"></a>Címtár-ACL kezelése

Ez a példa lekéri, majd beállítja a nevű könyvtár ACL-listáját `my-directory` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](./data-lake-storage-access-control.md)ismertető témakört.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Lekérheti és beállíthatja egy tároló gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez adjon át egy üres karakterláncot ( `""` ) a **DataLakeFileSystemClient. getDirectoryClient** metódusba.

### <a name="manage-a-file-acl"></a>Fájl hozzáférés-vezérlési listájának kezelése

Ez a példa lekéri, majd beállítja a nevű fájl ACL-listáját `upload-file.txt` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](./data-lake-storage-access-control.md)ismertető témakört.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>ACL beállítása rekurzív módon

Az ACL-eket a szülő könyvtár meglévő alárendelt elemein is hozzáadhatja, frissítheti és eltávolíthatja anélkül, hogy ezeket a módosításokat egyenként el kellene végeznie az egyes alárendelt elemek esetében. További információ: [rekurzív hozzáférés-vezérlési listák (ACL-ek) beállítása Azure Data Lake Storage Gen2hoz](recursive-access-control-lists.md).

## <a name="see-also"></a>Lásd még

* [API-referenciadokumentáció](/java/api/overview/azure/storage-file-datalake-readme)
* [Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)