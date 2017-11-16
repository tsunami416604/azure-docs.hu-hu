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
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 66d0e20d670e49cdfe64614d1fc6f5739fde6155
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud rendszerhéj (előzetes verzió) PowerShell működése
A felhő rendszerhéj (előzetes verzió) PowerShell továbbra is fennáll a fájlok a következő metóduson keresztül: 
* A megadott fájlmegosztással csatlakoztatása `clouddrive` a a `$Home` könyvtárhoz, a fájlmegosztás közvetlen interakció.

## <a name="list-cloud-drive-file-shares"></a>Lista felhőalapú Meghajtójukra fájlmegosztások
A `Get-CloudDrive` parancs a felhő rendszerhéj a felhő meghajtó által jelenleg csatlakoztatott fájl megosztás adatainak beolvasása. <br>
![Get-CloudDrive fut](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Felhőalapú Meghajtójukra leválasztása
Egy fájlmegosztást, hogy bármikor a felhő rendszerhéj csatlakoztatott is leválasztása. Ha a fájlmegosztás el lett távolítva, kérni fogja hozhat létre, és a következő munkamenet egy új fájlmegosztást csatlakoztatni.

A `Dismount-CloudDrive` parancs fájlmegosztás leválasztja az aktuális tárfiókból. Az aktuális munkamenet leválasztása a felhőalapú Meghajtójukra leáll. A felhasználó kéri hozhat létre, és az Új fájlmegosztás csatlakoztatása a következő munkamenet során.
![Dismount-CloudDrive fut](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Következő lépések
[Rövid útmutató a PowerShellhez](quickstart-powershell.md) <br>
[További tudnivalók az Azure File storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>