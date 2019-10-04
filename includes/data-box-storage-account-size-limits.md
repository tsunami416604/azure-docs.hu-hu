---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277268"
---
Itt az adatok tárfiókba történő másolt mérete korlátok vonatkoznak. Győződjön meg arról, hogy a feltöltött adatok megfelel-e ezeket a korlátokat. Ezek a korlátok a legfrissebb információkért nyissa meg [az Azure blob storage méretezési célokat](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) és [Azure Files tárolók skálázása](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Az Azure storage-fiókra másolni adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| A blokkblobok és lapblobok                                            | 2 PB Egyesült Államok és Európa.<br>500 TB-os más régiókban, amely tartalmazza az Egyesült Királyság.  <br> Ez magában foglalja többek között a Data Box forrásokból származó adatok.|
| Azure Files                                                          | Az egy 5 TB-os.<br> Az összes mappát *StorageAccount_AzureFiles* hajtsa végre ezt a korlátot.       |
