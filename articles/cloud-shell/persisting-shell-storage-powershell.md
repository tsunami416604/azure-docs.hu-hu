---
title: "PowerShell Azure Cloud rendszerhéj (előzetes verzió) lévő fájlok maradnak |} Microsoft Docs"
description: "A forgatókönyv a hogyan Azure Cloud rendszerhéj fájlokat továbbra is fennáll."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: 74488b85ec524e4ad4c06a639a16ddbfd54b3154
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud rendszerhéj (előzetes verzió) PowerShell működése
A felhő rendszerhéj (előzetes verzió) PowerShell továbbra is fennáll a fájlok a következő metóduson keresztül: 
* A megadott Azure-fájlmegosztáshoz, csatlakoztatni `clouddrive` a a `$Home` könyvtárhoz, a fájlmegosztás közvetlen interakció.

## <a name="list-cloud-drive-azure-file-shares"></a>Lista felhő meghajtó Azure fájlmegosztások
A `Get-CloudDrive` parancs a felhő rendszerhéj a felhő meghajtó által jelenleg csatlakoztatott Azure fájl megosztás adatainak beolvasása. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Felhőalapú Meghajtójukra leválasztása
Az Azure fájlmegosztások, hogy bármikor a felhő rendszerhéj csatlakoztatott is leválasztása. Az Azure fájlmegosztások el lett távolítva, kérni fogja hozhat létre, és egy új Azure fájlmegosztás csatlakoztatása a következő munkamenetben.

A `Dismount-CloudDrive` parancs az Azure fájlmegosztások leválasztja az aktuális tárfiókból. Az aktuális munkamenet leválasztása a felhőalapú Meghajtójukra leáll. A felhasználó kéri hozhat létre, és egy új Azure fájlmegosztás csatlakoztatása a következő munkamenet során.
![Running Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>További lépések
[Rövid útmutató a PowerShellhez](quickstart-powershell.md) <br>
[További tudnivalók az Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>