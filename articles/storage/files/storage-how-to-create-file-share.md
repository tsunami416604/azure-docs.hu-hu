---
title: Azure-fájlmegosztás létrehozása | Microsoft Docs
description: Azure-fájlmegosztás létrehozása az Azure Files szolgáltatásban az Azure Portal, PowerShell és az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3b30df7410f56c203eb600089cce130a8de23d1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514607"
---
# <a name="create-a-file-share-in-azure-files"></a>Fájlmegosztás létrehozása az Azure Filesban
Azure-fájlmegosztás a [Azure Portal](https://portal.azure.com/), az Azure Storage PowerShell-parancsmagok, az Azure Storage ügyféloldali kódtárak vagy az azure Storage REST API használatával hozható létre. Az oktatóanyag során a következőket fogja elsajátítani:
* Azure-fájlmegosztás létrehozása a Azure Portal használatával
* [Azure-fájlmegosztás létrehozása PowerShell használatával](#create-file-share-through-powershell)
* [Azure-fájlmegosztás létrehozása a parancssori felület használatával](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat meglévő tárfiókot, vagy [létrehozhat egy új Azure-tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha PowerShell-lel szeretne Azure-fájlmegosztást létrehozni, szüksége lesz a fiókkulcsra és a tárfiók nevére. Ha a PowerShell vagy a parancssori felület használatát tervezi, szüksége lesz egy Storage-fiók kulcsára.

> [!NOTE]
> Ha 5 TiB-nál nagyobb fájlmegosztást szeretne létrehozni, tekintse meg a [nagyméretű fájlmegosztás engedélyezése](storage-files-how-to-create-large-file-share.md)című cikket.

## <a name="create-a-file-share-through-the-azure-portal"></a>Fájlmegosztás létrehozása a Azure Portal
1. **Lépjen a Azure Portal Storage-fiók**paneljére:    
    ![Tárfiók panel](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kattintson a Fájlmegosztás hozzáadása gombra**:    
    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Adja meg a nevet és a kvótát**:    
    ![Adjon meg egy nevet és egy kívánt kvótát az új fájlmegosztás számára](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Tekintse meg az új fájlmegosztást**: ![Az új fájlmegosztás megtekintése](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Töltsön fel egy fájlt**: ![Fájl feltöltése](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Tallózzon az új fájlmegosztásban, és kezelje könyvtárait és fájljait**: ![Fájlmegosztás tallózása](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Fájlmegosztás létrehozása PowerShell-lel
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. Lásd: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/overview)  for a telepítési pont és a telepítési utasítások.

> [!Note]  
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.

1. **Hozzon létre egy új Storage-fiókot:** A Storage-fiók olyan tárolók közös készlete, amelyekben az Azure-fájlmegosztás, valamint más tárolási erőforrások, például Blobok vagy várólisták helyezhetők üzembe.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. **Hozzon létre egy új fájlmegosztást**:    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Fájlmegosztás létrehozása parancssori felület (CLI) használatával
1. **A parancssori felület (CLI) használatának előkészítéseként töltse le és telepítse az Azure CLI-t.**  
    Lásd: [Az Azure CLI telepítése és az](https://docs.microsoft.com/cli/azure/install-azure-cli) [Azure CLI első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Hozzon létre egy kapcsolati sztringet ahhoz a tárfiókhoz, amelyen létre szeretné hozni a megosztást.**  
    Cserélje le a  ```<storage-account>```  and  ```<resource_group>```  with a Storage-fiók nevét és az erőforráscsoportot a következő példában:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **A fájlmegosztás létrehozása**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Következő lépések
* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [GYIK](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)   
