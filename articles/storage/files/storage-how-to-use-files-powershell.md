---
title: "Az Azure Files kezelése a PowerShell-lel | Microsoft Docs"
description: "Itt megismerheti, hogyan kezelheti az Azure Files szolgáltatást a PowerShell használatával."
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
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>Az Azure Files kezelése a PowerShell-lel
Az Azure PowerShell szolgáltatást is használhatja fájlmegosztások létrehozására és kezelésére.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Az Azure Storage PowerShell-parancsmagjainak telepítése
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell telepítése és konfigurálása).

> [!NOTE]
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.
> 
> 

Kattintson a **Start** gombra, és írja be a **Windows PowerShell** kifejezést egy Azure PowerShell ablak megnyitásához. A PowerShell-ablak betölti az Azure PowerShell modult.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Környezet létrehozása a tárfiókhoz és a fiókkulcshoz
Hozza létre a tárfiók környezetét. A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. Útmutatás a fiókkulcs átmásolásához az [Azure Portalról](https://portal.azure.com): [A tárelérési kulcs megtekintése és másolása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

Az alábbi példában cserélje ki a `storage-account-name` és a `storage-account-key` elemet a tárfiók nevére és kulcsára.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Új fájlmegosztás létrehozása
Hozzon létre egy `logs` nevű új megosztást.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Így létrejött egy fájlmegosztás a fájltárolóban. A következő lépésben hozzá kell adnia egy könyvtárat és egy fájlt.

> [!IMPORTANT]
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Könyvtár létrehozása a fájlmegosztásban
Hozzon létre a megosztásban egy könyvtárat. Az alábbi példában szereplő könyvtár neve `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Helyi fájl feltöltése a könyvtárba
Töltsön fel egy helyi fájlt a könyvtárba. Az alábbi példa a következő helyről tölt fel egy fájlt: `C:\temp\Log1.txt`. A fájl elérési útját úgy szerkessze, hogy egy, a helyi gépen található érvényes fájlra mutasson.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>A könyvtárban található fájlok listázása
Ha látni szeretné a fájlt a könyvtárban, listázhatja a könyvtárban található összes fájlt. A parancs visszaadja a CustomLogs könyvtárban található összes fájlt és alkönyvtárt (ha van alkönyvtár).

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

A Get-AzureStorageFile parancs bármilyen átadott könyvtárobjektum fájljait és könyvtárait listázza. A „Get-AzureStorageFile -Share $s” parancs a gyökérkönyvtár fájljait és könyvtárait listázza. Ha egy alkönyvtár fájljait szeretné listázni, meg az alkönyvtárat kell megadnia a Get-AzureStorageFile parancsnak. Így a parancs függőleges vonalig tartó első része visszaadja a CustomLogs alkönyvtár egy könyvtárpéldányát, amelyet aztán átad a Get-AzureStorageFile parancsnak, ami visszaadja a CustomLogs könyvtárban található fájlok és könyvtárak listáját.

## <a name="copy-files"></a>Fájlok másolása
Az Azure PowerShell 0.9.7-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Alább bemutatjuk, hogyan hajthatja végre ezeket a másolási műveleteket a PowerShell-parancsmagokkal.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)    
