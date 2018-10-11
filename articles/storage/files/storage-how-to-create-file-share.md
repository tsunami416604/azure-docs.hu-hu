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
ms.openlocfilehash: ec952aa26d7bc6b185b425700080a4f474564b76
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955809"
---
# <a name="create-a-file-share-in-azure-files"></a>Fájlmegosztás létrehozása az Azure Filesban
Azure-fájlmegosztásokat létrehozhat az [Azure Portalon](https://portal.azure.com/), az Azure Storage PowerShell parancsmagjainak segítségével, illetve az Azure Storage ügyfélkódtáraival vagy az Azure Storage REST API-val. Az oktatóanyag során a következőket fogja elsajátítani:
* [Azure-fájlmegosztás létrehozása az Azure Portal használatával](#create-file-share-through-the-azure-portal)
* [Azure-fájlmegosztás létrehozása PowerShell használatával](#create-file-share-through-powershell)
* [Azure-fájlmegosztás létrehozása a parancssori felület használatával](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat meglévő tárfiókot, vagy [létrehozhat egy új Azure-tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha PowerShell-lel szeretne Azure-fájlmegosztást létrehozni, szüksége lesz a fiókkulcsra és a tárfiók nevére. PowerShell vagy parancssori felület használata esetén szüksége lesz a tárfiók kulcsára.

## <a name="create-file-share-through-the-azure-portal"></a>Fájlmegosztás létrehozása az Azure Portalról
1. **Ugorjon az Azure Portal Tárfiók paneljére**:    
    ![Tárfiók panel](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kattintson a Fájlmegosztás hozzáadása gombra**:    
    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Adja meg a nevet és a kvótát. A kvóta jelenleg legfeljebb 5 TiB lehet**:    
    ![Adjon meg egy nevet és egy kívánt kvótát az új fájlmegosztás számára](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Tekintse meg az új fájlmegosztást**: ![Az új fájlmegosztás megtekintése](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Töltsön fel egy fájlt**: ![Fájl feltöltése](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Tallózzon az új fájlmegosztásban, és kezelje könyvtárait és fájljait**: ![Fájlmegosztás tallózása](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Fájlmegosztás létrehozása PowerShell-lel
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) (Az Azure PowerShell telepítése és konfigurálása).

> [!Note]  
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.

1. **Hozzon létre egy környezetet a tárfiókhoz és a fiókkulcshoz** A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. A fiókkulcs az [Azure Portalról](https://portal.azure.com/) történő másolásához útmutatást a [tárfiókok hozzáférési kulcsaival](../common/storage-account-manage.md#access-keys) kapcsolatos témakörben találhat.

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Hozzon létre egy új fájlmegosztást**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="create-file-share-through-command-line-interface-cli"></a>Fájlmegosztás létrehozása parancssori felület (CLI) használatával
1. **A parancssori felület (CLI) használatának előkészítéseként töltse le és telepítse az Azure CLI-t.**  
    További információk: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Hozzon létre egy kapcsolati sztringet ahhoz a tárfiókhoz, amelyen létre szeretné hozni a megosztást.**  
    Az alábbi példában cserélje ki a ```<storage-account>``` és a ```<resource_group>``` elemet a Storage-fiók nevére és erőforráscsoportjára:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Fájlmegosztás létrehozása**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>További lépések
* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [GYIK](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)   
