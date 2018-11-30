---
title: Azure-fájlmegosztás létrehozása | Microsoft Docs
description: Azure-fájlmegosztás létrehozása az Azure Files szolgáltatásban az Azure Portal, PowerShell és az Azure CLI használatával.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.component: files
ms.openlocfilehash: 83829264f16fb295a1f5fa4f2efc74d8b35ec6eb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309191"
---
# <a name="create-a-file-share-in-azure-files"></a>Fájlmegosztás létrehozása az Azure Filesban
Azure-fájlmegosztások használatával hozhat létre a [az Azure portal](https://portal.azure.com/), az Azure Storage PowerShell parancsmagjainak, az Azure Storage ügyfélkódtáraival vagy az Azure Storage REST API. Az oktatóanyag során a következőket fogja elsajátítani:
* [Azure-fájlmegosztás létrehozása az Azure Portal használatával](#create-file-share-through-the-azure-portal)
* [Azure-fájlmegosztás létrehozása PowerShell használatával](#create-file-share-through-powershell)
* [Hogyan hozhat létre Azure-fájlmegosztások a parancssori felületről](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat meglévő tárfiókot, vagy [létrehozhat egy új Azure-tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha PowerShell-lel szeretne Azure-fájlmegosztást létrehozni, szüksége lesz a fiókkulcsra és a tárfiók nevére. Ha azt tervezi, hogy a Powershell vagy a parancssori felület segítségével szüksége lesz a Tárfiók kulcsára.

## <a name="create-a-file-share-through-the-azure-portal"></a>Az Azure Portalon keresztül fájlmegosztás létrehozása
1. **Nyissa meg az Azure Portal Tárfiók paneljére**:    
    ![Tárfiók panel](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kattintson a Fájlmegosztás hozzáadása gombra**:    
    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Adja meg a nevet és a kvótát. A kvóta jelenlegi maximális érték pedig 5 TiB**:    
    ![Adjon meg egy nevet és egy kívánt kvótát az új fájlmegosztás számára](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Tekintse meg az új fájlmegosztást**: ![Az új fájlmegosztás megtekintése](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Töltsön fel egy fájlt**: ![Fájl feltöltése](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Tallózzon az új fájlmegosztásban, és kezelje könyvtárait és fájljait**: ![Fájlmegosztás tallózása](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Fájlmegosztás létrehozása PowerShell-lel
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) a telepítési pont és a telepítési útmutatást.

> [!Note]  
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.

1. **Hozzon létre egy környezetet a tárfiókhoz és a fiókkulcshoz** A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. Útmutatás a fiókkulcs átmásolásához az [az Azure portal](https://portal.azure.com/), lásd: [Tárfiók hozzáférési kulcsainak](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Hozzon létre egy új fájlmegosztást**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. Fájlmegosztásokat és fájlokat elnevezésével kapcsolatos részleteket lásd: [elnevezése és a hivatkozó megosztások, könyvtárak, fájlok és metaadatok](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Fájlmegosztás létrehozása parancssori felület (CLI) használatával
1. **A parancssori felület (CLI) használatának előkészítéseként töltse le és telepítse az Azure parancssori felület.**  
    Lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Hozzon létre egy kapcsolati sztringet ahhoz a tárfiókhoz, amelyen létre szeretné hozni a megosztást.**  
    Cserélje le ```<storage-account>``` és ```<resource_group>``` az a fiók nevére és erőforráscsoportjára tárolócsoportot az alábbi példában:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **A fájlmegosztás létrehozása**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>További lépések
* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)   
