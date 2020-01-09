---
title: Azure-fájlmegosztás létrehozása
titleSuffix: Azure Files
description: Azure-fájlmegosztás létrehozása a Azure Portal, a PowerShell vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452877"
---
# <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
A Azure Files a [Azure Portal](https://portal.azure.com/), az Azure Storage PowerShell-parancsmagok, az Azure Storage ügyféloldali kódtárak vagy az azure Storage REST API használatával hozhatók létre. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:
- Azure-fájlmegosztás létrehozása a Azure Portal használatával
- Azure-fájlmegosztás létrehozása a PowerShell használatával
- Azure-fájlmegosztás létrehozása az Azure CLI használatával

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat olyan Storage-fiókot, amely már létezik, vagy [létrehozhat egy új Azure Storage-fiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Azure-fájlmegosztás PowerShell-lel való létrehozásához szüksége lesz a fiók kulcsára és a Storage-fiók nevére. Ha a PowerShell vagy a parancssori felület használatát tervezi, szüksége lesz egy Storage-fiók kulcsára.

> [!NOTE]
> Ha 5 TiB-nál nagyobb fájlmegosztást szeretne létrehozni, tekintse meg a [nagyméretű fájlmegosztás engedélyezése](storage-files-how-to-create-large-file-share.md)című témakört.

## <a name="create-a-file-share-through-the-azure-portal"></a>Fájlmegosztás létrehozása a Azure Portal

1. Lépjen a Azure Portal **Storage-fiók** paneljére.
    ![Storage-fiók ablaktábla](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Kattintson a **+ fájlmegosztás** gombra.
    ![a fájlmegosztás hozzáadása gomb](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Adja meg a **név** és a **kvóta**adatait.
    ![az új fájlmegosztás nevét és kvótáját](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Tekintse meg az új fájlmegosztást.
    ![az új fájlmegosztást a felhasználói felületen](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Töltsön fel egy fájlt.
    ![a navigációs sávon a feltöltés gombot](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Tallózással keresse meg a fájlmegosztást, és kezelje a címtárakat és a fájlokat.
    a fájlmegosztás ![mappa nézete](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Fájlmegosztás létrehozása a PowerShell-lel

Töltse le és telepítse az Azure PowerShell-parancsmagokat. Lásd: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/overview) a telepítési ponthoz és a telepítési utasításokhoz.

> [!Note]  
> Javasoljuk, hogy töltse le és telepítse, vagy frissítsen a verzióra a legújabb Azure PowerShell modulra.

1. Hozzon létre egy új tárfiókot.
    A Storage-fiók egy megosztott tároló, amely az Azure-fájlmegosztás és-tárolási erőforrások, például Blobok vagy várólisták üzembe helyezésére használható.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Hozzon létre egy új fájlmegosztást.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Fájlmegosztás létrehozása a parancssori felületről

1. Töltse le és telepítse az Azure CLI-t.
    Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [az első lépések az Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Hozzon létre egy kapcsolódási karakterláncot arra a Storage-fiókra, amelyben létre szeretné hozni a megosztást.
    Cserélje le ```<storage-account>``` és ```<resource_group>``` a Storage-fiók nevére és az erőforráscsoporthoz a következő példában:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Hozza létre a fájlmegosztást.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Következő lépések

* [Fájlmegosztás csatlakoztatása és csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Tárolási fájlok – gyakori kérdések](storage-files-faq.md)
* [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)
