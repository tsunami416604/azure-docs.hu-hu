---
title: "Azure PowerShell-példaszkript – Blobok migrálása tárfiókok között az AzCopy Windows rendszeren történő használatával | Microsoft Docs"
description: "Egy Azure Storage-fiókban található blobok másolása egy másik fiókba az AzCopy használatával."
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Blobok migrálása tárfiókok között az AzCopy Windows rendszeren történő használatával

Ez a példaszkript minden blobobjektumot átmásol a felhasználó által megadott forrástárfiókból a felhasználó által megadott céltárfiókba. 

Ehhez a `Get-AzureStorageContainer` parancsot használja, amely felsorolja egy adott tárfiók összes tárolóját. A példa ezután AzCopy-parancsokat ad ki, amelyek minden tárolót átmásolnak a forrástárfiókból a céltárfiókba. Bármilyen hiba esetén a példa újrapróbálkozik $retryTimes alkalommal (az alapértelmezett érték 3, ami a `-RetryTimes` paraméterrel módosítható). Ha minden újrapróbálkozáskor hiba történik, a felhasználó újrafuttathatja a szkriptet úgy, hogy a `-LastSuccessContainerName` paraméterrel megadja a példa számára az utolsó sikeresen átmásolt tárolót. A példa ekkor ettől a ponttól folytatja a tárolók másolását.

Ehhez a példához az Azure PowerShell Storage moduljának **4.0.2**-es vagy újabb verziója szükséges. A saját telepített verzióját a `Get-Module -ListAvailable Azure.storage` paranccsal ellenőrizheti. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Emellett a példához a [Windowshoz készült AzCopy](http://aka.ms/downloadazcopy) legújabb verziója is szükséges. Az alapértelmezett telepítési könyvtár a következő: `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

A példának szüksége van a forrástárfiók nevére és kulcsára, a céltárfiók nevére és kulcsára, valamint az AzCopy.exe fájl teljes elérési útjára (ha nem az alapértelmezett könyvtárba van telepítve).

Néhány példa a minta bemeneteire:

Ha az AzCopy az alapértelmezett könyvtárba van telepítve:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Ha az AzCopy nem az alapértelmezett könyvtárba van telepítve:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Ha hiba történt, és a példát újra kell futtatni egy adott tárolótól kezdve: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja az adatok egyik tárfiókból a másikba másolásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Visszaadja az adott Storage-fiókhoz társított tárolókat. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Létrehoz egy Azure Storage-környezetet. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
