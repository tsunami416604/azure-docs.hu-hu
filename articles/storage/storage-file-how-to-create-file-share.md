---
title: "Azure-fájlmegosztás létrehozása | Microsoft Docs"
description: "Azure-fájlmegosztás létrehozása az Azure File Storage szolgáltatásban az Azure Portal, PowerShell és az Azure CLI használatával."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: bfe12fbdd50e82404ff49b1e34adc03913e50905
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Fájlmegosztás létrehozása az Azure File Storage-ban
Azure fájlmegosztásokat létrehozhat az [Azure Portalon](https://portal.azure.com/), az Azure Storage PowerShell parancsmagjainak segítségével, illetve az Azure Storage ügyfélkódtáraival vagy az Azure Storage REST API-val. Ez a oktatóanyagban az alábbiakat ismerheti meg:
* [Azure-fájlmegosztás létrehozása az Azure Portal használatával](#Create file share through the Portal)
* [Azure-fájlmegosztás létrehozása PowerShell használatával](#Create file share using PowerShell)
* [Azure-fájlmegosztás létrehozása parancssori felület használatával](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Előfeltételek
Azure-fájlmegosztás létrehozásához használhat már létező tárfiókot, vagy [létrehozhat egy új Azure-tárfiókot](storage-create-storage-account.md). Ha PowerShell-lel szeretne Azure-fájlmegosztást létrehozni, szüksége lesz a fiókkulcsra és a tárfiók nevére. PowerShell vagy parancssori felület használata esetén szüksége lesz a tárfiók kulcsára.

## <a name="create-file-share-through-the-portal"></a>Fájlmegosztás létrehozása a Portalon keresztül
1. **Ugorjon az Azure Portal Tárfiók paneljére**:    
    ![Tárfiók panel](media/storage-file-how-to-create-file-share/create-file-share-portal1.png)

2. **Kattintson a Fájlmegosztás hozzáadása gombra**:    
    ![Kattintson a fájlmegosztás hozzáadása gombra](media/storage-file-how-to-create-file-share/create-file-share-portal2.png)

3. **Adja meg a nevet és a kvótát. A kvóta jelenleg legfeljebb 5 TB lehet**:    
    ![Adjon meg egy nevet és egy kívánt kvótát az új fájlmegosztás számára](media/storage-file-how-to-create-file-share/create-file-share-portal3.png)

4. **Tekintse meg az új fájlmegosztást**: ![Az új fájlmegosztás megtekintése](media/storage-file-how-to-create-file-share/create-file-share-portal4.png)

5. **Töltsön fel egy fájlt**: ![Fájl feltöltése](media/storage-file-how-to-create-file-share/create-file-share-portal5.png)

6. **Tallózzon az új fájlmegosztásban, és kezelje könyvtárait és fájljait**: ![Fájlmegosztás tallózása](media/storage-file-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Fájlmegosztás létrehozása PowerShell-lel
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) (Az Azure PowerShell telepítése és konfigurálása).

> [!Note]  
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.

1. **Hozzon létre egy környezetet a tárfiókhoz és a fiókkulcshoz** A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. Útmutatás a fiókkulcs átmásolásához az [Azure Portalról](https://portal.azure.com/): [A tárelérési kulcs megtekintése és másolása](storage-create-storage-account.md#view-and-copy-storage-access-keys).

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
    Lásd: [Az Azure CLI 2.0-s verziójának telepítése](/cli/azure/install-az-cli2.md) és [Bevezetés az Azure CLI 2.0-s verziójának használatába](/cli/azure/get-started-with-azure-cli.md).

2. **Hozzon létre egy kapcsolati karakterláncot ahhoz a tárfiókhoz, amelyen létre szeretné hozni a megosztást.**  
    Az alábbi példában cserélje ki a ```<storage-account>``` és a ```<resource_group>``` elemet a tárfiók nevére és erőforráscsoportjára.

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

## <a name="next-steps"></a>Következő lépések
* [Fájlmegosztás csatlakoztatása – Windows](storage-file-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-file-how-to-use-files-mac.md)

Az alábbi hivatkozások további információkat tartalmaznak az Azure File Storage-ról.

* [Gyakori kérdések](storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-file-connection-problems.md)
