---
title: Azure-fájlmegosztás létrehozása | Microsoft Docs
description: Azure-fájlmegosztás létrehozása az Azure Files szolgáltatásban az Azure Portal, PowerShell és az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e56be394bc7667dfca9a0b417593e8e587073712
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699648"
---
# <a name="create-a-file-share-in-azure-files"></a>Fájlmegosztás létrehozása az Azure Filesban
Azure-fájlmegosztás a [Azure Portal](https://portal.azure.com/), az Azure Storage PowerShell-parancsmagok, az Azure Storage ügyféloldali kódtárak vagy az azure Storage REST API használatával hozható létre. Az oktatóanyag során a következőket fogja elsajátítani:
* Azure-fájlmegosztás létrehozása a Azure Portal használatával
* [Azure-fájlmegosztás létrehozása PowerShell használatával](#create-file-share-through-powershell)
* [Azure-fájlmegosztás létrehozása a parancssori felület használatával](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat meglévő tárfiókot, vagy [létrehozhat egy új Azure-tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha PowerShell-lel szeretne Azure-fájlmegosztást létrehozni, szüksége lesz a fiókkulcsra és a tárfiók nevére. Ha a PowerShell vagy a parancssori felület használatát tervezi, szüksége lesz egy Storage-fiók kulcsára.

## <a name="create-a-file-share-through-the-azure-portal"></a>Fájlmegosztás létrehozása a Azure Portal
1. **Lépjen a Azure Portal Storage-fiók**paneljére:    
    ![Tárfiók panel](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kattintson a Fájlmegosztás hozzáadása gombra**:    
    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Adja meg a nevet és a kvótát. A kvóta jelenlegi maximális értéke 5 TiB**:    
    ![Adjon meg egy nevet és egy kívánt kvótát az új fájlmegosztás számára](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Tekintse meg az új fájlmegosztást**:  ![Az új fájlmegosztás megtekintése](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Fájl feltöltése**:  ![Fájl feltöltése](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Tallózással keresse meg a fájlmegosztást, és kezelje a címtárakat és a fájlokat**:  ![Fájlmegosztás tallózása](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Fájlmegosztás létrehozása PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. Lásd:  [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)a telepítési ponthoz és a telepítési utasításokhoz.

> [!Note]  
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.

1. **Hozzon létre egy környezetet a tárfiókhoz és a fiókkulcshoz** A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. A fiók kulcsának a Azure Portalból történő másolásával kapcsolatos utasításokért lásd: [Storage-fiók elérési kulcsa](../common/storage-account-manage.md#access-keys). [](https://portal.azure.com/)

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Hozzon létre egy új fájlmegosztást**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Fájlmegosztás létrehozása parancssori felület (CLI) használatával
1. **A parancssori felület (CLI) használatának előkészítéseként töltse le és telepítse az Azure CLI-t.**  
    Lásd: [Az Azure CLI telepítése és az](https://docs.microsoft.com/cli/azure/install-azure-cli) [Azure CLI első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Hozzon létre egy kapcsolati sztringet ahhoz a tárfiókhoz, amelyen létre szeretné hozni a megosztást.**  
    Cserélje ```<storage-account>```le aés ```<resource_group>```aneveta Storage-fiók nevére és erőforráscsoporthoz a következő példában: 

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

## <a name="next-steps"></a>További lépések
* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)   
