---
title: PowerShell Azure Cloud rendszerhéj (előzetes verzió) lévő fájlok maradnak |} Microsoft Docs
description: A forgatókönyv a hogyan Azure Cloud rendszerhéj fájlokat továbbra is fennáll.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud rendszerhéj (előzetes verzió) PowerShell működése
A felhő rendszerhéj (előzetes verzió) PowerShell továbbra is fennáll a fájlok a következő metóduson keresztül: 
* A megadott Azure-fájlmegosztáshoz, csatlakoztatni `clouddrive` a a `$Home` könyvtárhoz, a fájlmegosztás közvetlen interakció.

## <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure fájlmegosztásokat
A `Get-CloudDrive` parancs segítségével lekérdezhető az Azure fájl megosztási információk által aktuálisan csatlakoztatva a `clouddrive` a felhő rendszerhéj. <br>
![Get-CloudDrive fut](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Válassza le a lemezképet `clouddrive`
Az Azure fájlmegosztások, hogy bármikor a felhő rendszerhéj csatlakoztatott is leválasztása. Az Azure fájlmegosztások el lett távolítva, kérni fogja hozhat létre, és egy új Azure fájlmegosztás csatlakoztatása a következő munkamenetben.

A `Dismount-CloudDrive` parancs az Azure fájlmegosztások leválasztja az aktuális tárfiókból. Leválasztás a `clouddrive` megszakítja az aktuális munkamenet. A felhasználó kéri hozhat létre, és egy új Azure fájlmegosztás csatlakoztatása a következő munkamenet során.
![Dismount-CloudDrive fut](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>További lépések
[Rövid útmutató a PowerShellhez](quickstart-powershell.md) <br>
[További tudnivalók az Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>